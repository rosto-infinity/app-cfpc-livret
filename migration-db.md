Voici les migrations séparées avec l'ordre d'implémentation correct :

## **Ordre d'implémentation :**
```bash
1. php artisan migrate:fresh
2. Ou exécuter les migrations dans cet ordre
```

---

## **Migration 1 : Années Académiques**
**Fichier:** `database/migrations/2025_01_01_000001_create_annees_academiques_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('annees_academiques', function (Blueprint $table) {
            $table->id();
            $table->string('libelle', 20)->unique();
            $table->date('date_debut');
            $table->date('date_fin');
            $table->boolean('is_active')->default(false);
            $table->timestamps();
            
            $table->index('is_active');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('annees_academiques');
    }
};
```

---

## **Migration 2 : Spécialités**
**Fichier:** `database/migrations/2025_01_01_000002_create_specialites_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('specialites', function (Blueprint $table) {
            $table->id();
            $table->string('code', 20)->unique();
            $table->string('intitule', 100);
            $table->text('description')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('specialites');
    }
};
```

---

## **Migration 3 : Modules**
**Fichier:** `database/migrations/2025_01_01_000003_create_modules_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('modules', function (Blueprint $table) {
            $table->id();
            $table->string('code', 10)->unique();
            $table->string('intitule', 100);
            $table->decimal('coefficient', 4, 2)->default(1.00);
            $table->tinyInteger('ordre')->unsigned();
            $table->timestamps();
            
            $table->index('ordre');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('modules');
    }
};
```

---

## **Migration 4 : Users (Étudiants)**
**Fichier:** `database/migrations/2025_01_01_000004_create_users_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('matricule', 20)->unique();
            $table->string('nom', 100);
            $table->string('prenom', 100);
            $table->string('email')->unique()->nullable();
            $table->string('password');
            $table->foreignId('specialite_id')->constrained('specialites')->onDelete('restrict');
            $table->foreignId('annee_academique_id')->constrained('annees_academiques')->onDelete('restrict');
            $table->rememberToken();
            $table->timestamps();
            $table->softDeletes();
            
            $table->index(['specialite_id', 'annee_academique_id']);
            $table->index('nom');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

---

## **Migration 5 : Évaluations**
**Fichier:** `database/migrations/2025_01_01_000005_create_evaluations_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('evaluations', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
            $table->foreignId('module_id')->constrained('modules')->onDelete('restrict');
            $table->foreignId('annee_academique_id')->constrained('annees_academiques')->onDelete('cascade');
            $table->tinyInteger('semestre')->unsigned();
            $table->decimal('note', 5, 2);
            $table->timestamps();
            
            $table->unique(['user_id', 'module_id', 'semestre', 'annee_academique_id'], 'eval_unique');
            $table->index(['user_id', 'annee_academique_id']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('evaluations');
    }
};
```

---

## **Migration 6 : Bilans Compétences**
**Fichier:** `database/migrations/2025_01_01_000006_create_bilans_competences_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('bilans_competences', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
            $table->foreignId('annee_academique_id')->constrained('annees_academiques')->onDelete('cascade');
            $table->decimal('moy_eval_semestre1', 5, 2)->nullable();
            $table->decimal('moy_eval_semestre2', 5, 2)->nullable();
            $table->decimal('moy_evaluations', 5, 2)->nullable();
            $table->decimal('moy_competences', 5, 2)->nullable();
            $table->decimal('moyenne_generale', 5, 2)->nullable();
            $table->text('observations')->nullable();
            $table->timestamps();
            
            $table->unique(['user_id', 'annee_academique_id'], 'bilan_unique');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('bilans_competences');
    }
};
```

---

## **Commandes d'exécution :**

```bash
# Exécuter toutes les migrations
php artisan migrate

# Ou réinitialiser et migrer
php artisan migrate:fresh

# Voir l'état des migrations
php artisan migrate:status

# Annuler la dernière migration
php artisan migrate:rollback

# Annuler toutes les migrations
php artisan migrate:reset
```

---

## **Ordre de dépendances :**
```
1️⃣ annees_academiques (aucune dépendance)
2️⃣ specialites (aucune dépendance)
3️⃣ modules (aucune dépendance)
4️⃣ users (dépend de specialites & annees_academiques)
5️⃣ evaluations (dépend de users, modules & annees_academiques)
6️⃣ bilans_competences (dépend de users & annees_academiques)
```
