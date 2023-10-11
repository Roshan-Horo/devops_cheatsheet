
Requirement:
- kubernetes cluster( means kubernetes installed)
- kubectl

## Helmcomponents

- `helm cli`
- `chart`
- `online chart repository`
- `metadata`

## Chart

chart.yaml
```yaml
apiVersion: v2
appVersion: 5.8.1
version: 12.1.27
name: workpress
description: Web publishing platform fo rbuilding blogs and websites.
type: application
dependencies: 
  - condition: mariadb.enabled
    name: mariadb
    repository: https://charts.bitnami.com/bitnami
    version: 9.x.x
keywords:
  - application
  - blog
  - wordpress
maintainers:
  - email: containers@bitname.com
    name: Bitnami
home:
icon: 
```

## Helm Chart Structure

hello-world-chart
  - templates/  # Templates directory
  - values.yaml # configurable values
  - chart.yaml  # chart information
  - LICENSE     # chart License
  - Readme.md   # Readme file
  - charts\     # Dependency chart


## Helm Command

- `helm <command>`

- `helm --help`: help command
- search `chart` on ArtifactHub
- `helm search <app_name>`
- `helm serach hub <app_name>`

### Deploying wordpress

- `helm repo add bitnami https://charts.bitnami.com/bitnami`
- `helm install my-release bitnami/wordpress` or `helm install --set wordpressBlogName="Helm Tutorials" my-release bitnami/wordpress` or `--values custom-values.yaml`
- locally : `helm pull --untar bitname/wordpress`: download and unzip, then install `helm install my-release ./wordpress`
- `helm list`
- `helm uninstall my-release`

## upgrade and rollback

- `helm upgrade nginx-release bitnami/nginx`
- `helm upgrade nginx-release bitnami/nginx --version <chart_version> `
- `helm list`: list all helm release
- `helm history <release_name>`: more info about release like it's previous revision
- `helm rollback <release_name> <revision_no>`: rollback to specified revision

## Repo command

- `helm repo list`
- `helm repo add <url>`
- `hlem repo update`

# Writing Helm Chart

- `helm create <chart_name>`: create a helm folder structure and you can modify it.
- `template file with values` : 

templates/deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-nginx
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
        - name: hello-world
          image: {{ .Values.image.repository }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
```

values.yaml
```yaml
replicaCount: 2

image:
  repository: nginx
  pullPolicy: IfNotPresent
  tag: "1.16.0"
```

## verifying helm chart

- `linting` : `helm lint ./<chart_name>`
- `templating` : `helm template ./<chart_name>` or `helm template <release_name> <chart_name>` or  `helm template <chart_name> --debug`
- `dry run`: `helm install <release_name> <chart_name> --dry-run`

# Functions

{{ default "nginx" .Values.image.repository }}

# Pipelines

{{ .Values.image.repository | default "nginx" }}

# Conditionals

```yaml
{{- if .Values.orgLabel }}
labels:
  org: {{ .Values.orgLabel }}
{{- else if eq .Values.orgLabel "hr"}}
labels:
  org: human resources
{{- end }}
```


## Scope

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-appinfo
data:
  {{- with .Values.app}}
    {{- with .ui}}
      background: {{ .bg }}
      foreground: {{ .fg }}
    {{-end }}
    {{- with .db}}
      database: {{ .name }}
      connection: {{ .conn }}
    {{- end}}
  release: {{ $.Release.name }}

  {{- end }}
```


## Ranges

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-regioninfo
data:
  region:
  {{- range .Values.region }}
    - {{ . | quote }}
  {{- end}}
```

## Named Templates

__helpers.tpl
```yaml
{{- define "labels"}}
     app.kubernetes.io/name: {{ .Release.Name }}
     app.Kubernetes.io/instance: {{ .Release.Name }}
{{- end}}
```

deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: {{ .Release.Name }}-nginx
  labels:
    {{- template "labels" . }}
spec:
  selector:
    matchLabels:
      {{- include "labels" . | indent 2 }}
  template:
    metadata:
      labels:
        {{- include "labels" . | indent 4 }}
    spec:
      containers:
        - name: nginx
          image: "nginx:1.16:0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
```


## Chart Hooks

- `pre/post upgrade`: run jobs before/after upgrade
- `pre/post install`: run jobs before/after install
- `pre/post delete`: run jobs before/after delete

backup-job.yaml
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: {{ .Release.Name }}-nginx
  annotations:
    "helm.sh/hook": pre-upgrade
    "helm.sh/hook-weight": "5"
    "helm.sh/hook-delete-policy": hook-succeeded
spec:
  template:
    metadata:
      name: {{ .Release.Name }}-nginx
    spec:
      restartPolicy: Never
      containers:
        - name: pre-upgrade-backup-job
          image: "alpine"
          command: ["/bin/backup.sh"]
```

