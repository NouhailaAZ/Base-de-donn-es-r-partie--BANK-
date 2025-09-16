# Base de données répartie - BANK 🏦

## 📋 Description du projet

Ce projet implémente une **base de données distribuée** pour un système bancaire, permettant de gérer les clients et comptes à travers plusieurs sites géographiques. Le système utilise Oracle 11g et est conçu pour optimiser les performances et assurer la disponibilité des données bancaires.

### Contexte
Notre banque possède des succursales dans différentes régions géographiques (Casablanca et Rabat). Plutôt que de stocker toutes les données sur un seul serveur centralisé, ce projet utilise une architecture distribuée pour :
- Répartir les données entre les différentes succursales
- Permettre à chaque succursale d'accéder aux informations locales
- Maintenir une vue globale cohérente à l'échelle de l'ensemble de la banque

## 🎯 Objectifs

- Décentralisation de l'information bancaire
- Gestion de l'augmentation du volume de données
- Optimisation du volume des transactions
- Amélioration des performances par traitement parallèle des requêtes

## 🏗️ Architecture du système

### Sites distribués
- **Site Central** : Serveur principal de coordination
- **Site 1 (Casablanca)** : Gestion des clients de Casablanca avec solde négatif
- **Site 2 (Rabat)** : Gestion des clients de Rabat avec solde positif ou nul

### Répartition des données
- **R1** : σ(VilleClient='Casablanca' AND Solde < 0)(Client ⋈ Compte)
- **R2** : σ(VilleClient='Rabat' AND Solde ≥ 0)(Client ⋈ Compte)

## 🛠️ Technologies utilisées

- **SGBD** : Oracle 11g
- **Développement** : SQL Developer
- **Virtualisation** : VMware
- **OS** : Windows 7
- **Langage** : SQL, PL/SQL

## 📊 Modèle de données

### Tables principales
- **Client** : Informations des clients bancaires
- **Compte** : Données des comptes bancaires
- **Agence** : Informations des agences

## 🚀 Installation et configuration

### Prérequis
- VMware installé
- Image Windows 7
- Oracle 11g
- SQL Developer

### Étapes de déploiement

#### 1. Configuration du réseau virtuel
```bash
# Adresses IP des machines virtuelles
BDD Central    : 192.168.1.100
BDD Casablanca : 192.168.1.1  
BDD Rabat      : 192.168.1.10
```

#### 2. Test de connectivité
```bash
ping 192.168.1.1   # Test vers Casablanca
ping 192.168.1.10  # Test vers Rabat
```

#### 3. Création des utilisateurs
```sql
-- Site Central
CREATE USER Central IDENTIFIED BY Central;
GRANT ALL PRIVILEGES TO Central;

-- Site Casablanca
CREATE USER siteCasa IDENTIFIED BY Site1;
GRANT ALL PRIVILEGES TO siteCasa;

-- Site Rabat
CREATE USER userSite2 IDENTIFIED BY Site2;
GRANT ALL PRIVILEGES TO userSite2;
```

#### 4. Configuration des Database Links
```sql
-- Depuis le site central
CREATE PUBLIC DATABASE LINK linkCasa
CONNECT TO siteCasa IDENTIFIED BY siteCasa
USING 'siteCasa:1552/ORCL';

CREATE PUBLIC DATABASE LINK lienSite2
CONNECT TO userSite2 IDENTIFIED BY Site2
USING 'Site2:1552/ORCL';
```

#### 5. Création des synonymes
```sql
-- Pour Casablanca
CREATE PUBLIC SYNONYM Client1 FOR ClientSite1@linkCasa;
CREATE PUBLIC SYNONYM Compte1 FOR CompteSite1@linkCasa;
CREATE PUBLIC SYNONYM Agence1 FOR AgenceSite1@linkCasa;
```

## ⚙️ Fonctionnalités implémentées

### Procédures stockées
- **Insertion des clients** depuis la base centrale
- **Insertion des comptes** vers les sites distants
- **Gestion automatisée** de la répartition

### Triggers de synchronisation
- **Insertion** : Distribution automatique vers les sites appropriés
- **Suppression** : Suppression cascade sur tous les sites
- **Mise à jour** : Synchronisation des modifications

### Gestion de la cohérence
- Transactions distribuées
- Vérification de l'intégrité référentielle
- Gestion des conflits de données

## 📝 Utilisation

### Test des liens de base de données
```sql
SELECT sysdate FROM dual@linkCasa;
SELECT sysdate FROM dual@lienSite2;
```

### Vérification des utilisateurs
```sql
SELECT username FROM DBA_USERS;
```

## 👥 Équipe de développement

- **Nouhaila AZLAG**
- **Chaimaa FAKHAM**

*Projet réalisé dans le cadre de la 2ème année cycle d'ingénieur - Génie Informatique (2022/2023)*

## 📈 Avantages de cette architecture

- **Performance** : Traitement parallèle des requêtes
- **Disponibilité** : Redondance des données
- **Scalabilité** : Capacité d'extension horizontale
- **Localité** : Accès rapide aux données locales

## 🔮 Évolutions futures

- Ajout de nouveaux sites géographiques
- Optimisation des requêtes distribuées
- Interface utilisateur web
- Monitoring et métriques de performance

## 📄 Licence

Ce projet est réalisé dans un cadre académique.

---

*Pour plus d'informations, consultez la documentation complète `Base de données Avances Projet.pdf
`*
