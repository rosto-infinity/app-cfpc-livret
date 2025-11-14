

## Cahier des Charges - Système de Gestion des Évaluations Académiques
### 1. Contexte et Objectifs
1.1 Objectif Principal
Développer une application web Laravel 12 pour la gestion des évaluations semestrielles des étudiants avec calcul automatique des moyennes et suivi des compétences.
1.2 Portée
-	Gestion des années académiques
-	Gestion des spécialités
-	Gestion des utilisateurs (étudiants)
-	Saisie et calcul des évaluations
-	Génération de relevés de notes
________________________________________
2. Modèle Conceptuel de Données (MCD)
2.1 Entités Identifiées
ANNEE_ACADEMIQUE
-	id (PK)
-	libelle (ex: "2025-2026")
-	date_debut
-	date_fin
-	is_active
SPECIALITE
-	id (PK)
-	code
-	intitule
MODULE
-	id (PK)
-	code (M1, M2, ..., M10)
-	intitule
-	coefficient
UTILISATEUR (Étudiant)
-	id (PK)
-	matricule
-	nom
-	prenom
-	specialite_id (FK)
-	annee_academique_id (FK)
EVALUATION
-	id (PK)
-	utilisateur_id (FK)
-	module_id (FK)
-	semestre (1 ou 2)
-	note
-	annee_academique_id (FK)
BILAN_COMPETENCES
-	id (PK)
-	utilisateur_id (FK)
-	annee_academique_id (FK)
-	moy_eval1 (30% semestre 1)
-	moy_eval2 (30% semestre 2)
-	moy_competences (70%)
-	moyenne_generale (100%)
2.2 Relations
-	Un étudiant appartient à UNE spécialité
-	Un étudiant est inscrit pour UNE année académique
-	Un étudiant a PLUSIEURS évaluations
-	Une évaluation concerne UN module et UN étudiant
-	Un bilan de compétences est lié à UN étudiant

---
### . Migrations Laravel 12 (Optimisées)

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        // Table: annees_academiques
        Schema::create('annees_academiques', function (Blueprint $table) {
            $table->id();
            $table->string('libelle', 20)->unique(); // ex: "2025-2026"
            $table->date('date_debut');
            $table->date('date_fin');
            $table->boolean('is_active')->default(false);
            $table->timestamps();
            
            $table->index('is_active');
        });

        // Table: specialites
        Schema::create('specialites', function (Blueprint $table) {
            $table->id();
            $table->string('code', 20)->unique();
            $table->string('intitule', 100);
            $table->text('description')->nullable();
            $table->timestamps();
        });

        // Table: modules
        Schema::create('modules', function (Blueprint $table) {
            $table->id();
            $table->string('code', 10)->unique(); // M1, M2, ..., M10
            $table->string('intitule', 100);
            $table->decimal('coefficient', 4, 2)->default(1.00);
            $table->tinyInteger('ordre')->unsigned(); // Pour l'ordre d'affichage
            $table->timestamps();
            
            $table->index('ordre');
        });

        // Table: users (étudiants)
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

        // Table: evaluations
        Schema::create('evaluations', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
            $table->foreignId('module_id')->constrained('modules')->onDelete('restrict');
            $table->foreignId('annee_academique_id')->constrained('annees_academiques')->onDelete('cascade');
            $table->tinyInteger('semestre')->unsigned(); // 1 ou 2
            $table->decimal('note', 5, 2); // Note sur 20
            $table->timestamps();
            
            // Contrainte d'unicité: un étudiant ne peut avoir qu'une note par module/semestre/année
            $table->unique(['user_id', 'module_id', 'semestre', 'annee_academique_id'], 'eval_unique');
            $table->index(['user_id', 'annee_academique_id']);
        });

        // Table: bilans_competences
        Schema::create('bilans_competences', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
            $table->foreignId('annee_academique_id')->constrained('annees_academiques')->onDelete('cascade');
            $table->decimal('moy_eval_semestre1', 5, 2)->nullable(); // Moyenne des M1-M5
            $table->decimal('moy_eval_semestre2', 5, 2)->nullable(); // Moyenne des M6-M10
            $table->decimal('moy_evaluations', 5, 2)->nullable(); // 30% du total
            $table->decimal('moy_competences', 5, 2)->nullable(); // 70% (Bilan skills)
            $table->decimal('moyenne_generale', 5, 2)->nullable(); // 100%
            $table->text('observations')->nullable();
            $table->timestamps();
            
            $table->unique(['user_id', 'annee_academique_id'], 'bilan_unique');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('bilans_competences');
        Schema::dropIfExists('evaluations');
        Schema::dropIfExists('users');
        Schema::dropIfExists('modules');
        Schema::dropIfExists('specialites');
        Schema::dropIfExists('annees_academiques');
    }
};

