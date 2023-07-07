# K8s Role-Based Access Control (RBAC) 


Role-based access control (RBAC) is a way of granting users granular access to Kubernetes API resources. RBAC is a security design that restricts access to Kubernetes resources based on the role the user holds.

API Objects for configuring RBAC: Role, ClusterRole, RoleBinding and ClusterRoleBinding.

With RBAC we can add constraints to access kubernetes resources. For example we can give permission to listing pods for specific namespace to a ServiceAccount and prevent it to delete any resource. We can do it for a specific namespace or cluster wide as well.

- Role defines what can be done to Kubernetes Resources.
- Role contains one or more rules that represent a set of permissions.
- Permissions are additive. There are no deny rules.
- Roles are namespaced, meaning Roles work within the constraints of a namespace. It would default to the default namespace if none was specified.
- After creating a Role, you assign it to a user or group of users by creating a RoleBinding.

There are 3 important concept in RBAC.

- Subject : Subjects are nothing but a group of users, services, or team making an attempt at Kubernetes API. It defines what operations a user, service, or a group can perform.
    - Users: These are global, and meant for humans or processes living outside the cluster.
    - Groups: Set of users.
    - Service Accounts: Kubernetes uses service accounts to authenticate and authorize requests by pods to the Kubernetes API server. These are namespaced and meant for intra-cluster processes running inside pods.
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

- They can be divided into two types of management:

	- Users (If having a K8s cluster locally in Minikube or Kind out of the box we get admin access by default, Kubernetes Adminstrator in production will define access for dev, qe)
	- Service Accounts (Managing the access for the services in the cluster like malicious pod deleting configmaps,secrets or content related to API server) (These are the YAML files)
	- Roles/Cluster Roles
	- Role binding/ Cluster role binding 
	
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
Performing on Minikube cluster locally.

1. Create a K8s Minikube cluster using the commands in OneNote


2. Create a namespace
```
kubectl create ns rbac-test
```

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
