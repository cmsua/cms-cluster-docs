# Account Control

## LDAP

### OLC Access Rules

Query why we want read by *

```bash
ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=%sysadmin,ou=sudoers,dc=cms,dc=physics,dc=ua,dc=edu

ldapsearch -Q -LLL -Y EXTERNAL -H ldapi:/// -b cn=config '(olcDatabase={2}mdb)' olcAccess

ldapmodify -Q -Y EXTERNAL -H ldapi:///<<EOF
dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.exact=gidNumber=0+uidNumber=1001,cn=peercred,cn=external,cn=auth manage by * break
olcAccess: {1}to attrs=userPassword,shadowLastChange by self write by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by anonymous auth by * none
olcAccess: {2}to * by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by self read by * read
EOF
```

### SSH Keys (Run in OpenLDAP)

Source: [Link](https://github.com/AndriiGrytsenko/openssh-ldap-publickey/blob/master/misc/openssh-lpk-openldap.ldif)

```bash
ldapadd -Q -Y EXTERNAL -H ldapi:///<<EOF
dn: cn=openssh-lpk-openldap,cn=schema,cn=config
objectClass: olcSchemaConfig
olcAttributeTypes: {0}( 1.3.6.1.4.1.24552.500.1.1.1.13 NAME 'sshPublicKey' D
 ESC 'MANDATORY: OpenSSH Public key' EQUALITY octetStringMatch SYNTAX 1.3.6.
 1.4.1.1466.115.121.1.40 )
olcObjectClasses: {0}( 1.3.6.1.4.1.24552.500.1.1.2.0 NAME 'ldapPublicKey' DE
 SC 'MANDATORY: OpenSSH LPK objectclass' SUP top AUXILIARY MUST ( sshPublicK
 ey $ uid ) )
EOF
```

## Kerberos

### Machine Initialization - Create Keytab

On the Admin Machine

```bash
HOST=rose kubectl -n kerberos exec -it $(kubectl -n kerberos get pod -l "component=kadmin" -o jsonpath='{.items[0].metadata.name}') -- bash -c "rm /etc/krb5.keytab; kadmin.local addprinc -randkey host/$HOST; kadmin.local ktadd -k /etc/krb5.keytab host/$HOST; base64 /etc/krb5.keytab"
```

On the target:

```bash
base64 -d | sudo tee /etc/krb5.keytab
sudo chmod 600 /etc/krb5.keytab
```

### Schemas (run in openldap)
```bash
# Text from  https://raw.githubusercontent.com/krb5/krb5/master/src/plugins/kdb/ldap/libkdb_ldap/kerberos.openldap.ldif -o kerberos.openldap.ldif
ldapadd -Q -Y EXTERNAL -H ldapi:///<<EOF

EOF
```

### olcAccess (run in openldap)
```bash
ldapsearch -Y EXTERNAL -H ldapi:/// -b cn=config "(|(cn=config)(olcDatabase={2}mdb))"

ldapmodify -Q -Y EXTERNAL -H ldapi:///<<EOF
dn: olcDatabase={2}mdb,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.exact=gidNumber=0+uidNumber=1001,cn=peercred,cn=external,cn=auth manage by * break
olcAccess: {1}to attrs=userPassword,shadowLastChange by self write by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by anonymous auth by * none
olcAccess: {2}to attrs=krbPrincipalKey by anonymous auth by dn.exact="uid=kdc-service,dc=cms,dc=physics,dc=ua,dc=edu" read by dn.exact="uid=kadmin-service,dc=cms,dc=physics,dc=ua,dc=edu" write by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by self write by * none
olcAccess: {3}to dn.subtree="cn=krbContainer,dc=cms,dc=physics,dc=ua,dc=edu" by dn.exact="uid=kdc-service,dc=cms,dc=physics,dc=ua,dc=edu" read by dn.exact="uid=kadmin-service,dc=cms,dc=physics,dc=ua,dc=edu" write by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by * none
olcAccess: {4}to dn.subtree="ou=users,dc=cms,dc=physics,dc=ua,dc=edu" by dn.exact="uid=kdc-service,dc=cms,dc=physics,dc=ua,dc=edu" read by dn.exact="uid=kadmin-service,dc=cms,dc=physics,dc=ua,dc=edu" write by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by * read
olcAccess: {5}to * by dn="cn=admin,dc=cms,dc=physics,dc=ua,dc=edu" write by * read
EOF
```

### Admin Accounts (run in openldap)
```bash
ldapadd -Q -Y EXTERNAL -H ldapi:///<<EOF
dn: uid=kdc-service,dc=cms,dc=physics,dc=ua,dc=edu
uid: kdc-service
objectClass: account
objectClass: simpleSecurityObject
userPassword: {CRYPT}x
description: Account used for the Kerberos KDC

dn: uid=kadmin-service,dc=cms,dc=physics,dc=ua,dc=edu
uid: kadmin-service
objectClass: account
objectClass: simpleSecurityObject
userPassword: {CRYPT}x
description: Account used for the Kerberos Admin server
EOF

ldappasswd -Q -Y EXTERNAL -H ldapi:/// uid=kdc-service,dc=cms,dc=physics,dc=ua,dc=edu -S
ldappasswd -Q -Y EXTERNAL -H ldapi:/// uid=kadmin-service,dc=cms,dc=physics,dc=ua,dc=edu -S
```

### Create Realm (run in kerberos)
```bash
kdb5_ldap_util create -D cn=admin,dc=cms,dc=physics,dc=ua,dc=edu -subtrees ou=users,dc=cms,dc=physics,dc=ua,dc=edu -r CMS.PHYSICS.UA.EDU -s -sf /etc/krb5kdc/stash2 -H ldap://openldap.openldap.svc.cluster.local
base64 /etc/krb5kdc/stash2 -w0 # for secret

kdb5_ldap_util -D cn=admin,dc=cms,dc=physics,dc=ua,dc=edu -H ldap://openldap.openldap.svc.cluster.local view -r CMS.PHYSICS.UA.EDU

kdb5_ldap_util -D cn=admin,dc=cms,dc=physics,dc=ua,dc=edu stashsrvpw -f /etc/krb5kdc/service2.keyfile uid=kdc-service,dc=cms,dc=physics,dc=ua,dc=edu
kdb5_ldap_util -D cn=admin,dc=cms,dc=physics,dc=ua,dc=edu stashsrvpw -f /etc/krb5kdc/service2.keyfile uid=kadmin-service,dc=cms,dc=physics,dc=ua,dc=edu
base64 /etc/krb5kdc/service2.keyfile -w0 # for secret
```