nGrinder supports LDAP. If LDAP configuration is enabled, users can be authenticated by LDAP.  
It can be used with LDAP servers like Active Directory or OpenLDAP.

## Configuration
You can find LDAP configuration in the `${NGRINDER_HOME}/system.conf`.
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
Specify the name of LDAP server host name (like `ldap.acme.org`). You don't need to include the protocol `ldap://` or `ldaps://`.


### Port
If your LDAP server uses a port other than `389`, you can specify the port number.  
To connect to LDAP over SSL(LDAPS), specify the port number `636` or the port number you are using.

### Base DN
Cause the LDAP search is performed recursively, we can start searching at a sub-node. You get a better performance because it narrows down the scope of a search.

This field specifies the DN of such a subtree.

If you do specify this value, the field normally looks something like `dc=acme,dc=org`

### User Search Base
If you specify a relative DN (from the base DN) here, further narrow down searches will be performed to the sub-tree.

The combination of **base DN** and **user search base** is used to searh a user.

### User Search Filter
The LDAP search performed in nGrinder is aim to get the user information by given the user id.  
The user search filter determines the query to be run to identify the user information.  
Default value is `(objectClass=*)`.

### User Display Name
Specify the attribute key that represents user display name. Default value is `displayName`.

### User Email
Specify the attribute key that represents user email. Default value is `mail`.

### Manager DN and Manager Password
If your LDAP server doesn't support anonymous binding, nGrinder need to bind to the LDAP server first. nGrinder will attempt to bind with manager DN and password you specified here.