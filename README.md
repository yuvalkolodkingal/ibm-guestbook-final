# IBM Containers Final Project - Guestbook

Coursera: Introduction to Containers w/ Docker, Kubernetes & OpenShift

## setup

```bash
export MY_NAMESPACE=sn-labs-YOURUSERNAME   # change this!!
cd v1/guestbook
```

## task 1-3: build v1 and deploy

```bash
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1
ibmcloud cr login
ibmcloud cr region-set us-south
docker push us.icr.io/$MY_NAMESPACE/guestbook:v1
ibmcloud cr images

# edit deployment.yml with your namespace first
kubectl apply -f deployment.yml
kubectl port-forward deployment/guestbook 3000:3000
```

open http://localhost:3000 - should say Guestbook - v1

## task 4-5: HPA

```bash
kubectl autoscale deployment guestbook --cpu-percent=5 --min=1 --max=10
kubectl get hpa guestbook

# generate load (use your openshift route url)
kubectl run -i --tty load-generator --rm --image=busybox:1.36.0 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://localhost:3000/; done"

kubectl get hpa guestbook --watch
```

## task 6-8: rolling update to v2

```bash
cp public/index-v2.html public/index.html
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v2
docker push us.icr.io/$MY_NAMESPACE/guestbook:v2
kubectl apply -f deployment-v2.yml
kubectl rollout status deployment/guestbook
```

## task 9-10: rollback

```bash
kubectl rollout history deployment/guestbook
kubectl rollout undo deployment/guestbook
kubectl get rs
```
