# Swimlane Flask Docker

Simple flask app to allow for interaction with Swimlane.

# Installation

First you'll need to create and retrieve secrets that will be used by the Flask deployment.

Create a secret for the Personal Access Token:
```bash
kubectl create secret generic swimlane-flask-pat --from-literal=pat=<pat> -n <namespace>
```

This deployment assumes that it will be deployed to a namespace with an existing Swimlane deployment. Retreive the existing ImagePullSecret:
```bash
kubectl get secret -n <namespace> --field-selector type=kubernetes.io/dockerconfigjson
```

If necessary, update the ImagePullSecret in the `flask.yaml` with the correct name:
```yaml
      imagePullSecrets:
      - name: swimlane-swimlane
```

Lastly, the default ingress class in the `flask.yaml` is traefik. Be sure to change it if you're not using Traefik as your Ingress Controller:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: flask-ingress
  annotations:
    kubernetes.io/ingress.class: <another-ingress-class>
    ingress.kubernetes.io/protocol: https
```

Apply the Flask YAML
```bash
kubectl apply -f flask.yaml -n <namespace>
```