```

4. Fonctionnalités Principales
4.1 Gestion des Évaluations
-	Saisie des notes par module et par semestre (M1 à M10)
-	Calcul automatique des moyennes semestrielles
-	MOY/20 EVAL1 = (M1 + M2 + M3 + M4 + M5) / 5
-	MOY/20 EVAL2 = (M6 + M7 + M8 + M9 + M10) / 5
4.2 Calcul du Bilan Final
-	Évaluations semestrielles: 30% de la note finale
-	Bilan des compétences: 70% de la note finale
-	MOY.GEN (100%) = (MOY_EVALUATIONS × 0.30) + (MOY_COMPETENCES × 0.70)
4.3 Reporting
-	Relevés de notes individuels
-	Tableaux récapitulatifs par classe/spécialité
-	Export Excel/PDF
________________________________________
5. Optimisations Techniques
5.1 Index de Base de Données
-	Index sur les clés étrangères
-	Index composites pour les requêtes fréquentes
-	Index sur les colonnes de filtrage (is_active, semestre)
5.2 Contraintes d\Intégrité
-	Contraintes UNIQUE pour éviter les doublons
-	Foreign keys avec ON DELETE appropriés
-	Soft deletes pour les utilisateurs
5.3 Performance
-	Eager loading pour réduire les requêtes N+1
-	Cache pour les données statiques (modules, spécialités)
-	Pagination pour les listes longues
________________________________________
6. Stack Technique
-	Framework: Laravel 12 (Novembre 2024)
-	PHP: 8.2+
-	Base de données: MySQL 8.0+ / PostgreSQL 15+
-	Front-end: Blade + Livewire ou Vue.js/Inertia
-	Authentification: Laravel Sanctum/Breeze
________________________________________
7. Modules Complémentaires (Optionnels)
-	Dashboard administrateur
-	Gestion des enseignants
-	Historique des modifications
-	Notifications email/SMS
-	API REST pour intégration externe

---



#  BilanCompetenceController 

```php
<?php

declare(strict_types=1);

namespace App\Http\Controllers;

use App\Models\BilanCompetence;
use App\Models\User;
use App\Models\AnneeAcademique;
use App\Models\Specialite;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use Illuminate\Support\Facades\DB;

class BilanCompetenceController extends Controller
{
    public function index(Request $request): View
    {
        $query = BilanCompetence::with(['user.specialite', 'anneeAcademique']);

        if ($anneeId = $request->input('annee_id')) {
            $query->byAnneeAcademique((int)$anneeId);
        }

        if ($specialiteId = $request->input('specialite_id')) {
            $query->whereHas('user', fn($q) => $q->where('specialite_id', $specialiteId));
        }

        $bilans = $query->latest()->paginate(20);

        $annees = AnneeAcademique::ordered()->get();
        $specialites = Specialite::ordered()->get();

        return view('bilans.index', compact('bilans', 'annees', 'specialites'));
    }

    public function create(Request $request): View
    {
        $userId = $request->query('user_id');
        $user = $userId ? User::with(['specialite', 'anneeAcademique', 'bilanCompetence'])->findOrFail($userId) : null;

        // Si l'utilisateur a déjà un bilan pour l'année en cours
        if ($user && $user->bilanCompetence) {
            return redirect()
                ->route('bilans.edit', $user->bilanCompetence)
                ->with('info', 'Un bilan existe déjà pour cet étudiant. Vous pouvez le modifier.');
        }

        $users = User::with(['specialite', 'anneeAcademique'])
            ->whereDoesntHave('bilanCompetence')
            ->ordered()
            ->get();

        return view('bilans.create', compact('user', 'users'));
    }

