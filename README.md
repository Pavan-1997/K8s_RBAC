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
- Resources : Kubernetes API objects which we will operate on.
- Verbs : The operations which we want to do with our resources.

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
  
