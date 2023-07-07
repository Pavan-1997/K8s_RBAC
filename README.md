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

- Subject : Users, groups or service accounts.
- Resources : Kubernetes API objects which we will operate on.
- Verbs : The operations which we want to do with our resources.