    public function store(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'user_id' => 'required|exists:users,id',
            'moy_competences' => 'required|numeric|min:0|max:20',
            'observations' => 'nullable|string|max:1000',
        ]);

        try {
            DB::beginTransaction();

            $user = User::findOrFail($validated['user_id']);

            // Vérifier si un bilan existe déjà
            $exists = BilanCompetence::where('user_id', $user->id)
                ->where('annee_academique_id', $user->annee_academique_id)
                ->exists();

            if ($exists) {
                return back()
                    ->withInput()
                    ->with('error', 'Un bilan existe déjà pour cet étudiant.');
            }

            $bilan = new BilanCompetence([
                'user_id' => $user->id,
                'annee_academique_id' => $user->annee_academique_id,
                'observations' => $validated['observations'] ?? null,
            ]);

            $bilan->calculateAndSave((float)$validated['moy_competences']);

            DB::commit();

            return redirect()
                ->route('bilans.show', $bilan)
                ->with('success', 'Bilan de compétences créé avec succès.');
        } catch (\Exception $e) {
            DB::rollBack();
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la création: ' . $e->getMessage());
        }
    }

    public function show(BilanCompetence $bilan): View
    {
        $bilan->load(['user.specialite', 'anneeAcademique']);

        $evaluationsSemestre1 = $bilan->user->getEvaluationsBySemestre(1);
        $evaluationsSemestre2 = $bilan->user->getEvaluationsBySemestre(2);

        return view('bilans.show', compact('bilan', 'evaluationsSemestre1', 'evaluationsSemestre2'));
    }

    public function edit(BilanCompetence $bilan): View
    {
        $bilan->load(['user.specialite', 'anneeAcademique']);

        return view('bilans.edit', compact('bilan'));
    }

    public function update(Request $request, BilanCompetence $bilan): RedirectResponse
    {
        $validated = $request->validate([
            'moy_competences' => 'required|numeric|min:0|max:20',
            'observations' => 'nullable|string|max:1000',
        ]);

        try {
            DB::beginTransaction();

            $bilan->observations = $validated['observations'] ?? null;
            $bilan->calculateAndSave((float)$validated['moy_competences']);

            DB::commit();

            return redirect()
                ->route('bilans.show', $bilan)
                ->with('success', 'Bilan de compétences mis à jour avec succès.');
        } catch (\Exception $e) {
            DB::rollBack();
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la mise à jour: ' . $e->getMessage());
        }
    }

    public function destroy(BilanCompetence $bilan): RedirectResponse
    {
        try {
            $bilan->delete();

            return redirect()
                ->route('bilans.index')
                ->with('success', 'Bilan de compétences supprimé avec succès.');
        } catch (\Exception $e) {
            return back()->with('error', 'Erreur lors de la suppression: ' . $e->getMessage());
        }
    }

    public function recalculer(BilanCompetence $bilan): RedirectResponse
    {
        try {
            DB::beginTransaction();

            $bilan->calculateAndSave($bilan->moy_competences);

            DB::commit();

            return back()->with('success', 'Bilan recalculé avec succès.');
        } catch (\Exception $e) {
            DB::rollBack();
            return back()->with('error', 'Erreur lors du recalcul: ' . $e->getMessage());
        }
    }

    public function genererTous(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'annee_academique_id' => 'required|exists:annees_academiques,id',
            'specialite_id' => 'nullable|exists:specialites,id',
            'moy_competences_defaut' => 'required|numeric|min:0|max:20',
        ]);

        try {
            DB::beginTransaction();

            $query = User::where('annee_academique_id', $validated['annee_academique_id'])
                ->whereDoesntHave('bilanCompetence', function($q) use ($validated) {
                    $q->where('annee_academique_id', $validated['annee_academique_id']);
                });

            if (isset($validated['specialite_id'])) {
                $query->where('specialite_id', $validated['specialite_id']);
            }

            $users = $query->get();
            $count = 0;

            foreach ($users as $user) {
                $bilan = new BilanCompetence([
                    'user_id' => $user->id,
                    'annee_academique_id' => $user->annee_academique_id,
                ]);

                $bilan->calculateAndSave((float)$validated['moy_competences_defaut']);
                $count++;
            }

            DB::commit();

            return back()->with('success', "{$count} bilans générés avec succès.");
        } catch (\Exception $e) {
            DB::rollBack();
            return back()->with('error', 'Erreur lors de la génération: ' . $e->getMessage());
        }
    }

    public function tableauRecapitulatif(Request $request): View
    {
        $anneeId = $request->input('annee_id') ?? AnneeAcademique::active()->first()?->id;
        $specialiteId = $request->input('specialite_id');

        $query = BilanCompetence::with(['user.specialite', 'anneeAcademique'])
            ->whereNotNull('moyenne_generale');

        if ($anneeId) {
            $query->where('annee_academique_id', $anneeId);
        }

        if ($specialiteId) {
            $query->whereHas('user', fn($q) => $q->where('specialite_id', $specialiteId));
        }

        $bilans = $query->get()->sortByDesc('moyenne_generale');

        $stats = [
            'total' => $bilans->count(),
            'admis' => $bilans->filter(fn($b) => $b->isAdmis())->count(),
            'moyenne_generale' => $bilans->avg('moyenne_generale'),
            'meilleure_moyenne' => $bilans->max('moyenne_generale'),
            'moyenne_la_plus_basse' => $bilans->min('moyenne_generale'),
        ];

        $annees = AnneeAcademique::ordered()->get();
        $specialites = Specialite::ordered()->get();

        return view('bilans.tableau-recapitulatif', compact('bilans', 'stats', 'annees', 'specialites'));
    }
}

```


---


### EvaluationController 

```php
<?php

declare(strict_types=1);

namespace App\Http\Controllers;

use App\Models\Evaluation;
use App\Models\User;
use App\Models\Module;
use App\Models\AnneeAcademique;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use Illuminate\Support\Facades\DB;

class EvaluationController extends Controller
{
    public function index(Request $request): View
    {
        $query = Evaluation::with(['user', 'module', 'anneeAcademique']);

        if ($userId = $request->input('user_id')) {
            $query->byUser((int)$userId);
        }

        if ($semestre = $request->input('semestre')) {
            $query->bySemestre((int)$semestre);
        }

        if ($anneeId = $request->input('annee_id')) {
            $query->byAnneeAcademique((int)$anneeId);
        }

        $evaluations = $query->latest()->paginate(20);

        $users = User::ordered()->get();
        $annees = AnneeAcademique::ordered()->get();

        return view('evaluations.index', compact('evaluations', 'users', 'annees'));
    }

    public function create(Request $request): View
    {
        $userId = $request->query('user_id');
        $user = $userId ? User::with(['specialite', 'anneeAcademique'])->findOrFail($userId) : null;

        $modules = Module::ordered()->get();
        $users = User::with(['specialite', 'anneeAcademique'])->ordered()->get();
        $annees = AnneeAcademique::ordered()->get();

        return view('evaluations.create', compact('modules', 'users', 'annees', 'user'));
    }

