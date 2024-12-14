    ```
    apiVersion: v1
    kind: Pod
    metadata:
      creationTimestamp: null
      labels:
         run: nginx-nodename
      name: nginx-nodename
    spec:
      nodeName: <node_name> # add
      containers:
      - image: nginx
        name: nginx-nodename
        resources: {}
      dnsPolicy: ClusterFirst
      restartPolicy: Always
    status: {}
    ```
