## CD

According to [ArgoCD](https://argoproj.github.io/argo-cd/getting_started/) Official Document, follow the few steps to deploy ArgoCD to k8s and open the UI web on browser. Below are necessary steps:

1. Install Argo CD

   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

2. Download Argo CD CLI [(on Linux)](https://argoproj.github.io/argo-cd/cli_installation/)

   ```bash
   curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
   chmod +x /usr/local/bin/argocd
   ```

   If there is any permission error, use:

   ```bash
   sudo -i chmod 777 /usr/local/bin/argocd
   // or
   sudo chmod +x /usr/local/bin/argocd
   ```

3. Access The Argo CD API Server (就是開一個 external IP)

   把 argocd-server 改成 LoadBalancer

   ```bash
   kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
   ```

4. 然後登入 argoCD

   拿到 default 密碼：

   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```

   登入：

   ```bash
   argocd login <ARGOCD_SERVER>
   // 放 server host
   // 帳號是：admin
   // 密碼是：上一個步驟拿到的東西
   ```

   改密碼：

   ```bash
   argocd account update-password
   ```

5. Register A Cluster To Deploy Apps To (Optional)

   Register 一個 cluster

   看看現在這台 server 有哪些 cluster：

   ```bash
   kubectl config get-contexts -o name
   ```

   在 argocd 註冊一個 cluster：

   ```bash
   argocd cluster add ${上一步驟拿到的 cluster name}
   ```

6. Create An Application From A Git Repository 在 argocd 上面創一個 project 連到 github

   我選擇開一個 UI，用 UI 操作：

   - web ui url: [https://140.112.106.12:31459](https://140.112.106.12:31459)
   - username: admin
   - password: iUJPlKTkKacAHuKn

   NOTE：在 server CLI 上面要先登入 argocd 才打得開 web UI

7. 延續 6. 在 argocd 上開專案的步驟如下。要先去 settings 裡面設定 repositories

   1. 選擇 connect repo using ssh
      1. 要去 server generate 一個 rsa key
      2. public key 放 github，private key 放在 argocd
      3. 設定好按 connct 就可以
   2. 之後就會在 Applications 按 new App 按照[官方設定](https://argoproj.github.io/argo-cd/getting_started/)完就可以看到新的 project 了
      - NOTE：如果設定的時候 sync policy 選擇了 automatic，點進去這個 project 就可以看到 pod 長出來了！