    public function store(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'user_id' => 'required|exists:users,id',
            'module_id' => 'required|exists:modules,id',
            'annee_academique_id' => 'required|exists:annees_academiques,id',
            'semestre' => 'required|integer|in:1,2',
            'note' => 'required|numeric|min:0|max:20',
        ]);

        try {
            // Vérifier si l'évaluation existe déjà
            $exists = Evaluation::where('user_id', $validated['user_id'])
                ->where('module_id', $validated['module_id'])
                ->where('semestre', $validated['semestre'])
                ->where('annee_academique_id', $validated['annee_academique_id'])
                ->exists();

            if ($exists) {
                return back()
                    ->withInput()
                    ->with('error', 'Cette évaluation existe déjà pour cet étudiant.');
            }

            Evaluation::create($validated);

            return redirect()
                ->route('evaluations.index')
                ->with('success', 'Évaluation créée avec succès.');
        } catch (\Exception $e) {
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la création: ' . $e->getMessage());
        }
    }

    public function show(Evaluation $evaluation): View
    {
        $evaluation->load(['user.specialite', 'module', 'anneeAcademique']);

        return view('evaluations.show', compact('evaluation'));
    }

    public function edit(Evaluation $evaluation): View
    {
        $evaluation->load(['user', 'module', 'anneeAcademique']);
        
        $modules = Module::ordered()->get();
        $users = User::with(['specialite', 'anneeAcademique'])->ordered()->get();
        $annees = AnneeAcademique::ordered()->get();

        return view('evaluations.edit', compact('evaluation', 'modules', 'users', 'annees'));
    }

    public function update(Request $request, Evaluation $evaluation): RedirectResponse
    {
        $validated = $request->validate([
            'note' => 'required|numeric|min:0|max:20',
        ]);

        try {
            $evaluation->update($validated);

            return redirect()
                ->route('evaluations.index')
                ->with('success', 'Évaluation mise à jour avec succès.');
        } catch (\Exception $e) {
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la mise à jour: ' . $e->getMessage());
        }
    }

    public function destroy(Evaluation $evaluation): RedirectResponse
    {
        try {
            $evaluation->delete();

            return redirect()
                ->route('evaluations.index')
                ->with('success', 'Évaluation supprimée avec succès.');
        } catch (\Exception $e) {
            return back()->with('error', 'Erreur lors de la suppression: ' . $e->getMessage());
        }
    }

    public function saisirMultiple(Request $request): View
    {
        $userId = $request->query('user_id');
        $semestre = $request->query('semestre', 1);

        $user = null;
        $modules = collect();
        $evaluations = collect();

        if ($userId) {
            $user = User::with(['specialite', 'anneeAcademique'])->findOrFail($userId);
            
            $modules = $semestre == 1 
                ? Module::semestre1()->ordered()->get()
                : Module::semestre2()->ordered()->get();

            $evaluations = Evaluation::where('user_id', $userId)
                ->where('semestre', $semestre)
                ->where('annee_academique_id', $user->annee_academique_id)
                ->get()
                ->keyBy('module_id');
        }

        $users = User::with(['specialite', 'anneeAcademique'])->ordered()->get();

        return view('evaluations.saisir-multiple', compact('user', 'modules', 'evaluations', 'users', 'semestre'));
    }

    public function storeMultiple(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'user_id' => 'required|exists:users,id',
            'semestre' => 'required|integer|in:1,2',
            'evaluations' => 'required|array',
            'evaluations.*.module_id' => 'required|exists:modules,id',
            'evaluations.*.note' => 'required|numeric|min:0|max:20',
        ]);

        try {
            DB::beginTransaction();

            $user = User::findOrFail($validated['user_id']);

            foreach ($validated['evaluations'] as $evalData) {
                Evaluation::updateOrCreate(
                    [
                        'user_id' => $user->id,
                        'module_id' => $evalData['module_id'],
                        'semestre' => $validated['semestre'],
                        'annee_academique_id' => $user->annee_academique_id,
                    ],
                    [
                        'note' => $evalData['note'],
                    ]
                );
            }

            DB::commit();

            return redirect()
                ->route('evaluations.saisir-multiple', [
                    'user_id' => $user->id,
                    'semestre' => $validated['semestre']
                ])
                ->with('success', 'Évaluations enregistrées avec succès.');
        } catch (\Exception $e) {
            DB::rollBack();
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de l\'enregistrement: ' . $e->getMessage());
        }
    }

    public function releveNotes(User $user): View
    {
        $user->load(['specialite', 'anneeAcademique']);

        $evaluationsSemestre1 = $user->getEvaluationsBySemestre(1);
        $evaluationsSemestre2 = $user->getEvaluationsBySemestre(2);

        $moyenneSemestre1 = $user->getMoyenneSemestre(1);
        $moyenneSemestre2 = $user->getMoyenneSemestre(2);

        return view('evaluations.releve-notes', compact(
            'user',
            'evaluationsSemestre1',
            'evaluationsSemestre2',
            'moyenneSemestre1',
            'moyenneSemestre2'
        ));
    }
}


```


---



### ModuleController 

```php
<?php

declare(strict_types=1);

namespace App\Http\Controllers;

use App\Models\Module;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;

class ModuleController extends Controller
{
    public function index(): View
    {
        $modules = Module::withCount('evaluations')
            ->ordered()
            ->get();

        $semestre1 = $modules->filter(fn($m) => $m->isSemestre1());
        $semestre2 = $modules->filter(fn($m) => $m->isSemestre2());

        return view('modules.index', compact('modules', 'semestre1', 'semestre2'));
    }

    public function create(): View
    {
        return view('modules.create');
    }

