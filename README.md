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
----------------------------------------------------------------

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

--------------------------------------------------------------------------------------------------------------------

**Helm bilan noldan repo yaratib, ishka tushirish**

Helm repo yaratamiz.

        helm create helm-front
        cd helm-front/templates/

templates papkada keraksiz resurslarni delete qilamiz.

        rm -r NOTES.txt httproute.yaml tests hpa.yaml serviceaccount.yaml

temlateslarni config qilinadi.

deployment.yaml:

        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: {{ .Release.Name }}-app
          namespace: {{ .Values.namespace }}
          labels:
            app: {{ .Release.Name }}-app
            chart: "{{ .Chart.Name }}-{{ .Chart.Version }}"
            release: {{ .Release.Name }}
            heritage: {{ .Release.Service }}
        spec:
          replicas: {{ .Values.replicaCount }}
          revisionHistoryLimit: {{ .Values.revisionHistoryLimit }}
          selector:
            matchLabels:
              app: {{ .Release.Name }}-app
          template:
            metadata:
              labels:
                app: {{ .Release.Name }}-app
            spec:
              imagePullSecrets:
              - name: {{ .Values.HarborSecret }}
              containers:
              - name: {{ .Release.Name }}-app
                image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
                imagePullPolicy: {{ .Values.image.pullPolicy }}
                ports:
                  - containerPort: {{ .Values.containerPort }}

service.yaml:

      apiVersion: v1
      kind: Service
      metadata:
        name: {{ .Release.Name }}-svc
        labels:
          app: {{ .Release.Name }}-svc
          chart: "{{ .Chart.Name }}-{{ .Chart.Version }}"
          release: {{ .Release.Name }}
          heritage: {{ .Release.Service }}
      spec:
        ports:
        - port: {{ .Values.servicePort }}
          protocol: TCP
          targetPort: {{ .Values.containerPort }}
        selector:
          app: {{ .Release.Name }}-app
        type: {{ .Values.serviceType }}
      status:
        loadBalancer: {}  

ingress.yaml:

        apiVersion: networking.k8s.io/v1
        kind: Ingress
        metadata:
          name: {{ .Release.Name }}-ingress
          creationTimestamp: null
        spec:
          ingressClassName: nginx
          rules:
          - host: {{ .Values.domen }}
            http:
              paths:
              - backend:
                  service:
                    name: {{ .Release.Name }}-svc
                    port:
                      number: {{ .Values.servicePort }}
                path: /
                pathType: {{ .Values.pathType }}
        status:
          loadBalancer: {}

values.yaml:

        namespace: prod

        #Replicas
        replicaCount: 3
        revisionHistoryLimit: 3

        #Image
        image:
          repository: harbor.mm.uz/java-images/node/front
          tag: latest
          pullPolicy: IfNotPresent
          pullSecretName: my-pull-secret

        #Ports
        containerPort: 80


        #Service-Params
        servicePort: 8099
        serviceType: ClusterIP

        #Ingress-Params
        domen: devdeploy.mm.uz
        pathType: Prefix

        httpRoute:
          enabled: false

        #Secrets
        HarborSecret: harbor-secret

Keyin helm reponi install qilamiz

        helm install front ./helm-front -n prod

Agar manifestda yoki kodeda o'zgarish bo'lsa update qilinadi

        helm upgrade --install	front ./helm-front -n prod



**Rollout and Rollback**

Check rollout status

        kubectl rollout status deployment/front-app -n prod

Qaysi versiya ishlayotganini ko‘rasiz.

        helm list -n prod

Qaysi revision’lar mavjudligini bilib olasiz.

        helm history front -n prod

Yangi reliz chiqanda update qilish

        helm upgrade front ./front -n prod
        
Yoki --install bilan:

        helm upgrade --install front ./front -n prod

-- Rollback qanday ishlaydi

Helm rollback komandasi:

helm rollback <release> <revision> -n <namespace>

<release> → front (sizning release nomi) - <revision> → qaysi revision’ga qaytmoqchi ekaningiz
-n <namespace> → prod

Misol: agar siz revision 2 holatiga qaytmoqchi bo‘lsangiz:

        helm rollback front 2 -n prod

Bu komanda front release’ini revision 2 holatiga tiklaydi.
Kubernetes Deployment avtomatik eski Pod’lar bilan tiklanadi.
Rollback amalga oshgach, yangi release avtomatik navbatdagi revision sifatida saqlanadi (masalan, 7-revision).+

