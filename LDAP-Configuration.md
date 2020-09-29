NGrinder supports LDAP login as a default plugin. If you enable this configuration, you can authenticate users using LDAP.  
It can be used with LDAP servers like Active Directory or OpenLDAP.

## Configuration
You can find LDAP related configurations in the `${NGRINDER_HOME}/system.conf`.
> Any changes to the LDAP configuration will be affected on the fly.
```
######################################################################################
# LDAP login plugin configuration.
######################################################################################
# If you want to enable LDAP login plugin, please enable below.
#ldap.enabled=true

# your LDAP server address
#ldap.server=

# your LDAP server port
#ldap.port=389

# base dn where to start LDAP search
#ldap.base.dn=

# user search base where to start LDAP user search. The combination of base dn and the search base is used for search a user
#ldap.user.search.base=

# user search filter
#ldap.user.search.filter=(objectClass=*)

# user attribute that contains display name
#ldap.user.display_name=displayName

# user attribute that contains email
#ldap.user.email=mail

# If your LDAP server doesn't support anonymous binding, nGrinder have to authenticate with manager dn and password
#ldap.manager.dn=
#ldap.manager.password=

# LDAP connect timeout in the unit of milliseconds.
#ldap.connect.timeout=10000

# LDAP response timeout in the unit of milliseconds.
#ldap.response.timeout=30000
```

### Server
Specify the name of LDAP server host name. You don't need to include the protocol `ldap://` or `ldaps://`.


### Port
If your LDAP server uses a port other than `389`, you can specify the port number.

### Base DN
Cause the LDAP search is performed recursively, we can start a search a sub-node, you get a better performance because it narrows down the scope of a search.

This field specifies the DN of such a subtree.

### User Search Base
If you specify a relative DN (from the base DN) here, further narrow down searches will be performed to the sub-tree.

The combination of **base DN** and **user search base** is used to searh a user.

### User Search Filter
Specify the filter that determines a user more specifically. Default value is `(objectClass=*)`.

### User Display Name
Specify the attribute key that represents user display name. Default value is `displayName`.

### User Email
Specify the attribute key that represents user email. Default value is `mail`.

### Manager DN and Manager Password
If your LDAP server doesn't support anonymous binding, nGrinder need to bind to the LDAP server first. nGrinder will attempt to bind with manager DN and password you specified here.