    public function store(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'code' => 'required|string|max:10|unique:modules,code',
            'intitule' => 'required|string|max:100',
            'coefficient' => 'required|numeric|min:0.1|max:10',
            'ordre' => 'required|integer|min:1|max:100',
        ]);

        try {
            Module::create($validated);

            return redirect()
                ->route('modules.index')
                ->with('success', 'Module créé avec succès.');
        } catch (\Exception $e) {
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la création: ' . $e->getMessage());
        }
    }

    public function show(Module $module): View
    {
        $module->loadCount('evaluations');

        $stats = [
            'semestre' => $module->getSemestre(),
            'total_evaluations' => $module->evaluations_count,
            'moyenne_generale' => $module->evaluations()->avg('note'),
        ];

        return view('modules.show', compact('module', 'stats'));
    }

    public function edit(Module $module): View
    {
        return view('modules.edit', compact('module'));
    }

    public function update(Request $request, Module $module): RedirectResponse
    {
        $validated = $request->validate([
            'code' => 'required|string|max:10|unique:modules,code,' . $module->id,
            'intitule' => 'required|string|max:100',
            'coefficient' => 'required|numeric|min:0.1|max:10',
            'ordre' => 'required|integer|min:1|max:100',
        ]);

        try {
            $module->update($validated);

            return redirect()
                ->route('modules.index')
                ->with('success', 'Module mis à jour avec succès.');
        } catch (\Exception $e) {
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la mise à jour: ' . $e->getMessage());
        }
    }

    public function destroy(Module $module): RedirectResponse
    {
        try {
            if ($module->evaluations()->exists()) {
                return back()->with('error', 'Impossible de supprimer un module avec des évaluations.');
            }

            $module->delete();

            return redirect()
                ->route('modules.index')
                ->with('success', 'Module supprimé avec succès.');
        } catch (\Exception $e) {
            return back()->with('error', 'Erreur lors de la suppression: ' . $e->getMessage());
        }
    }
}


```



---

### SpecialiteController 

```php

<?php

declare(strict_types=1);

namespace App\Http\Controllers;

use App\Models\Specialite;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;

class SpecialiteController extends Controller
{
    public function index(Request $request): View
    {
        $query = Specialite::query();

        if ($search = $request->input('search')) {
            $query->search($search);
        }

        $specialites = $query->withCount('users')
            ->ordered()
            ->paginate(15);

        return view('specialites.index', compact('specialites'));
    }

    public function create(): View
    {
        return view('specialites.create');
    }

    public function store(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'code' => 'required|string|max:20|unique:specialites,code',
            'intitule' => 'required|string|max:100',
            'description' => 'nullable|string',
        ]);

        try {
            Specialite::create($validated);

            return redirect()
                ->route('specialites.index')
                ->with('success', 'Spécialité créée avec succès.');
        } catch (\Exception $e) {
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la création: ' . $e->getMessage());
        }
    }

    public function show(Specialite $specialite): View
    {
        $specialite->loadCount('users');
        
        $stats = [
            'total_etudiants' => $specialite->getUsersCount(),
            'etudiants_actifs' => $specialite->getActiveUsersCount(),
        ];

        $etudiants = $specialite->users()
            ->with(['anneeAcademique'])
            ->ordered()
            ->paginate(20);

        return view('specialites.show', compact('specialite', 'stats', 'etudiants'));
    }

    public function edit(Specialite $specialite): View
    {
        return view('specialites.edit', compact('specialite'));
    }

    public function update(Request $request, Specialite $specialite): RedirectResponse
    {
        $validated = $request->validate([
            'code' => 'required|string|max:20|unique:specialites,code,' . $specialite->id,
            'intitule' => 'required|string|max:100',
            'description' => 'nullable|string',
        ]);

        try {
            $specialite->update($validated);

            return redirect()
                ->route('specialites.index')
                ->with('success', 'Spécialité mise à jour avec succès.');
        } catch (\Exception $e) {
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la mise à jour: ' . $e->getMessage());
        }
    }

    public function destroy(Specialite $specialite): RedirectResponse
    {
        try {
            if ($specialite->users()->exists()) {
                return back()->with('error', 'Impossible de supprimer une spécialité avec des étudiants.');
            }

            $specialite->delete();

            return redirect()
                ->route('specialites.index')
                ->with('success', 'Spécialité supprimée avec succès.');
        } catch (\Exception $e) {
            return back()->with('error', 'Erreur lors de la suppression: ' . $e->getMessage());
        }
    }
}

```





### AnneeAcademiqueController 

```php

<?php

declare(strict_types=1);

namespace App\Http\Controllers;

use App\Models\AnneeAcademique;
use Illuminate\Http\Request;
use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use Illuminate\Support\Facades\DB;

class AnneeAcademiqueController extends Controller
{
    public function index(): View
    {
        $annees = AnneeAcademique::withCount('users')
            ->ordered()
            ->paginate(15);

        return view('annees.index', compact('annees'));
    }

    public function create(): View
    {
        return view('annees.create');
    }

