# JBoss AMQ Authentication and Authorization Strategies
There are two phases in determining what a user can do: authentication and authorization. Authentication involves determining a user is who they say they are. In the simple case this means verifying a username and password, but more complex authentication methods are available such as certificate based authentication. Once a user is authenticated you then need to determine what they are authorized to do.

A good strategy, and one baked into all the auth/authz tools JBoss AMQ provides, is to is do role based authorization. A set of roles is attached to the user which the authentication process determines once a user is authenticated. The application then decides what those particular role means. For example you might have a user role that is only allowed to perform read operations and an admin role that is allowed to do everything.

There are a bunch of JBoss things a user could be authorized to do, like undeploy an application, but with AMQ we are generally concerned with what queues or topics you are allowed to access. AMQ provides several methods of mapping user roles to queue/topic access.


## JBoss AMQ 6.2

### Authentication

#### OSGi Containers and JAAS Realms
Open Services Gateway Initiative (OSGi) - architecture for modular application development.
Apache Felix
Java Authentication and Authorization Service, a general framework for
implementing authentication in a Java application

#### Karaf Realm
The preconfigured JAAS realm JBoss AMQ automatically uses to provide authentication for remote administration of the OSGi runtime, Fuse Management Console and for JMX management. It consists of 4 modules: [PropertiesLoginModule](https://access.redhat.com/webassets/avalon/d/red-hat-jboss-fuse/6.3/api-reference/karaf/org/apache/karaf/jaas/modules/properties/PropertiesLoginModule.html), [FileAuditLoginModule](https://access.redhat.com/webassets/avalon/d/red-hat-jboss-fuse/6.3/api-reference/karaf/org/apache/karaf/jaas/modules/audit/FileAuditLoginModule.html), [EventAdminAuditLoginModule](https://access.redhat.com/webassets/avalon/d/red-hat-jboss-fuse/6.3/api-reference/karaf/org/apache/karaf/jaas/modules/audit/EventAdminAuditLoginModule.html), [PublickeyLoginModule](https://access.redhat.com/webassets/avalon/d/red-hat-jboss-fuse/6.3/api-reference/karaf/org/apache/karaf/jaas/modules/publickey/PublickeyLoginModule.html). All enabled by default out of the box.

##### PropertiesLoginModule
The [PropertiesLoginModule](https://access.redhat.com/webassets/avalon/d/red-hat-jboss-fuse/6.3/api-reference/karaf/org/apache/karaf/jaas/modules/properties/PropertiesLoginModule.html) uses a simple file-based repository located by default at `InstallDir/etc/users.properties`. You can add users to the file by opening it in a text editor and adding the user to a new line. Users in `users.properties` take the form:
```
Username=Password[,UserGroup|Role][,UserGroup|Role]...
```
e.g. `jdoe=topsecret,Administrator`. Yes that password is stored in plaintext by default, you can configure encryption by setting encryption properties in `InstallDir/etc/org.apache.karaf.jaas.cfg`:
```
encryption.enabled = true
encryption.name = basic
encryption.prefix = {CRYPT}
encryption.suffix = {CRYPT}
encryption.algorithm = MD5
encryption.encoding = hexadecimal
```

You can add a new group to users.properties using the following syntax:
```
_g_\:GroupName=Role1,Role2,...
```
So creating a new group and adding a user to it would look like:
```
_g_\:admingroup=SuperUser,Administrator
majorclanger=secretpass,_g_:admingroup
```

##### PublicKeyLoginModule
This [PublickeyLoginModule](https://access.redhat.com/webassets/avalon/d/red-hat-jboss-fuse/6.3/api-reference/karaf/org/apache/karaf/jaas/modules/publickey/PublickeyLoginModule.html) module is used to store SSH public key credentials in a flat file format. Users and keys are stored in `InstallDir/etc/keys.properties`. You can add users to the file using a text editor and adding the user and key with the following syntax:
```
Username=PublicKey[,UserGroup|Role][,UserGroup|Role]...
```
e.g.
```
jdoe=AAAAB3NzaC1kc3MAAACBAP1/U4EddRIpUt9KnC7s5Of2EbdSPO9EAMMeP4C2USZpRV1AIlH7WT2NWPq/xfW6MPbLm1Vs14E7gB00b/JmYLdrmVClpJ+f6AR7ECLCT7up1/63xhv4O1fnfqimFQ8E+4P208UewwI1VBNaFpEy9nXzrith1yrv8iIDGZ3RSAHHAAAAFQCXYFCPFSMLzLKSuYKi64QL8Fgc9QAAAnEA9+GghdabPd7LvKtcNrhXuXmUr7v6OuqC+VdMCz0HgmdRWVeOutRZT+ZxBxCBgLRJFnEj6EwoFhO3zwkyjMim4TwWeotifI0o4KOuHiuzpnWRbqN/C/ohNWLx+2J6ASQ7zKTxvqhRkImog9/hWuWfBpKLZl6Ae1UlZAFMO/7PSSoAAACBAKKSU2PFl/qOLxIwmBZPPIcJshVe7bVUpFvyl3BbJDow8rXfskl8wO63OzP/qLmcJM0+JbcRU/53Jj7uyk31drV2qxhIOsLDC9dGCWj47Y7TyhPdXh/0dthTRBy6bqGtRPxGa7gJov1xm/UuYYXPIUR/3x9MAZvZ5xvE0kYXO+rx
,Administrator
```
(note: put it all on one line, I inserted a newline after the private key for display purposes)

Just like the PropertiesLoginModule you can add new groups to the `keys.properties` file using the following syntax:
```
_g_\:GroupName=Role1,Role2,...
```
e.g.
```
_g_\:admingroup=SuperUser,Administrator
jdoe=AAAAB3NzaC1kc3MAAACBAP1/U4EddRIpUt9KnC7s5Of2EbdSPO9EAMMeP4C2USZpRV1AIlH7WT2NWPq/xfW6MPbLm1Vs14E7gB00b/JmYLdrmVClpJ+f6AR7ECLCT7up1/63xhv4O1fnfqimFQ8E+4P208UewwI1VBNaFpEy9nXzrith1yrv8iIDGZ3RSAHHAAAAFQCXYFCPFSMLzLKSuYKi64QL8Fgc9QAAAnEA9+GghdabPd7LvKtcNrhXuXmUr7v6OuqC+VdMCz0HgmdRWVeOutRZT+ZxBxCBgLRJFnEj6EwoFhO3zwkyjMim4TwWeotifI0o4KOuHiuzpnWRbqN/C/ohNWLx+2J6ASQ7zKTxvqhRkImog9/hWuWfBpKLZl6Ae1UlZAFMO/7PSSoAAACBAKKSU2PFl/qOLxIwmBZPPIcJshVe7bVUpFvyl3BbJDow8rXfskl8wO63OzP/qLmcJM0+JbcRU/53Jj7uyk31drV2qxhIOsLDC9dGCWj47Y7TyhPdXh/0dthTRBy6bqGtRPxGa7gJov1xm/UuYYXPIUR/3x9MAZvZ5xvE0kYXO+rx
,_g_:admingroup
```
(note: put it all on one line, I inserted a newline after the private key for display purposes)

#### JAAS LDAP Login Module
This module enables you to store user data in an LDAP database, authenticating username/password credentials and  returning the list of roles associated with the authenticated user. It has several important pieces of configuration for specifying how to find users and roles in ldap.

* `user.base.dn` - Specifies the `DN` of the subtree of the `DIT` to search for user entries. For example, `ou=users,ou=system`
* `user.filter` - Specifies the LDAP search filter used to locate user credentials. It is applied to the subtree selected by `user.base.dn`. For example, `(uid=%u)`, where %u is replaced by the username extracted from the incoming credentials
* `user.search.subtree` - Specifies whether the user entry search scope includes the subtrees of the tree selected by user.base.dn. If `true`, the user lookup is recursive (`SUBTREE`). If `false`, the user lookup is performed only at the first level (`ONELEVEL`).
* `role.base.dn` - Specifies the `DN` of the subtree of the `DIT` to search for role entries. For example,
`ou=groups,ou=system`.
* `role.filter` - Specifies the LDAP search filter used to locate roles. It is applied to the subtree selected by role.base.dn. For example, `(member=uid=%u)`, where `%u` is replaced by the user name extracted from the incoming credentials.
* `role.mapping` - Specifies the mapping between LDAP groups and JAAS roles. If no mapping is specified, the default mapping is for each LDAP group to map to the corresponding JAAS role of the same name. The role
mapping is specified with the following syntax:
```
ldap-group=jaas-role(,jaas-role)*(;ldap-group=jaas-role(,jaas-role)*)*
```
Where each LDAP group, `ldap-group`, is specified by its Common Name (`CN`). Forexample given the LDAP groups, admin, devop, and tester, you could map them to JAAS roles, as follows:
```
role.mapping=admin=Administrator;devop=Administrator,Deployer;tester=Monitor
```
* `role.name.attribute` - Specifies the attribute type of the role entry that contains the name of the role/group. If you omit this option, the role search feature is effectively disabled. For example, `cn`.
* `role.search.subtree` - Specifies whether the role entry search scope includes the subtrees of the tree selected by `role.base.dn`. If true, the role lookup is recursive (`SUBTREE`). If false, the role lookup is performed only at the first level (`ONELEVEL`).



## JBoss AMQ 7.0

# Additional Resources
* [Red Hat JBoss AMQ Overview - developers.redhat.com](https://developers.redhat.com/products/amq/overview/)
* [Red Hat JBoss AMQ 6.3 Security Guide](https://access.redhat.com/documentation/en-US/Red_Hat_JBoss_A-MQ/6.3/pdf/Security_Guide/Red_Hat_JBoss_A-MQ-6.3-Security_Guide-en-US.pdf) (maybe behind a paywall)
