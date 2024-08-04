# K8s Role-Based Access Control (RBAC) 


Role-based access control (RBAC) is a way of granting users granular access to Kubernetes API resources. RBAC is a security design that restricts access to Kubernetes resources based on the role the user holds.

API Objects for configuring RBAC: Role, ClusterRole, RoleBinding and ClusterRoleBinding.

With RBAC we can add constraints to access kubernetes resources. For example we can give permission to listing pods for specific namespace to a ServiceAccount and prevent it to delete any resource. We can do it for a specific namespace or cluster wide as well.

- Role defines what can be done to Kubernetes Resources.
- Role contains one or more rules that represent a set of permissions.
- Permissions are additive. There are no deny rules.
- Roles are namespaced, meaning Roles work within the constraints of a namespace. It would default to the default namespace if none was specified.
- After creating a Role, you assign it to a user or group of users by creating a RoleBinding.

There are 3 important concepts in RBAC.

- Subject : Subjects are nothing but a group of users, services, or team making an attempt at Kubernetes API. It defines what operations a user, service, or a group can perform.
  ```
    - Users: These are global, and meant for humans or processes living outside the cluster.
    - Groups: Set of users.
    - Service Accounts: Kubernetes uses service accounts to authenticate and authorize requests by pods to the Kubernetes API server. These are namespaced and meant for intra-cluster processes running inside pods.
  ```
- Resources : The set of Kubernetes API Objects available in the cluster are called Resources. For examples, Pods, Deployments, Services, Nodes, PersistentVolumes etc.
- Verbs : The set of operations that can be executed to the resources are called verbs. For examples, different verbs are get, watch, create, delete. Ultimately all of them are Create, Read, Update or Delete (CRUD) operations.

ClusterRole:

- ClusterRole works the same as Role, but they are applied to the cluster as a whole.
- ClusterRoles are not bound to a specific namespace. ClusterRole give access across more than one namespace or all namespaces.
- After creating a ClusterRole, you assign it to a user or group of users by creating a RoleBinding or ClusterRoleBinding.
- ClusterRoles are typically used with service accounts.

RoleBinding:

- Role Binding is used for granting permission to a Subject.
- RoleBinding holds a list of subjects (users, groups, or service accounts), and a reference to the role being granted.
- Role and RoleBinding are used in namespaced scoped.
- RoleBinding may reference any Role in the same namespace.
After you create a binding, you cannot change the Role or ClusterRole that it refers to. If you do want to change the roleRef for a binding, you need to remove the binding object and create a replacement

ClusterRoleBinding:

- ClusterRole and ClusterRoleBinding function like Role and RoleBinding, except they have wider scope.
- RoleBinding grants permissions within a specific namespace, whereas a ClusterRoleBinding grants access cluster-wide and to multiple namespaces.
- ClusterRoleBinding is binding or associating a ClusterRole with a Subject (users, groups, or service accounts).
  
---
Few key points:

- RBAC is related to security

- They can be divided the types of management:
  ```
	- Users (If having a K8s cluster locally in Minikube or Kind out of the box we get admin access by default, Kubernetes Adminstrator in production will define access for dev, qe)
	- Service Accounts (Managing the access for the services in the cluster like malicious pod deleting configmaps,secrets or content related to API server) (These are the YAML files)
	- Roles/Cluster Roles
	- Role binding/ Cluster role binding 
  ```
- API Server act as OAuth Server
	
- K8s doesn't deal with user management but offloads it to Identity Providers (AWS-IAM, AZURE, Openshift)

- Identity Providers in Organization - SSO, LDAP, Okta

- Identity Brokers (popular) that connect to Providers - Keyclock 

- K8s create a default Service Account at the time of creating POD the apps will be talking to API Server

- Role is a YAML file that has access to resources(secrets, configmap, pods) within a single namespace

- If wants to have across cluster then we create a Cluster Role

- Role Binding to attach the role created to the user 

- Create Service Account, Role -> Using Role Binding bind them both 

![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/550298aa-1e21-4609-ba05-f1de66a2acfa)

