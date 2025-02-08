## NUI on K8S (Experimental)

This repo manage the K8s deploy of NUI and publishes the related helm chart.
Current version of NUI on K8s deploys:
- a statefulset with NUI application
- related service
- optional ingress
- optional secret to add .creds files into the pod and use the nats credential auth

## Getting started
```
helm repo add nats-nui https://nats-nui.github.io/k8s/helm/charts
helm install nats-nui nats-nui/nui
```

## Customization
Check `values.yaml` to enable ingress and customize the install

### cli context secrets

To autoload CLI contexts into your NUI deployment, you can create a Kubernetes Secret containing the context files. Here is an example of how to create a Secret with a NATS CLI context:

1. Create a JSON file with the NATS CLI context:

    ```json
    {
      "name": "demo",
      "url": "nats://demo.nats.io:4222"
    }
    ```

2. Create a Kubernetes Secret using the JSON file:

    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: secret-cli-contexts-volume
    type: Opaque
    stringData:
      context1.json: |
        {
          "name": "demo",
          "url": "nats://demo.nats.io:4222"
        }
    ```

3. Update your `values.yaml` to reference the new Secret:

    ```yaml
    autoloadContexts: true
    contextsVolume:
      name: secret-cli-contexts-volume # Name of the Kubernetes Secret

      # Add more files as needed. All the creds added are mapped into the /cli-contexts of the container
      # and they will be loaded at the container start if the autoLoadContexts is set to true
      items:
        - key: context1
          path: context1.json
    ```

This setup will ensure that the NATS CLI context pointing to `demo.nats.io` is loaded into your container automatically.