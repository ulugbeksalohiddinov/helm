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
