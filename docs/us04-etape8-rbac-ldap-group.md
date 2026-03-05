# US04 - Étape 8 : RBAC et association à un groupe LDAP

## Objectif
Associer automatiquement un utilisateur MidPoint à un groupe LDAP via un rôle RBAC.

Rôle utilisé :  
Employe_Standard

## Groupe LDAP utilisé

DN :

cn=employeesUN,ou=groups,ou=agrotech,dc=example,dc=com

ObjectClass :

groupOfUniqueNames

Attribut utilisé :

uniqueMember

## Vérification LDAP

Commande utilisée :

ldapsearch -x -H ldap://localhost:10389 \
-D "uid=admin,ou=system" -w secret \
-b "cn=employeesUN,ou=groups,ou=agrotech,dc=example,dc=com" "(objectClass=*)" uniqueMember

Résultat :

uniqueMember: uid=admin,ou=system

## Configuration MidPoint

Association configurée dans le rôle :

Employe_Standard

