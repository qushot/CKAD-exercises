
# Core Concepts (13%)

kubernetes.io > Documentation > Reference > kubectl CLI > [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

kubernetes.io > Documentation > Tasks > Monitoring, Logging, and Debugging > [Get a Shell to a Running Container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)

kubernetes.io > Documentation > Tasks > Access Applications in a Cluster > [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

kubernetes.io > Documentation > Tasks > Access Applications in a Cluster > [Accessing Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/) using API

kubernetes.io > Documentation > Tasks > Access Applications in a Cluster > [Use Port Forwarding to Access Applications in a Cluster](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)

### 「mynamespace」という名前空間を作成し、この名前空間にnginxポッドを作成します。
原文: `Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace`

<details><summary>show</summary>
<p>

```bash
kubectl create namespace mynamespace
kubectl run nginx --image=nginx --restart=Never -n mynamespace
```

</p>
</details>

### 先ほど説明したポッドをYAMLを使って作成します。
原文: `Create the pod that was just described using YAML`

<details><summary>show</summary>
<p>

Easily generate YAML with:

```bash
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > pod.yaml
```

```bash
cat pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```bash
kubectl create -f pod.yaml -n mynamespace
```

Alternatively, you can run in one line

```bash
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml | kubectl create -n mynamespace -f -
```

</p>
</details>

### 「env」コマンドを実行するbusyboxポッドを作成します(kubectlコマンドを使用)。それを実行して、出力を確認してください。
原文: `Create a busybox pod (using kubectl command) that runs the command "env". Run it and see the output`

<details><summary>show</summary>
<p>

```bash
kubectl run busybox --image=busybox --command --restart=Never -it -- env # -it will help in seeing the output
# or, just run it without -it
kubectl run busybox --image=busybox --command --restart=Never -- env
# and then, check its logs
kubectl logs busybox
```

</p>
</details>

### 「env」コマンドを実行するbusyboxポッドを作成します(YAMLを使用)。それを実行して、出力を確認してください。
原文: `Create a busybox pod (using YAML) that runs the command "env". Run it and see the output`

<details><summary>show</summary>
<p>

```bash
# create a  YAML template with this command
kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml --command -- env > envpod.yaml
# see it
cat envpod.yaml
```

```YAML
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - env
    image: busybox
    name: busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```bash
# apply it and then see the logs
kubectl apply -f envpod.yaml
kubectl logs busybox
```

</p>
</details>

### 新しい名前空間 'myns' を作成せずに YAML を取得します。
原文: `Get the YAML for a new namespace called 'myns' without creating it`

<details><summary>show</summary>
<p>

```bash
kubectl create namespace myns -o yaml --dry-run
```

</p>
</details>

### 1CPU、1Gメモリ、2ポッドのハードリミットを持つ新しいResourceQuota 'myrq' を作成せずに YAML を取得します。
原文: `Get the YAML for a new ResourceQuota called 'myrq' with hard limits of 1 CPU, 1G memory and 2 pods without creating it`

<details><summary>show</summary>
<p>

```bash
kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run -o yaml
```

</p>
</details>

### 全ての名前空間のポッドを取得します。
原文: `Get pods on all namespaces`

<details><summary>show</summary>
<p>

```bash
kubectl get po --all-namespaces
```

</p>
</details>

### ポート80のトラフィックを許可したnginxポッドを作成します。
原文: `Create a pod with image nginx called nginx and allow traffic on port 80`

<details><summary>show</summary>
<p>

```bash
kubectl run nginx --image=nginx --restart=Never --port=80
```

</p>
</details>

### ポッドのイメージをnginx:1.7.1に変更します。イメージがPullされるとすぐにポッドがKillされ、再作成されることを確認します。
原文: `Change pod's image to nginx:1.7.1. Observe that the pod will be killed and recreated as soon as the image gets pulled`

<details><summary>show</summary>
<p>

```bash
# kubectl set image POD/POD_NAME CONTAINER_NAME=IMAGE_NAME:TAG
kubectl set image pod/nginx nginx=nginx:1.7.1
kubectl describe po nginx # you will see an event 'Container will be killed and recreated'
kubectl get po nginx -w # watch it
```
*Note*: you can check pod's image by running

```bash
kubectl get po nginx -o jsonpath='{.spec.containers[].image}{"\n"}'
```

</p>
</details>

### 前のステップで作成したnginxポッドのIPを取得し、一時的なbusyboxイメージを使用して'/'を取得します。
原文: `Get nginx pod's ip created in previous step, use a temp busybox image to wget its '/'`

<details><summary>show</summary>
<p>

```bash
kubectl get po -o wide # get the IP, will be something like '10.1.1.131'
# create a temp busybox pod
kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- 10.1.1.131:80
```

Alternatively you can also try a more advanced option:

```bash
# Get IP of the nginx pod
NGINX_IP=$(kubectl get pod nginx -o jsonpath='{.status.podIP}')
# create a temp busybox pod
kubectl run busybox --image=busybox --env="NGINX_IP=$NGINX_IP" --rm -it --restart=Never -- wget -O- $NGINX_IP:80
``` 

</p>
</details>

### ポッドのYAMLを取得します。
原文: `Get pod's YAML`

<details><summary>show</summary>
<p>

```bash
kubectl get po nginx -o yaml
# or
kubectl get po nginx -oyaml
# or
kubectl get po nginx --output yaml
# or
kubectl get po nginx --output=yaml
```

</p>
</details>

### 潜在的な問題の詳細を含むポッドの情報を取得します。(例: ポッドが起動していない)
原文: `Get information about the pod, including details about potential issues (e.g. pod hasn't started)`

<details><summary>show</summary>
<p>

```bash
kubectl describe po nginx
```

</p>
</details>

### ポッドのログを取得します。
原文: `Get pod logs`

<details><summary>show</summary>
<p>

```bash
kubectl logs nginx
```

</p>
</details>

### ポッドがクラッシュして再起動した場合、前のインスタンスのログを取得します。
原文: `If pod crashed and restarted, get logs about the previous instance`

<details><summary>show</summary>
<p>

```bash
kubectl logs nginx -p
```

</p>
</details>

### nginxポッドでシンプルなシェルを実行します。
原文: `Execute a simple shell on the nginx pod`

<details><summary>show</summary>
<p>

```bash
kubectl exec -it nginx -- /bin/sh
```

</p>
</details>

### 'hello world'をechoして終了するbusyboxポッドを作成します。
原文: `Create a busybox pod that echoes 'hello world' and then exits`

<details><summary>show</summary>
<p>

```bash
kubectl run busybox --image=busybox -it --restart=Never -- echo 'hello world'
# or
kubectl run busybox --image=busybox -it --restart=Never -- /bin/sh -c 'echo hello world'
```

</p>
</details>

### 同じことをしますが、ポッドが完了したら自動的に削除されます。
原文: `Do the same, but have the pod deleted automatically when it's completed`

<details><summary>show</summary>
<p>

```bash
kubectl run busybox --image=busybox -it --rm --restart=Never -- /bin/sh -c 'echo hello world'
kubectl get po # nowhere to be found :)
```

</p>
</details>

### nginxポッドを作成し、env値を「var1=val1」に設定します。ポッド内にenv値が存在することを確認します。
原文: `Create an nginx pod and set an env value as 'var1=val1'. Check the env value existence within the pod`

<details><summary>show</summary>
<p>

```bash
kubectl run nginx --image=nginx --restart=Never --env=var1=val1
# then
kubectl exec -it nginx -- env
# or
kubectl describe po nginx | grep val1
# or
kubectl run nginx --restart=Never --image=nginx --env=var1=val1 -it --rm -- env
```

</p>
</details>
