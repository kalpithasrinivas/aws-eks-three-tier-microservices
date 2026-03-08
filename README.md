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

<hr>

<h2>🐳 Run the Application Locally (Docker Compose)</h2>

<p>
Before deploying the Robot Shop application to <b>Amazon EKS</b>, the application was first verified locally using <b>Docker Compose</b>.
Running the application locally ensures that all microservices function correctly before moving to a Kubernetes environment.
</p>

<p>
Stan's Robot Shop is a <b>microservices based e-commerce application</b> consisting of multiple services such as:
</p>

<ul>
<li>Web (AngularJS + Nginx)</li>
<li>Cart Service (NodeJS)</li>
<li>Catalogue Service (Golang)</li>
<li>User Service (PHP)</li>
<li>Shipping Service (Java Spring Boot)</li>
<li>Ratings Service (Python Flask)</li>
<li>Dispatch Service</li>
<li>MongoDB</li>
<li>MySQL</li>
<li>Redis</li>
<li>RabbitMQ</li>
</ul>

<p>
Each service runs inside an independent Docker container.
</p>

<h3>Build Images (Optional)</h3>

<p>
If you want to build the application images locally instead of using prebuilt images from Docker Hub, run the following commands.
</p>

<pre>
export INSTANA_AGENT_KEY="&lt;your agent key&gt;"
docker-compose build
</pre>

<p>
If you changed the image registry in the <code>.env</code> file, push the images to that registry.
</p>

<pre>
docker-compose push
</pre>

<h3>Pull Images from Docker Hub</h3>

<p>
If you do not build the images locally, pull the images directly from Docker Hub.
</p>

<pre>
docker-compose pull
</pre>

<h3>Start the Application</h3>

<pre>
docker-compose up
</pre>

<h3>Access the Application</h3>

<p>
Once all containers start successfully, open the application in your browser:
</p>

<pre>
http://localhost:8080
</pre>

<img width="1877" height="965" src="<img width="1904" height="971" alt="image" src="https://github.com/user-attachments/assets/ef6ce96f-850c-44d9-9ce6-b3accb382833" />

<h3>Running Docker Containers</h3>

<p>
The screenshot below shows all Robot Shop microservices running successfully as Docker containers.
</p>

<img width="1912" height="442" src="https://github.com/user-attachments/assets/83ae3d72-2876-41c9-8758-3df5e6fe9e45" />

<p>
After confirming that the application works locally, the next step is deploying it on a production-grade Kubernetes environment using <b>Amazon EKS</b>.
</p>

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

<img width="1919" height="615" alt="image" src="https://github.com/user-attachments/assets/319e41fd-bcfd-4675-a511-71d4916264cb" />

<h3>Cluster Verification</h3>

<pre>
kubectl get nodes
</pre>

<img width="813" height="93" alt="image" src="https://github.com/user-attachments/assets/60d0bd52-6e8a-4041-a425-056a037da2c1" />


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

<img width="935" height="75" alt="image" src="https://github.com/user-attachments/assets/04fdd2cb-26a6-4395-a9a1-6e2aec384d01" />

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

<img width="1051" height="361" alt="image" src="https://github.com/user-attachments/assets/fde27226-3b5b-473c-afcd-7a2016142516" />


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

<img width="1222" height="63" alt="image" src="https://github.com/user-attachments/assets/e8e03883-5c9a-44da-aaf4-f017aa5187fd" />


<hr>

<h2>🖥 Application Access</h2>

<p>
The application is accessible using the AWS ALB DNS name.
</p>

<pre>
http://k8s-robotsho-robotsho-xxxxx.us-east-1.elb.amazonaws.com
</pre>

<img width="1913" height="861" alt="image" src="https://github.com/user-attachments/assets/a703b129-9965-4de0-ab8f-96447473c2e3" />

<img width="1892" height="893" alt="image" src="https://github.com/user-attachments/assets/3fb534ef-22df-4b3d-9042-8de3fd03cc3e" />

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