    public function store(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'libelle' => 'required|string|max:20|unique:annees_academiques,libelle',
            'date_debut' => 'required|date',
            'date_fin' => 'required|date|after:date_debut',
            'is_active' => 'boolean',
        ]);

        try {
            DB::beginTransaction();

            $annee = AnneeAcademique::create($validated);

            if ($request->boolean('is_active')) {
                $annee->activate();
            }

            DB::commit();

            return redirect()
                ->route('annees.index')
                ->with('success', 'Année académique créée avec succès.');
        } catch (\Exception $e) {
            DB::rollBack();
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la création: ' . $e->getMessage());
        }
    }

    public function show(AnneeAcademique $annee): View
    {
        $annee->loadCount(['users', 'evaluations', 'bilansCompetences']);

        $stats = [
            'total_etudiants' => $annee->users_count,
            'total_evaluations' => $annee->evaluations_count,
            'total_bilans' => $annee->bilans_competences_count,
        ];

        return view('annees.show', compact('annee', 'stats'));
    }

    public function edit(AnneeAcademique $annee): View
    {
        return view('annees.edit', compact('annee'));
    }

    public function update(Request $request, AnneeAcademique $annee): RedirectResponse
    {
        $validated = $request->validate([
            'libelle' => 'required|string|max:20|unique:annees_academiques,libelle,' . $annee->id,
            'date_debut' => 'required|date',
            'date_fin' => 'required|date|after:date_debut',
            'is_active' => 'boolean',
        ]);

        try {
            DB::beginTransaction();

            $annee->update($validated);

            if ($request->boolean('is_active')) {
                $annee->activate();
            }

            DB::commit();

            return redirect()
                ->route('annees.index')
                ->with('success', 'Année académique mise à jour avec succès.');
        } catch (\Exception $e) {
            DB::rollBack();
            return back()
                ->withInput()
                ->with('error', 'Erreur lors de la mise à jour: ' . $e->getMessage());
        }
    }

    public function destroy(AnneeAcademique $annee): RedirectResponse
    {
        try {
            if ($annee->is_active) {
                return back()->with('error', 'Impossible de supprimer l\'année active.');
            }

            if ($annee->users()->exists()) {
                return back()->with('error', 'Impossible de supprimer une année avec des étudiants.');
            }

            $annee->delete();

            return redirect()
                ->route('annees.index')
                ->with('success', 'Année académique supprimée avec succès.');
        } catch (\Exception $e) {
            return back()->with('error', 'Erreur lors de la suppression: ' . $e->getMessage());
        }
    }

    public function activate(AnneeAcademique $annee): RedirectResponse
    {
        try {
            $annee->activate();

            return back()->with('success', 'Année académique activée avec succès.');
        } catch (\Exception $e) {
            return back()->with('error', 'Erreur lors de l\'activation: ' . $e->getMessage());
        }
    }
}

```


---


### BilanCompetence 

```php

<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Builder;

class BilanCompetence extends Model
{
    use HasFactory;

    protected $table = 'bilans_competences';

    protected $fillable = [
        'user_id',
        'annee_academique_id',
        'moy_eval_semestre1',
        'moy_eval_semestre2',
        'moy_evaluations',
        'moy_competences',
        'moyenne_generale',
        'observations',
    ];

    protected $casts = [
        'moy_eval_semestre1' => 'decimal:2',
        'moy_eval_semestre2' => 'decimal:2',
        'moy_evaluations' => 'decimal:2',
        'moy_competences' => 'decimal:2',
        'moyenne_generale' => 'decimal:2',
    ];

    // Relations
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function anneeAcademique(): BelongsTo
    {
        return $this->belongsTo(AnneeAcademique::class, 'annee_academique_id');
    }

    // Scopes
    public function scopeByUser(Builder $query, int $userId): Builder
    {
        return $query->where('user_id', $userId);
    }

    public function scopeByAnneeAcademique(Builder $query, int $anneeId): Builder
    {
        return $query->where('annee_academique_id', $anneeId);
    }

    // Methods
    public function calculateAndSave(?float $moyCompetences = null): bool
    {
        // Calcul des moyennes semestrielles
        $this->moy_eval_semestre1 = Evaluation::calculateMoyenneSemestre(
            $this->user_id,
            1,
            $this->annee_academique_id
        );

        $this->moy_eval_semestre2 = Evaluation::calculateMoyenneSemestre(
            $this->user_id,
            2,
            $this->annee_academique_id
        );

        // Calcul de la moyenne des évaluations (30%)
        if ($this->moy_eval_semestre1 !== null && $this->moy_eval_semestre2 !== null) {
            $moyenneEvals = ($this->moy_eval_semestre1 + $this->moy_eval_semestre2) / 2;
            $this->moy_evaluations = round($moyenneEvals, 2);
        }

        // Si la moyenne des compétences est fournie
        if ($moyCompetences !== null) {
            $this->moy_competences = round($moyCompetences, 2);
        }

        // Calcul de la moyenne générale (30% évals + 70% compétences)
        if ($this->moy_evaluations !== null && $this->moy_competences !== null) {
            $this->moyenne_generale = round(
                ($this->moy_evaluations * 0.30) + ($this->moy_competences * 0.70),
                2
            );
        }

        return $this->save();
    }

    public function getAppreciation(): string
    {
        if ($this->moyenne_generale === null) {
            return 'Non évalué';
        }

        return match (true) {
            $this->moyenne_generale >= 18 => 'Excellent',
            $this->moyenne_generale >= 16 => 'Très bien',
            $this->moyenne_generale >= 14 => 'Bien',
            $this->moyenne_generale >= 12 => 'Assez bien',
            $this->moyenne_generale >= 10 => 'Passable',
            default => 'Insuffisant',
        };
    }

    public function getMention(): string
    {
        if ($this->moyenne_generale === null) {
            return '';
        }

        return match (true) {
            $this->moyenne_generale >= 16 => 'Très Bien',
            $this->moyenne_generale >= 14 => 'Bien',
            $this->moyenne_generale >= 12 => 'Assez Bien',
            $this->moyenne_generale >= 10 => 'Passable',
            default => 'Ajourné',
        };
    }