---
# Performing on Minikube cluster locally.

1. Create a K8s Minikube cluster using the commands in OneNote


2. Create a namespace
```
kubectl create ns rbac-test
```
![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/f632fae1-caf1-4447-bbc9-b1a8920d0f5a)


3. Create a Service Account within the namespace
```
vim serviceaccount.yml
```
 
4. Apply the created Service Account on cluster
```
kubectl apply -f serviceaccount.yml
```

5. Check whether the serviceaccount can get the pods in namespace created
```
kubectl auth can-i --as system:serviceaccount:rbac-test:foo get pods -n rbac-test
```
![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/b364248d-250e-4402-9221-c75eb7814b5d)


6. Creating a role 
```
vi role.yml
```
 
7. Apply the created role on cluster
```
kubectl apply -f role.yml
```

8. Once again perform Step 5.

Still you see as "no" since, we haven't binded it to the serviceaccount

![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/eadb9443-8540-4f02-8304-2858c9202e82)


9. Create a Role Binding 
```
vi rolebinding.yml
```
  
10. Apply the created rolebinding on cluster
```
kubectl apply -f rolebinding.yml 
```

11.  Once again perform Step 5.

     You should see as "yes"

![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/9bbf8db3-5182-4789-9dc7-064c2fe83306)

12. ServiceAccount can create pods, deployments like below
```
kubectl auth can-i --as system:serviceaccount:rbac-test:foo create pods -n rbac-test
```
```
kubectl auth can-i --as system:serviceaccount:rbac-test:foo create deployments -n rbac-test
```
   Output: yes


13. You can't create a pod in different namespace as we have not assinged with a cluster role
```
kubectl auth can-i --as system:serviceaccount:rbac-test:foo create deployments -n kube-system
```
   Output: no

![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/95466ee2-2568-4384-b15a-42afa6f85bb6)


14. Create a cluster rolebinding
```
vi clusterrolebinding.yml
```

15. Apply the created rolebinding on cluster
```
kubectl apply -f clusterrolebinding.yml
```

16. Now perform Step 13.

	   You should see as "yes" 
	
	   Now you can do everything in the cluster.

![image](https://github.com/Pavan-1997/K8s_RBAC/assets/32020205/1a606d32-5c38-47d7-914c-1924a6a17b64)

---

## ANOTHER VERSION FOR RBAC

1. Check if you can create pods
```
kubectl auth can-i create pods
```

2. Check your identity
```
kubectl auth whoami
```

3. Shortened alias for the same command:
```
k auth can-i create po
```

4. Check if a specific user (e.g., pavan) can create pods:
```
kubectl auth can-i create pods --as pavan
```
![image](https://github.com/user-attachments/assets/73e67386-f3a2-4e56-b28a-02c9fced5d7d)


5. Apply the `role2.yml` and `role-binding2.yml`
```
k apply -f role2.yml
k apply -f role-binding2.yml
```

6. Check again if a specific user (e.g., pavan) can create pods:
```
kubectl auth can-i create pods --as pavan
```
![image](https://github.com/user-attachments/assets/0469180a-b809-4d20-bbf8-c4b38f85f0d6)

7. Now Generate the csr key and file
```
openssl genrsa -out pavan1.key 2048
```
```
openssl req -new -key pavan.key -out pavan.csr -subj "/CN=pavan"
```
8. Create a new user entry named pavan in your kubeconfig file, using the provided client key and certificate
```
k config set credentials pavan --client-key=pavan.key --client-certificate=pavan.crt
```

9. Now set the context for the user pavan
```
 k config get-contexts
```
![image](https://github.com/user-attachments/assets/9eeccbc9-0884-4af4-bf21-44ca295dbe29)

```
k config set-context pavan --cluster=kind-pavantest --user=pavan
```
```
 k config get-contexts
```
![image](https://github.com/user-attachments/assets/7d1c87a4-35d9-41a1-b0fe-78a57c60d5a4)

10. Set the context to the user pavan
```
k config use-context pavan
```


