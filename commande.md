Voici une reformulation plus concise et optimisée de vos commandes :

## **Option 1 : Commandes individuelles (plus lisible)**

```bash
php artisan make:migration create_annees_academiques_table
php artisan make:migration create_specialites_table
php artisan make:migration create_modules_table
php artisan make:migration create_evaluations_table
php artisan make:migration create_competences_table
php artisan make:migration add_details_to_users_table --table=users
```

---

## **Option 2 : Commandes en une seule ligne (plus rapide)**

```bash
php artisan make:migration create_annees_academiques_table && \
php artisan make:migration create_specialites_table && \
php artisan make:migration create_modules_table && \
php artisan make:migration create_evaluations_table && \
php artisan make:migration create_competences_table && \
php artisan make:migration add_details_to_users_table --table=users
```

---

## **Option 3 : Script Bash (recommandé pour projets récurrents)**

Créez un fichier `create_migrations.sh` :

```bash
#!/bin/bash

# Créer les migrations de tables
php artisan make:migration create_annees_academiques_table
php artisan make:migration create_specialites_table
php artisan make:migration create_modules_table
php artisan make:migration create_evaluations_table
php artisan make:migration create_competences_table

# Modifier une table existante
php artisan make:migration add_details_to_users_table --table=users

echo "✅ Toutes les migrations ont été créées avec succès!"
```

Puis exécutez :

```bash
chmod +x create_migrations.sh
./create_migrations.sh
```

---

## **Option 4 : Avec modèles et migrations (plus complet)**

```bash
php artisan make:model AnneeAcademique -m
php artisan make:model Specialite -m
php artisan make:model Module -m
php artisan make:model Evaluation -m
php artisan make:model Competence -m
php artisan make:migration add_details_to_users_table --table=users
```

---

## **Option 5 : Avec contrôleurs, modèles et migrations (complet)**

```bash
php artisan make:model AnneeAcademique -mcr
php artisan make:model Specialite -mcr
php artisan make:model Module -mcr
php artisan make:model Evaluation -mcr
php artisan make:model Competence -mcr
php artisan make:migration add_details_to_users_table --table=users
```

**Flags disponibles :**
- `-m` : Migration
- `-c` : Controller
- `-r` : Resource (RESTful)
- `-a` : Tout (`-mcr`)

---

## **Résumé des commandes créées :**

| Migration | Créée | Statut |
|-----------|-------|--------|
| `create_annees_academiques_table` | ✅ | 2025_11_16_090622 |
| `create_specialites_table` | ✅ | 2025_11_16_090710 |
| `create_modules_table` | ✅ | 2025_11_16_090740 |
| `add_details_to_users_table` | ✅ | 2025_11_16_090919 |
| `create_evaluations_table` | ✅ | 2025_11_16_092735 |
| `create_competences_table` | ✅ | 2025_11_16_092828 |

---

## **Prochaines étapes :**

```bash
# 1. Éditer les migrations (ajouter les colonnes)
nano database/migrations/2025_11_16_090622_create_annees_academiques_table.php

# 2. Exécuter les migrations
php artisan migrate

# 3. Vérifier les tables créées
php artisan migrate:status
```

Quelle option préférez-vous ? 🚀
