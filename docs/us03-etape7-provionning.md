# US-03 Étape 7 : Provisionnement A01 vers LDAP + Groupe Sales

## Objectif
Provisionner l'utilisateur A01 dans ApacheDS et le rattacher au groupe Sales.

## Processus de provisionnement

### 1. Création de l'utilisateur dans MidPoint
- Import CSV via la tâche RH_Agrotech
- Utilisateur A01 créé avec les attributs RH

### 2. Activation du provisionnement LDAP
- **Utilisateurs** → **A01** → **Projections**
- Ajouter projection : **ApacheDS_Agrotech** (Compte LDAP)
- Les mappings schemaHandling transforment automatiquement :
  - `name` → `uid`
  - `fullName` → `cn`
  - `familyName` → `sn`
  - `givenName` → `givenName`
  - `emailAddress` → `mail`

### 3. Création du compte LDAP
- **DN** : `uid=A01,ou=users,ou=agrotech,dc=example,dc=com`
- **Attributs** :
  - `uid: A01`
  - `cn: Jean Dupont`
  - `sn: Dupont`
  - `givenName: Jean`
  - `mail: j.dupont@agrotech.fr`

### 4. Rattachement au groupe Sales

**Commande :**
```bash
ldapmodify -x -H ldap://localhost:10389 -D "uid=admin,ou=system" -w secret << EOF
dn: cn=Sales,ou=groups,ou=agrotech,dc=example,dc=com
changetype: modify
add: member
member: uid=A01,ou=users,ou=agrotech,dc=example,dc=com
EOF
```

**Vérification :**
```bash
ldapsearch -x -H ldap://localhost:10389 -D "uid=admin,ou=system" -w secret -b "cn=Sales,ou=groups,ou=agrotech,dc=example,dc=com"
```

**Résultat :**
```
dn: cn=Sales,ou=groups,ou=agrotech,dc=example,dc=com
cn: Sales
member: uid=A01,ou=users,ou=agrotech,dc=example,dc=com
```

## Explication du provisionnement

Le provisionnement dans MidPoint suit ce workflow :

1. **Source** : Identité créée dans MidPoint (depuis CSV RH)
2. **Projection** : Association de l'identité à une ressource cible (ApacheDS)
3. **Mappings** : Transformation automatique des attributs (schemaHandling)
4. **Synchronisation** : Création du compte dans le système cible via connecteur LDAP
5. **Entitlements** : Rattachement aux groupes/permissions

## Avantages du provisionnement automatique

- Création automatique des comptes lors de l'arrivée d'un employé
- Cohérence des données entre les systèmes
- Traçabilité via l'historique MidPoint
- Réduction des erreurs manuelles

## Fichiers de configuration utilisés

- `configs/apacheds-resource.xml` : Configuration ressource ApacheDS
- `configs/resource-csv-rh.xml` : Configuration source RH
- `data/Odoo_HR.csv` : Données RH source
