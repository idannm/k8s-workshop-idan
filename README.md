# k8s-workshop-idan
Markdown
# K8s Workshop Final Project

This repository contains the Helm chart for the WordPress & MySQL assignment.

## Requirements
* Minikube (EC2)
* Helm

## Installation Steps

### 1. Install NGINX Ingress Controller
(Based on workshop section: "Install NGINX Ingress Controller")
```bash
helm install ingress-nginx ingress-nginx/ingress-nginx --create-namespace --namespace ingress-nginx
2. Install the Application (This Chart)
(Based on workshop section: "Set Up Your App" & "Set Up the Database")

Bash
helm install my-site ./idan-chart
3. Install Monitoring (Prometheus & Grafana)
(Based on workshop section: "Check out kube-prom-stack")

Bash
helm install monitoring prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
How to Access Grafana
(Based on workshop section: "Create a Grafana Panel")

Run Port-Forward:

Bash
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80 --address 0.0.0.0 &
Open Browser: http://<YOUR-EC2-IP>:3000

Credentials:

User: admin

Password: (Use the command below to get it)

Bash
kubectl get secret --namespace monitoring monitoring-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

זהו! ברגע שסיימת ליצור את הקבצים האלה, יש לך Repo שעונה על **כל** הסעיפים במסמך, כולל Helm, StatefulSet, PVC, Ingress, והוראות לניטור. בהצלחה!
