[values-fin.txt](https://github.com/user-attachments/files/18217894/values-fin.txt)

[ingres-fin.txt](https://github.com/user-attachments/files/18217893/ingres-fin.txt)

[service-fin.txt](https://github.com/user-attachments/files/18217892/service-fin.txt)

[deploy-fin.txt](https://github.com/user-attachments/files/18217891/deploy-fin.txt)

[mon.txt](https://github.com/user-attachments/files/18409558/mon.txt)

[post.txt](https://github.com/user-attachments/files/18409557/post.txt)


**For K3S**

    export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

    kubectl get pods --all-namespaces

    helm ls --all-namespaces


        helm create test-helm 
        helm repo list
        helm  repo add bitnami https://charts.bitnami.com/bitnami
        helm show chart chart-name

        helm install test ./test-helm -n test #test-helm dirdagi helmni install qilish

#Agar helmda o'zgarishlar bo'lsa va boshqattan run qilish kerak bo'lsa
        helm upgrade --install	test ./test-helm -n test

        helm uninstall test

        helm package --version 0.0.1 . # tar gz yaratib beradi arxivlangan
        helm repo index test-helm   # helm papkdan tashqarida turib qilinadi. test-helm papkani ichida index.yaml yaratadi.
