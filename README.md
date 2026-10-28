# Lab 2 — hello-app on Kubernetes

Your copy of this repository is your submission. The lab sheet is on the course site; this file is where you record what you did.

What's here:

| File | What it is |
|---|---|
| `src/`, `index.html` | a small React page that shows which Pod answered |
| `nginx.conf` | serves the page, and answers `/whoami` with the Pod name |
| `Dockerfile` | builds the page, then serves it with nginx on port 80 |

You don't change any of these. You add `hello-app.yaml` and fill in the sections below.

## Before you push, check

- [ ] `hello-app.yaml` is in the root of this repository
- [ ] `screenshot.png` is in the root of this repository
- [ ] every section below is filled in

---

## 1. My image

Docker Hub image I deployed (for example `ravi123/hello-app:v1`):

```
tejaswinis123/hello-app:v1
```

## 2. Pods across nodes

Output of `kubectl get pods -o wide` after you applied `hello-app.yaml`:

```
NAME                         READY   STATUS    RESTARTS      AGE   IP          NODE             NOMINATED NODE   READINESS GATES
hello-app-6d5df7757b-9b6k2   1/1     Running   0             22m   10.1.0.35   docker-desktop   <none>           <none>
hello-app-6d5df7757b-kqs5d   1/1     Running   0             32m   10.1.0.32   docker-desktop   <none>           <none>
hello-app-6d5df7757b-nbr5t   1/1     Running   0             32m   10.1.0.33   docker-desktop   <none>           <none>
hello-app-6d5df7757b-rt59g   1/1     Running   0             32m   10.1.0.31   docker-desktop   <none>           <none>
```

## 3. The Service and its endpoints

Output of `kubectl get service hello-app` and `kubectl get endpointslices -l kubernetes.io/service-name=hello-app`:

```

kubectl get service hello-app
NAME        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-app   LoadBalancer   10.107.171.82   localhost     8080:31718/TCP   39m

kubectl get endpointslices -l kubernetes.io/service-name=hello-app
NAME              ADDRESSTYPE   PORTS   ENDPOINTS                                   AGE
hello-app-4dhm2   IPv4          80      10.1.0.33,10.1.0.35,10.1.0.32 + 1 more...   39m
```

## 4. Browser

Add `screenshot.png`: the page at `http://localhost:8080` with at least two Pods in the "Answers so far" table.
![Hello app running on kubernetes](<screenshot.png>)



## 5. The four traps

For each trap, write what you saw and why it happened, in two or three lines of your own words.

**Trap 1 — the local image.**
An image available on my local machine is not necessarily available to Kubernetes. Using the correct image published to Docker Hub allows Kubernetes to pull it when needed.

**Trap 2 — the selector typo.**
When I changed the Service selector so that it did not match the Pod labels, the Service could not find the application Pods. Correcting the selector restored the matching endpoints.
**Trap 3 — the wrong targetPort.**
When I changed targetPort from 80 to 81, the Service forwarded traffic to the wrong container port. Restoring targetPort: 80 allowed traffic to reach nginx.
**Trap 4 — the EXTERNAL-IP address.**
The LoadBalancer Service showed localhost as its external address in Docker Desktop Kubernetes. I accessed the application using http://localhost:8080 rather than expecting a public IP address

## 6. Your YAML vs the generated YAML
Two fields that may appear in generated YAML but are not required in my handwritten manifest are creationTimestamp and status. These fields are normally managed by Kubernetes, so I do not need to specify them for the Deployment and Service to work.

I checked the generated YAML to compare these fields with my own manifest.

Name two fields in the `--dry-run=client -o yaml` output that were not in your file. Why does your file still work without them?
Two fields are creationTimestamp and status.
- creationTimestamp: Kubernetes automatically records when a resource was created.
- status: Kubernetes manages this field to report the current state of the resource.

Why does the file still work without them?

These fields are managed by Kubernetes and do not need to be specified in our YAML file. Kubernetes creates the resource using the required configuration fields and updates its status automatically.