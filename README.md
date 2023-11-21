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
