template.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-app
  labels:
    app: {{ .Release.Name }}-app
    chart: "{{ .Chart.Name }}-{{ .Chart.Version }}"
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  affinity:
    {{- toYaml .Values.affinity | nindent 4 }}
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
      containers:
      - name: {{ .Release.Name }}-app
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        ports:
          - containerPort: {{ .Values.containerPort }}
        resources:
          {{- toYaml .Values.resources | nindent 10 }}
        env:
        {{- toYaml .Values.env | nindent 8}}


values.yaml

#Affinity
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: ulugbek
          operator: In
          values:
          - salohiddinov

#Replicas
replicaCount: 3
revisionHistoryLimit: 3

#Image
image:
  repository: myapp/myapp-image
  tag: latest
  pullPolicy: IfNotPresent
  pullSecretName: my-pull-secret

#Ports
containerPort: 8080

#Resources
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
    
    
#ENV-ConfigMap
env:
- name: ENV
  valueFrom:
    configMapKeyRef:
      name: environment
      key: ENV
- name: AUTHOR
  valueFrom:
    configMapKeyRef:
      name: environment
      key: AUTHOR    
