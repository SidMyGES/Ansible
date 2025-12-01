## Présentation du projet

Ce projet automatise le déploiement d'une stack LEMP (Linux, Nginx, MySQL, PHP) à l'aide d'Ansible.

Deux environnements sont supportés :
- staging
- production

Chaque environnement dispose de ses propres serveurs web et serveur de base de données.
Le déploiement utilise des rôles Ansible séparés et modulaires (nginx, php, mysql, app).

---

## Architecture Ansible

```
ansible/
  inventory.ini
  group_vars/
    all.yml
    staging_web.yml
    staging_db.yml
    production_web.yml
    production_db.yml
  roles/
    nginx/
      tasks/main.yml
      handlers/main.yml
      files/conf
    php/
      tasks/main.yml
      handlers/main.yml
    mysql/
      tasks/main.yml
      handlers/main.yml
    app/
      tasks/main.yml
      templates/index.php.j2
  staging.yml
  production.yml
```

---

## Rôles

### Rôle nginx
- Installe Nginx
- Déploie un fichier de configuration nginx personnalisé
- Active le service

### Rôle php
- Installe PHP-FPM et les dépendances nécessaires
- Active le service php-fpm

### Rôle mysql
- Installe MySQL Server
- Crée la base de données
- Crée un utilisateur pour chaque serveur web de l’environnement
- Autorise MySQL à écouter sur toutes les interfaces

### Rôle app
- Crée le dossier de l'application
- Déploie le fichier index.php à l'aide d'un template Jinja2
- Configure les identifiants de connexion en fonction de l’environnement (via group_vars)

---

## Inventaire

```
[staging_web]
web1 ansible_host=...
web2 ansible_host=...

[staging_db]
db1 ansible_host=...

[production_web]
web1 ansible_host=...
web2 ansible_host=...
web3 ansible_host=...

[production_db]
db1 ansible_host=...
```

---

## Variables

Les variables globales sont définies dans `group_vars/all.yml`.
Les variables spécifiques aux environnements sont définies dans :

- `group_vars/staging_web.yml`
- `group_vars/staging_db.yml`
- `group_vars/production_web.yml`
- `group_vars/production_db.yml`

Les mots de passe sensibles sont chiffrés avec Ansible Vault.

---

## Déploiement

### Déploiement en staging
```
ansible-playbook -i inventory.ini staging.yml
```

### Déploiement en production
```
ansible-playbook -i inventory.ini production.yml
```

---

## Sécurité

Les mots de passe (MySQL, SSH, etc.) sont chiffrés à l’aide d’Ansible Vault :
```
ansible-vault encrypt group_vars/*.yml
```

---

## Conclusion

Le projet fournit un déploiement entièrement automatisé d'une stack LEMP, séparé par environnement, modulable et sécurisé via Ansible Vault.