    public function isAdmis(): bool
    {
        return $this->moyenne_generale !== null && $this->moyenne_generale >= 10;
    }
}

```


---


### Evaluation.php

```php

<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Builder;

class Evaluation extends Model
{
    use HasFactory;

    protected $table = 'evaluations';

    protected $fillable = [
        'user_id',
        'module_id',
        'annee_academique_id',
        'semestre',
        'note',
    ];

    protected $casts = [
        'note' => 'decimal:2',
        'semestre' => 'integer',
    ];

    // Relations
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function module(): BelongsTo
    {
        return $this->belongsTo(Module::class, 'module_id');
    }

    public function anneeAcademique(): BelongsTo
    {
        return $this->belongsTo(AnneeAcademique::class, 'annee_academique_id');
    }

    // Scopes
    public function scopeBySemestre(Builder $query, int $semestre): Builder
    {
        return $query->where('semestre', $semestre);
    }

    public function scopeByUser(Builder $query, int $userId): Builder
    {
        return $query->where('user_id', $userId);
    }

    public function scopeByAnneeAcademique(Builder $query, int $anneeId): Builder
    {
        return $query->where('annee_academique_id', $anneeId);
    }

    public function scopeByModule(Builder $query, int $moduleId): Builder
    {
        return $query->where('module_id', $moduleId);
    }

    // Methods
    public function isValidNote(): bool
    {
        return $this->note >= 0 && $this->note <= 20;
    }

    public function getAppreciation(): string
    {
        return match (true) {
            $this->note >= 18 => 'Excellent',
            $this->note >= 16 => 'Très bien',
            $this->note >= 14 => 'Bien',
            $this->note >= 12 => 'Assez bien',
            $this->note >= 10 => 'Passable',
            default => 'Insuffisant',
        };
    }

    public static function calculateMoyenneSemestre(int $userId, int $semestre, int $anneeId): ?float
    {
        $moyenne = self::where('user_id', $userId)
            ->where('semestre', $semestre)
            ->where('annee_academique_id', $anneeId)
            ->avg('note');

        return $moyenne ? round((float)$moyenne, 2) : null;
    }
}

```

---



### User.php 

```php
<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Relations\HasOne;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use HasFactory, Notifiable, SoftDeletes;

    protected $fillable = [
        'matricule',
        'nom',
        'prenom',
        'email',
        'password',
        'specialite_id',
        'annee_academique_id',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];

    // Relations
    public function specialite(): BelongsTo
    {
        return $this->belongsTo(Specialite::class, 'specialite_id');
    }

    public function anneeAcademique(): BelongsTo
    {
        return $this->belongsTo(AnneeAcademique::class, 'annee_academique_id');
    }

    public function evaluations(): HasMany
    {
        return $this->hasMany(Evaluation::class, 'user_id');
    }

    public function bilanCompetence(): HasOne
    {
        return $this->hasOne(BilanCompetence::class, 'user_id')
            ->where('annee_academique_id', $this->annee_academique_id);
    }

    // Scopes
    public function scopeOrdered(Builder $query): Builder
    {
        return $query->orderBy('nom', 'asc')->orderBy('prenom', 'asc');
    }

    public function scopeSearch(Builder $query, string $search): Builder
    {
        return $query->where(function ($q) use ($search) {
            $q->where('nom', 'like', "%{$search}%")
              ->orWhere('prenom', 'like', "%{$search}%")
              ->orWhere('matricule', 'like', "%{$search}%");
        });
    }

    public function scopeBySpecialite(Builder $query, int $specialiteId): Builder
    {
        return $query->where('specialite_id', $specialiteId);
    }

    public function scopeByAnneeAcademique(Builder $query, int $anneeId): Builder
    {
        return $query->where('annee_academique_id', $anneeId);
    }

    // Methods
    public function getFullName(): string
    {
        return "{$this->nom} {$this->prenom}";
    }

    public function getEvaluationsBySemestre(int $semestre): \Illuminate\Database\Eloquent\Collection
    {
        return $this->evaluations()
            ->where('semestre', $semestre)
            ->where('annee_academique_id', $this->annee_academique_id)
            ->with('module')
            ->get()
            ->sortBy('module.ordre');
    }

    public function getMoyenneSemestre(int $semestre): ?float
    {
        $evaluations = $this->evaluations()
            ->where('semestre', $semestre)
            ->where('annee_academique_id', $this->annee_academique_id)
            ->get();

        if ($evaluations->isEmpty()) {
            return null;
        }

        return round($evaluations->avg('note'), 2);
    }
}

```

---



### Module.php 

```php

<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Builder;

class Module extends Model
{
    use HasFactory;

    protected $table = 'modules';

    protected $fillable = [
        'code',
        'intitule',
        'coefficient',
        'ordre',
    ];

    protected $casts = [
        'coefficient' => 'decimal:2',
        'ordre' => 'integer',
    ];

    // Relations
    public function evaluations(): HasMany
    {
        return $this->hasMany(Evaluation::class, 'module_id');
    }

    // Scopes
    public function scopeOrdered(Builder $query): Builder
    {
        return $query->orderBy('ordre', 'asc');
    }

    public function scopeSemestre1(Builder $query): Builder
    {
        return $query->whereIn('code', ['M1', 'M2', 'M3', 'M4', 'M5']);
    }

    public function scopeSemestre2(Builder $query): Builder
    {
        return $query->whereIn('code', ['M6', 'M7', 'M8', 'M9', 'M10']);
    }

