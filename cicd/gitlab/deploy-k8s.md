# Deploy to Kubernetes

1. Create a namespace: `kubectl create ns ci-ns`
2. Create a ServiceAccount: `kubectl create serviceaccount ci-sa --namespace=ci-ns`
3. Create a Role and a Rolebinding

    ```yml
    # cicd-role.yml
    
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: ci-role
      namespace: ci-ns
    roles:
    - apiGroups: [""]
      resources: ["pods", "services"]
      verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
    - apiGroups: ["extensions", "apps"]
      resources: ["deployments"]
      verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
    ```
  
    And bind the Role to the ServiceAccount:
    `kubectl create rolebinding ci-rb --role=ci-role --serviceaccount=ci-ns:ci-sa --namespace=ci-ns`.

4. Create a Kubectl config file

    ```yml
    apiVersion: v1
    kind: Config
    preference: {}
    clusters:
    - name: cluster-1
      cluster:
        certificate-authority-data: <cluster_token>
        server: <server_url>
    users:
    - name: cluster-1-user-1
      user:
        as-user-extra: {}
        token: <sa_token>
    contexts:
    - name: cluster-1-ctx
      context:
        cluster: cluster-1
        namespace: default
        user: cluster-1-user-1
    current-context: cluster-1-ctx
    ```
  
    > When `ci-sa` ServiceAccount is created, a Secret object is created too. The `<sa_token>` in `users.user.token` is the value of the `ci-sa`'s Secret (the value needs to decode with base64 first).

5. Set the Kubectl config file to `KUBECONFIG` env: `export KUBECONFIG=cluster-1-config.yml`.

## Deployment and Service

1. Create a Deployment
2. Create a Service for the Deployment
3. Pass pipeline's variables to Deployment and Service with `envsub < deploy.yml | kubectl apply -f -`.
4. Create a Secret for `docker-registry`.
5. Use the Secret in the Deployment with `spec.imagePullSecret.name: <secret_name>`.
