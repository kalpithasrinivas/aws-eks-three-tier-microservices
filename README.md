<h1 align="center">🚀 Three Tier Microservices Deployment on AWS EKS</h1>

<p align="center">
Deploying a Production-Style Microservices Application using <b>AWS EKS, Kubernetes, Helm and AWS ALB</b>
</p>

<hr>

<h2>📌 Project Overview</h2>

<p>
This project demonstrates the deployment of a <b>microservices based e-commerce application (Stan's Robot Shop)</b> on <b>Amazon Elastic Kubernetes Service (EKS)</b>.
</p>

<p>
The goal of this project is to showcase real-world DevOps practices such as:
</p>

<ul>
<li>Container orchestration using Kubernetes</li>
<li>Infrastructure provisioning on AWS</li>
<li>Application deployment using Helm</li>
<li>External traffic routing using AWS ALB</li>
<li>Persistent storage using EBS</li>
<li>Scaling Kubernetes worker nodes</li>
<li>Troubleshooting Kubernetes scheduling issues</li>
</ul>

<hr>

<h2>🏗 Architecture</h2>

<pre>
User
  │
  ▼
AWS Application Load Balancer
  │
  ▼
Kubernetes Ingress
  │
  ▼
Web Service (Frontend)
  │
  ▼
Microservices Layer
 ├── Cart
 ├── Catalogue
 ├── User
 ├── Shipping
 ├── Payment
 ├── Ratings
 └── Dispatch
  │
  ▼
Databases
 ├── MongoDB
 ├── MySQL
 ├── Redis
 └── RabbitMQ
</pre>

<hr>

<h2>🧰 Technologies Used</h2>

<table>
<tr>
<th>Technology</th>
<th>Purpose</th>
</tr>

<tr>
<td>AWS EKS</td>
<td>Managed Kubernetes cluster</td>
</tr>

<tr>
<td>Docker</td>
<td>Containerization</td>
</tr>

<tr>
<td>Kubernetes</td>
<td>Container orchestration</td>
</tr>

<tr>
<td>Helm</td>
<td>Kubernetes package manager</td>
</tr>

<tr>
<td>AWS Load Balancer Controller</td>
<td>Integrates ALB with Kubernetes</td>
</tr>

<tr>
<td>EBS CSI Driver</td>
<td>Persistent storage</td>
</tr>

<tr>
<td>MongoDB / MySQL / Redis</td>
<td>Application databases</td>
</tr>

<tr>
<td>RabbitMQ</td>
<td>Message queue</td>
</tr>

</table>

<hr>

<h2>📦 Application Microservices</h2>

<ul>
<li><b>Web</b> – AngularJS frontend</li>
<li><b>Cart Service</b> – NodeJS</li>
<li><b>Catalogue Service</b> – Golang</li>
<li><b>User Service</b> – PHP</li>
<li><b>Shipping Service</b> – Java Spring Boot</li>
<li><b>Ratings Service</b> – Python Flask</li>
<li><b>Dispatch Service</b></li>
<li><b>MongoDB</b> – Product database</li>
<li><b>MySQL</b> – User data</li>
<li><b>Redis</b> – Cache</li>
<li><b>RabbitMQ</b> – Messaging system</li>
</ul>

<hr>

<h2>⚙ Step 1 – Create EKS Cluster</h2>

<p>
The Kubernetes cluster was created using <b>eksctl</b>.
</p>

<pre>
eksctl create cluster \
--name robot-shop-eks-cluster \
--region us-east-1 \
--zones us-east-1a,us-east-1b \
--nodegroup-name robot-shop-nodes \
--node-type t3.small \
--nodes 2 \
--managed
</pre>

<p>
This command automatically created:
</p>

<ul>
<li>EKS control plane</li>
<li>EC2 worker nodes</li>
<li>VPC and subnets</li>
<li>Security groups</li>
<li>Kubernetes networking</li>
<li>CoreDNS</li>
<li>kube-proxy</li>
</ul>

<h3>Cluster Verification</h3>

<pre>
kubectl get nodes
</pre>

<img src="screenshots/cluster-nodes.png" width="900">

<hr>

<h2>🔐 Step 2 – Enable IAM OIDC Provider</h2>

<p>
OIDC allows Kubernetes pods to securely access AWS services using IAM roles.
</p>

<pre>
export cluster_name=robot-shop-eks-cluster
</pre>

<pre>
eksctl utils associate-iam-oidc-provider \
--cluster $cluster_name \
--approve
</pre>

<p>
Required for:
</p>

<ul>
<li>AWS Load Balancer Controller</li>
<li>EBS CSI Driver</li>
</ul>

<hr>

<h2>🌐 Step 3 – Install AWS Load Balancer Controller</h2>

<p>
The AWS Load Balancer Controller allows Kubernetes to automatically create <b>AWS Application Load Balancers</b>.
</p>

<h3>Create IAM Policy</h3>

<pre>
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
</pre>

<pre>
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json
</pre>

<h3>Create Service Account</h3>

<pre>
eksctl create iamserviceaccount \
--cluster robot-shop-eks-cluster \
--namespace kube-system \
--name aws-load-balancer-controller \
--role-name AmazonEKSLoadBalancerControllerRole \
--attach-policy-arn arn:aws:iam::ACCOUNT-ID:policy/AWSLoadBalancerControllerIAMPolicy \
--approve
</pre>

<h3>Install Controller Using Helm</h3>

<pre>
helm repo add eks https://aws.github.io/eks-charts
helm repo update
</pre>

<pre>
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=robot-shop-eks-cluster
</pre>

<img src="screenshots/alb-controller.png" width="900">

<hr>

<h2>💾 Step 4 – Install EBS CSI Driver</h2>

<p>
The EBS CSI driver enables dynamic volume provisioning for stateful applications.
</p>

<pre>
eksctl create addon \
--name aws-ebs-csi-driver \
--cluster robot-shop-eks-cluster
</pre>

Used by:

<ul>
<li>MongoDB</li>
<li>MySQL</li>
</ul>

<hr>

<h2>🚀 Step 5 – Deploy Robot Shop Application</h2>

<p>Deploy application using Helm.</p>

<pre>
cd eks/helm
kubectl create namespace robot-shop
helm install robot-shop --namespace robot-shop .
</pre>

<h3>Verify Pods</h3>

<pre>
kubectl get pods -n robot-shop
</pre>

<img src="screenshots/pods-running.png" width="900">

<hr>

<h2>⚠ Troubleshooting</h2>

<p>
Some pods were stuck in <b>Pending</b> state due to insufficient node memory.
</p>

<pre>
kubectl describe pod mysql-xxxxx -n robot-shop
</pre>

Error:

<pre>
Insufficient memory
</pre>

<h3>Solution – Scale Node Group</h3>

<pre>
eksctl scale nodegroup \
--cluster robot-shop-eks-cluster \
--name robot-shop-nodes \
--nodes-min 1 \
--nodes-max 4 \
--nodes 3
</pre>

<hr>

<h2>🌍 Step 6 – Expose Application Using Ingress</h2>

<p>
Ingress was configured to expose the application through AWS ALB.
</p>

<pre>
kubectl apply -f ingress.yaml
</pre>

<h3>Verify Ingress</h3>

<pre>
kubectl get ingress -n robot-shop
</pre>

<img src="screenshots/alb.png" width="900">

<hr>

<h2>🖥 Application Access</h2>

<p>
The application is accessible using the AWS ALB DNS name.
</p>

<pre>
http://k8s-robotsho-robotsho-xxxxx.us-east-1.elb.amazonaws.com
</pre>

<img src="screenshots/robot-shop-ui.png" width="900">

<hr>

<h2>🧠 DevOps Skills Demonstrated</h2>

<ul>
<li>AWS EKS cluster provisioning</li>
<li>Kubernetes microservices deployment</li>
<li>Helm application packaging</li>
<li>Kubernetes networking using Ingress</li>
<li>AWS ALB integration</li>
<li>Persistent storage using EBS</li>
<li>Cluster troubleshooting</li>
<li>Scaling worker nodes</li>
</ul>

<hr>

<h2>🧹 Cleanup</h2>

<p>
To avoid AWS charges delete the cluster after testing.
</p>

<pre>
eksctl delete cluster robot-shop-eks-cluster
</pre>

<hr>

<h2>👩‍💻 Author</h2>

<p>
<b>Kalpitha Srinivas</b><br>
DevOps Engineer
</p>

<p>
<a href="https://www.linkedin.com/in/kalpitha-srinivas-740358220/">LinkedIn</a>
</p>