    // Methods
    public function isSemestre1(): bool
    {
        return in_array($this->code, ['M1', 'M2', 'M3', 'M4', 'M5']);
    }

    public function isSemestre2(): bool
    {
        return in_array($this->code, ['M6', 'M7', 'M8', 'M9', 'M10']);
    }

    public function getSemestre(): int
    {
        return $this->isSemestre1() ? 1 : 2;
    }
}

```


---


### Specialite.php

```php

<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Builder;

class Specialite extends Model
{
    use HasFactory;

    protected $table = 'specialites';

    protected $fillable = [
        'code',
        'intitule',
        'description',
    ];

    // Relations
    public function users(): HasMany
    {
        return $this->hasMany(User::class, 'specialite_id');
    }

    // Scopes
    public function scopeOrdered(Builder $query): Builder
    {
        return $query->orderBy('intitule', 'asc');
    }

    public function scopeSearch(Builder $query, string $search): Builder
    {
        return $query->where(function ($q) use ($search) {
            $q->where('code', 'like', "%{$search}%")
              ->orWhere('intitule', 'like', "%{$search}%");
        });
    }

    // Methods
    public function getUsersCount(): int
    {
        return $this->users()->count();
    }

    public function getActiveUsersCount(): int
    {
        return $this->users()
            ->whereHas('anneeAcademique', fn($q) => $q->where('is_active', true))
            ->count();
    }
}
```

---



### AnneeAcademique.php

```php
}<?php

declare(strict_types=1);

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Builder;

class AnneeAcademique extends Model
{
    use HasFactory;

    protected $table = 'annees_academiques';

    protected $fillable = [
        'libelle',
        'date_debut',
        'date_fin',
        'is_active',
    ];

    protected $casts = [
        'date_debut' => 'date',
        'date_fin' => 'date',
        'is_active' => 'boolean',
    ];

    // Relations
    public function users(): HasMany
    {
        return $this->hasMany(User::class, 'annee_academique_id');
    }

    public function evaluations(): HasMany
    {
        return $this->hasMany(Evaluation::class, 'annee_academique_id');
    }

    public function bilansCompetences(): HasMany
    {
        return $this->hasMany(BilanCompetence::class, 'annee_academique_id');
    }

    // Scopes
    public function scopeActive(Builder $query): Builder
    {
        return $query->where('is_active', true);
    }

    public function scopeOrdered(Builder $query): Builder
    {
        return $query->orderBy('date_debut', 'desc');
    }

    // Methods
    public function activate(): bool
    {
        // Désactiver toutes les autres années
        self::where('id', '!=', $this->id)->update(['is_active' => false]);
        
        return $this->update(['is_active' => true]);
    }

    public function isActive(): bool
    {
        return $this->is_active;
    }
}


```


---
📦 Structure créée
Models (6 fichiers)
- 1.	AnneeAcademique.php - Gestion des années académiques
- 2.	Specialite.php - Gestion des spécialités
- 3.	Module.php - Gestion des modules (M1-M10)
- 4.	User.php - Gestion des étudiants
- 5.	Evaluation.php - Gestion des notes par module
6.	BilanCompetence.php - Calcul des bilans finaux
Controllers (5 fichiers)
1.	AnneeAcademiqueController - CRUD + activation
2.	SpecialiteController - CRUD + recherche
3.	ModuleController - CRUD + séparation semestres
4.	EvaluationController - CRUD + saisie multiple + relevé de notes
5.	BilanCompetenceController - CRUD + calcul auto + tableau récap
✨ Fonctionnalités principales
Models
-	Relations Eloquent optimisées
-	Scopes réutilisables (search, ordered, active, etc.)
-	Méthodes métier (calculs, statistiques)
-	Casts automatiques des types
-	Soft deletes pour les utilisateurs
Controllers
-	Validation stricte des données
-	Transactions DB pour l'intégrité
-	Gestion d'erreurs complète
-	Pagination automatique
-	Eager loading pour éviter N+1


### 

```php
// Années académiques
Route::resource('annees', AnneeAcademiqueController::class);
Route::post('annees/{annee}/activate', [AnneeAcademiqueController::class, 'activate'])->name('annees.activate');

// Spécialités
Route::resource('specialites', SpecialiteController::class);

// Modules
Route::resource('modules', ModuleController::class);

// Évaluations
Route::resource('evaluations', EvaluationController::class);
Route::get('evaluations/saisir-multiple', [EvaluationController::class, 'saisirMultiple'])->name('evaluations.saisir-multiple');
Route::post('evaluations/store-multiple', [EvaluationController::class, 'storeMultiple'])->name('evaluations.store-multiple');
Route::get('users/{user}/releve-notes', [EvaluationController::class, 'releveNotes'])->name('evaluations.releve-notes');

// Bilans
Route::resource('bilans', BilanCompetenceController::class);
Route::post('bilans/{bilan}/recalculer', [BilanCompetenceController::class, 'recalculer'])->name('bilans.recalculer');
Route::post('bilans/generer-tous', [BilanCompetenceController::class, 'genererTous'])->name('bilans.generer-tous');
Route::get('bilans/tableau-recapitulatif', [BilanCompetenceController::class, 'tableauRecapitulatif'])->name('bilans.tableau-recapitulatif');

```





###

```php

````





###

```php

````





###

```php

````









