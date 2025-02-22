# Notes on preparations

Label the k8s node with the sonoff antenna so that we can use node affinity to attach home assistant to the same node

  kubectl label nodes k8s101.k8s app=sonoff-controller

Then use affinity

  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: app
            operator: In
            values:
            - sonoff-controller

# Ensure your have provided secrets for the helm repos you want to reference. Eg in flux-system

    flux create secret helm helm-ghcr-io -u <USERNAME> -p ${GITHUB_TOKEN}

# You may also wish to create docker pull secrets

Reference from here https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

Or via a single command line

    kubectl create secret docker-registry polecatworks-clasic --docker-server=ghcr.io --docker-username=<USERNAME> --docker-password=${GHCR_TOKEN}

    kubectl create secret generic bproxy0-bucket-proxy --from-literal=access_key_id=<KEYID> --from-literal=secret_key=<SECRET>
