N°	Nom et prénoms/names and first names	Évaluations semestrielles/semester évaluations (30%)	MOY/20 Bilan des compétences (70%) skills assesment	MOY.GEN. (100%) en (rouge)
		Semestre/semester 1	Semestre 2/semestres		
		M1	M2	M3	M4	M5	MOY/20
EVAL1	M6	M7	M8	M9	M10	MOY/20
EVAL2		
1	DJOUKOUO BELVA	15	17	18	16	18		15	16	15	15	18			
2	DOMDJO DJOKO	16	17	19	18	19		17	16	18	16	17			
3	KONLACK FONING	16	17	15	16	18		18	17	18	16	17			
4	MAKAMTE SOH	16	15	17	16	17		18	17	14	18	15			
5	MOTIEGUEMWO	16	15	18	17	17		16	16	17	17	18			
6	SIGNING NGOUMTSA	16	18	17	17	19		17	18	16	17	18			
7	VIMPOU DJIKA	16	15	16	14	17		16	15	17	16	15			



Cahier des Charges - Système de Gestion des Évaluations Académiques
1. Contexte et Objectifs
1.1 Objectif Principal
Développer une application web Laravel 12 pour la gestion des évaluations semestrielles des étudiants avec calcul automatique des moyennes et suivi des compétences.
1.2 Portée
•	Gestion des années académiques
•	Gestion des spécialités
•	Gestion des utilisateurs (étudiants)
•	Saisie et calcul des évaluations
•	Génération de relevés de notes
________________________________________
2. Modèle Conceptuel de Données (MCD)
2.1 Entités Identifiées
ANNEE_ACADEMIQUE
•	id (PK)
•	libelle (ex: "2025-2026")
•	date_debut
•	date_fin
•	is_active
SPECIALITE
•	id (PK)
•	code
•	intitule
MODULE
•	id (PK)
•	code (M1, M2, ..., M10)
•	intitule
•	coefficient
UTILISATEUR (Étudiant)
•	id (PK)
•	matricule
•	nom
•	prenom
•	specialite_id (FK)
•	annee_academique_id (FK)
EVALUATION
•	id (PK)
•	utilisateur_id (FK)
•	module_id (FK)
•	semestre (1 ou 2)
•	note
•	annee_academique_id (FK)
BILAN_COMPETENCES
•	id (PK)
•	utilisateur_id (FK)
•	annee_academique_id (FK)
•	moy_eval1 (30% semestre 1)
•	moy_eval2 (30% semestre 2)
•	moy_competences (70%)
•	moyenne_generale (100%)
2.2 Relations
•	Un étudiant appartient à UNE spécialité
•	Un étudiant est inscrit pour UNE année académique
•	Un étudiant a PLUSIEURS évaluations
•	Une évaluation concerne UN module et UN étudiant
•	Un bilan de compétences est lié à UN étudiant
________________________________________
3. Migrations Laravel 12 (Optimisées)
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
________________________________________
4. Fonctionnalités Principales
4.1 Gestion des Évaluations
•	Saisie des notes par module et par semestre (M1 à M10)
•	Calcul automatique des moyennes semestrielles
•	MOY/20 EVAL1 = (M1 + M2 + M3 + M4 + M5) / 5
•	MOY/20 EVAL2 = (M6 + M7 + M8 + M9 + M10) / 5
4.2 Calcul du Bilan Final
•	Évaluations semestrielles: 30% de la note finale
•	Bilan des compétences: 70% de la note finale
•	MOY.GEN (100%) = (MOY_EVALUATIONS × 0.30) + (MOY_COMPETENCES × 0.70)
4.3 Reporting
•	Relevés de notes individuels
•	Tableaux récapitulatifs par classe/spécialité
•	Export Excel/PDF
________________________________________
5. Optimisations Techniques
5.1 Index de Base de Données
•	Index sur les clés étrangères
•	Index composites pour les requêtes fréquentes
•	Index sur les colonnes de filtrage (is_active, semestre)
5.2 Contraintes d'Intégrité
•	Contraintes UNIQUE pour éviter les doublons
•	Foreign keys avec ON DELETE appropriés
•	Soft deletes pour les utilisateurs
5.3 Performance
•	Eager loading pour réduire les requêtes N+1
•	Cache pour les données statiques (modules, spécialités)
•	Pagination pour les listes longues
________________________________________
6. Stack Technique
•	Framework: Laravel 12 (Novembre 2024)
•	PHP: 8.2+
•	Base de données: MySQL 8.0+ / PostgreSQL 15+
•	Front-end: Blade + Livewire ou Vue.js/Inertia
•	Authentification: Laravel Sanctum/Breeze
________________________________________
7. Modules Complémentaires (Optionnels)
•	Dashboard administrateur
•	Gestion des enseignants
•	Historique des modifications
•	Notifications email/SMS
•	API REST pour intégration externe
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


Voici tous les Models et Controllers optimisés pour votre système de gestion des évaluations académiques avec Laravel 12, en utilisant declare(strict_types=1) et les types de retour stricts.
📦 Structure créée
Models (6 fichiers)
1.	AnneeAcademique.php - Gestion des années académiques
2.	Specialite.php - Gestion des spécialités
3.	Module.php - Gestion des modules (M1-M10)
4.	User.php - Gestion des étudiants
5.	Evaluation.php - Gestion des notes par module
6.	BilanCompetence.php - Calcul des bilans finaux
Controllers (5 fichiers)
1.	AnneeAcademiqueController - CRUD + activation
2.	SpecialiteController - CRUD + recherche
3.	ModuleController - CRUD + séparation semestres
4.	EvaluationController - CRUD + saisie multiple + relevé de notes
5.	BilanCompetenceController - CRUD + calcul auto + tableau récap
✨ Fonctionnalités principales
Models
•	Relations Eloquent optimisées
•	Scopes réutilisables (search, ordered, active, etc.)
•	Méthodes métier (calculs, statistiques)
•	Casts automatiques des types
•	Soft deletes pour les utilisateurs
Controllers
•	Validation stricte des données
•	Transactions DB pour l'intégrité
•	Gestion d'erreurs complète
•	Pagination automatique
•	Eager loading pour éviter N+1
🎯 Routes suggérées (web.php)
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
Tous les fichiers sont prêts pour être utilisés avec vos vues Blade ! 🚀






























































































 






































































### **Correction des Exercices Théoriques sur la Programmation Orientée Objet**

---

#### **Exercice 1 : Concepts fondamentaux**
Les quatre piliers de la POO sont :
1. **Encapsulation** : Regrouper données et méthodes dans une classe et contrôler l'accès à ces données.  
   *Exemple* : Une télévision cache ses circuits internes, mais permet d'interagir via des boutons.
2. **Abstraction** : Simplifier la complexité en ne montrant que l'essentiel.  
   *Exemple* : Un volant permet de conduire sans comprendre le fonctionnement du moteur.
3. **Héritage** : Réutiliser le code d'une classe parente dans une classe enfant.  
   *Exemple* : Une voiture électrique hérite des propriétés d'une voiture classique.
4. **Polymorphisme** : Même interface, comportements différents.  
   *Exemple* : Un bouton "play" fonctionne pour musique, vidéo, etc.

---

#### **Exercice 2 : Classes et objets**
- **Classe** : Un modèle ou plan pour créer des objets.  
- **Objet** : Une instance concrète d'une classe.  
*Exemple* : Dans une bibliothèque, `Livre` est une classe, et un livre spécifique (ex : "1984") est un objet.

---

#### **Exercice 3 : Encapsulation**
Le mot-clé `private` protège les données internes d'une classe, empêchant leur modification directe depuis l'extérieur.  
*Avantages* :  
1. Contrôle des modifications via des méthodes (`setters`).  
2. Validation des données.  
3. Cohérence et sécurité accrues.

---

#### **Exercice 4 : Constructeur**
Le constructeur initialise les attributs d'un objet lors de sa création.  
*Importance* : Garantit que l'objet est dans un état valide dès le départ.

---

#### **Exercice 5 : Méthodes getter et setter**
- **Getter** : Permet de lire un attribut privé.  
- **Setter** : Permet de modifier un attribut privé avec validation.  
*Exemple de validation* : Vérifier qu'un âge est compris entre 0 et 150 dans un `setter`.

---

#### **Exercice 6 : Héritage**
L'héritage permet à une classe enfant de réutiliser les attributs et méthodes d'une classe parente.  
*Relation "EST-UN"* : Un chien **EST UN** animal, un développeur **EST UN** employé.  
*Exemple* : `Animal` (classe parente), `Chien`, `Chat`, `Oiseau` (classes enfants).

---

#### **Exercice 7 : Redéfinition de méthodes**
La redéfinition permet à une classe enfant de modifier le comportement d'une méthode héritée.  
*Mot-clé `parent::`* : Appelle la méthode parente avant d'ajouter des fonctionnalités spécifiques.  
*Exemple* : La méthode `afficher()` d'une classe `Rectangle` peut ajouter des détails spécifiques après avoir appelé `parent::afficher()`.

---

#### **Exercice 8 : Visibilité des membres**
- **Public** : Accessible partout.  
- **Protected** : Accessible dans la classe et ses enfants.  
- **Private** : Accessible uniquement dans la classe.  
*Exemple* : Un attribut `private` ne peut pas être accédé directement depuis une classe enfant ou l'extérieur.

---

#### **Exercice 9 : Composition vs Agrégation**
- **Composition** : Relation forte où l'objet contenu est détruit avec son conteneur.  
  *Exemple* : Une voiture **a un** moteur. Si la voiture est détruite, le moteur aussi.  
- **Agrégation** : Relation faible où l'objet contenu existe indépendamment.  
  *Exemple* : Une université **a des** étudiants. Si l'université ferme, les étudiants continuent d'exister.

---

#### **Exercice 10 : `$this`**
`$this` représente l'instance actuelle de l'objet.  
*Contexte d'utilisation* : Pour accéder aux attributs et méthodes de l'objet courant.  
*Essentiel* : Permet de différencier les variables locales des attributs de l'objet.

---

#### **Exercice 11 : Méthodes statiques**
Une méthode statique appartient à la classe plutôt qu'à une instance.  
*Différence* : Elle ne dépend pas d'un objet spécifique.  
*Exemple* : Une méthode `compterInstances()` qui retourne le nombre total d'instances créées.

---

#### **Exercice 12 : Polymorphisme**
Le polymorphisme permet à des objets de classes différentes d'utiliser la même interface avec des comportements spécifiques.  
*Exemple* : La méthode `calculerAire()` peut être redéfinie dans `Rectangle` et `Cercle`.  
*Avantage* : Facilite l'extensibilité en permettant d'ajouter de nouvelles classes sans modifier le code existant.

---

#### **Exercice 13 : Abstraction**
L'abstraction consiste à simplifier la complexité en ne montrant que l'essentiel.  
*Classes abstraites et interfaces* :  
- Une classe abstraite fournit une structure de base avec des méthodes abstraites.  
- Une interface définit un contrat que les classes doivent respecter.  
*Exemple* : Une classe `Forme` abstraite avec une méthode `calculerAire()`.

---

#### **Exercice 14 : Constantes de classe**
- **Constante (`const`)** : Valeur fixe accessible via la classe.  
- **Attribut statique (`static`)** : Valeur modifiable accessible via la classe.  
*Cas d'utilisation* : Utiliser `const` pour des valeurs fixes comme `PI`, et `static` pour des compteurs ou des configurations.

---

#### **Exercice 15 : PHP 8.4 et nouveautés**
1. **Property Hooks** : Permet de définir des comportements spécifiques lors de la lecture/écriture d'une propriété.  
   *Exemple* : Convertir automatiquement une chaîne en majuscules lors de la lecture.  
2. **Asymmetric Visibility** : Permet une visibilité différente pour la lecture et l'écriture.  
   *Exemple* : Une propriété `email` lisible publiquement mais modifiable uniquement via une méthode.

---

### **Résumé des Points Clés**
| Concept              | Définition                                                                 | Exemple                                      |
|----------------------|-----------------------------------------------------------------------------|---------------------------------------------|
| Encapsulation        | Protection des données via `private` et contrôle via méthodes               | `private float $solde;`                     |
| Héritage             | Réutilisation du code d'une classe parente                                  | `class Chien extends Animal {}`             |
| Polymorphisme        | Même interface, comportements différents                                   | `calculerAire()` dans `Rectangle` et `Cercle` |
| Abstraction          | Simplification de la complexité via classes abstraites et interfaces       | `abstract class Forme {}`                   |
| Méthodes statiques   | Appartenant à la classe plutôt qu'à une instance                           | `public static function compterInstances() {}` |

---

**En résumé** : Ces exercices permettent de maîtriser les concepts fondamentaux de la POO et de les appliquer dans des contextes concrets. 🚀


Correction des Exercices Théoriques sur la Programmation Orientée Objet
________________________________________
Exercice 1 : Concepts fondamentaux
Les quatre piliers de la POO sont :
1.	Encapsulation : Regrouper données et méthodes dans une classe et contrôler l'accès à ces données.
Exemple : Une télévision cache ses circuits internes, mais permet d'interagir via des boutons.
2.	Abstraction : Simplifier la complexité en ne montrant que l'essentiel.
Exemple : Un volant permet de conduire sans comprendre le fonctionnement du moteur.
3.	Héritage : Réutiliser le code d'une classe parente dans une classe enfant.
Exemple : Une voiture électrique hérite des propriétés d'une voiture classique.
4.	Polymorphisme : Même interface, comportements différents.
Exemple : Un bouton "play" fonctionne pour musique, vidéo, etc.
________________________________________
Exercice 2 : Classes et objets
•	Classe : Un modèle ou plan pour créer des objets.
•	Objet : Une instance concrète d'une classe.
Exemple : Dans une bibliothèque, Livre est une classe, et un livre spécifique (ex : "1984") est un objet.
________________________________________
Exercice 3 : Encapsulation
Le mot-clé private protège les données internes d'une classe, empêchant leur modification directe depuis l'extérieur.
Avantages :
1.	Contrôle des modifications via des méthodes (setters).
2.	Validation des données.
3.	Cohérence et sécurité accrues.
________________________________________
Exercice 4 : Constructeur
Le constructeur initialise les attributs d'un objet lors de sa création.
Importance : Garantit que l'objet est dans un état valide dès le départ.
________________________________________
Exercice 5 : Méthodes getter et setter
•	Getter : Permet de lire un attribut privé.
•	Setter : Permet de modifier un attribut privé avec validation.
Exemple de validation : Vérifier qu'un âge est compris entre 0 et 150 dans un setter.
________________________________________
Exercice 6 : Héritage
L'héritage permet à une classe enfant de réutiliser les attributs et méthodes d'une classe parente.
Relation "EST-UN" : Un chien EST UN animal, un développeur EST UN employé.
Exemple : Animal (classe parente), Chien, Chat, Oiseau (classes enfants).
________________________________________
Exercice 7 : Redéfinition de méthodes
La redéfinition permet à une classe enfant de modifier le comportement d'une méthode héritée.
Mot-clé parent:: : Appelle la méthode parente avant d'ajouter des fonctionnalités spécifiques.
Exemple : La méthode afficher() d'une classe Rectangle peut ajouter des détails spécifiques après avoir appelé parent::afficher().
________________________________________
Exercice 8 : Visibilité des membres
•	Public : Accessible partout.
•	Protected : Accessible dans la classe et ses enfants.
•	Private : Accessible uniquement dans la classe.
Exemple : Un attribut private ne peut pas être accédé directement depuis une classe enfant ou l'extérieur.
________________________________________
Exercice 9 : Composition vs Agrégation
•	Composition : Relation forte où l'objet contenu est détruit avec son conteneur.
Exemple : Une voiture a un moteur. Si la voiture est détruite, le moteur aussi.
•	Agrégation : Relation faible où l'objet contenu existe indépendamment.
Exemple : Une université a des étudiants. Si l'université ferme, les étudiants continuent d'exister.
________________________________________
Exercice 10 : $this
$this représente l'instance actuelle de l'objet.
Contexte d'utilisation : Pour accéder aux attributs et méthodes de l'objet courant.
Essentiel : Permet de différencier les variables locales des attributs de l'objet.
________________________________________
Exercice 11 : Méthodes statiques
Une méthode statique appartient à la classe plutôt qu'à une instance.
Différence : Elle ne dépend pas d'un objet spécifique.
Exemple : Une méthode compterInstances() qui retourne le nombre total d'instances créées.
________________________________________
Exercice 12 : Polymorphisme
Le polymorphisme permet à des objets de classes différentes d'utiliser la même interface avec des comportements spécifiques.
Exemple : La méthode calculerAire() peut être redéfinie dans Rectangle et Cercle.
Avantage : Facilite l'extensibilité en permettant d'ajouter de nouvelles classes sans modifier le code existant.
________________________________________
Exercice 13 : Abstraction
L'abstraction consiste à simplifier la complexité en ne montrant que l'essentiel.
Classes abstraites et interfaces :
•	Une classe abstraite fournit une structure de base avec des méthodes abstraites.
•	Une interface définit un contrat que les classes doivent respecter.
Exemple : Une classe Forme abstraite avec une méthode calculerAire().
________________________________________
Exercice 14 : Constantes de classe
•	Constante (const) : Valeur fixe accessible via la classe.
•	Attribut statique (static) : Valeur modifiable accessible via la classe.
Cas d'utilisation : Utiliser const pour des valeurs fixes comme PI, et static pour des compteurs ou des configurations.
________________________________________
Exercice 15 : PHP 8.4 et nouveautés
1.	Property Hooks : Permet de définir des comportements spécifiques lors de la lecture/écriture d'une propriété.
Exemple : Convertir automatiquement une chaîne en majuscules lors de la lecture.
2.	Asymmetric Visibility : Permet une visibilité différente pour la lecture et l'écriture.
Exemple : Une propriété email lisible publiquement mais modifiable uniquement via une méthode.
________________________________________
Résumé des Points Clés
Concept	Définition	Exemple
Encapsulation	Protection des données via private et contrôle via méthodes	private float $solde;
Héritage	Réutilisation du code d'une classe parente	class Chien extends Animal {}
Polymorphisme	Même interface, comportements différents	calculerAire() dans Rectangle et Cercle
Abstraction	Simplification de la complexité via classes abstraites et interfaces	abstract class Forme {}
Méthodes statiques	Appartenant à la classe plutôt qu'à une instance	public static function compterInstances() {}
________________________________________
En résumé : Ces exercices permettent de maîtriser les concepts fondamentaux de la POO et de les appliquer dans des contextes concrets. 🚀




### **Correction des Travaux Pratiques sur la Programmation Orientée Objet**

---

#### **TP 1 : Classe simple**
```php
<?php
declare(strict_types=1);

class Personne {
    public function __construct(
        private string $nom,
        private string $prenom,
        private int $age,
        private string $email
    ) {
        $this->setAge($age);
        $this->setEmail($email);
    }

    public function getNom(): string { return $this->nom; }
    public function setNom(string $nom): void { $this->nom = $nom; }

    public function getPrenom(): string { return $this->prenom; }
    public function setPrenom(string $prenom): void { $this->prenom = $prenom; }

    public function getAge(): int { return $this->age; }
    public function setAge(int $age): void {
        if ($age < 0) throw new InvalidArgumentException("L'âge doit être positif");
        $this->age = $age;
    }

    public function getEmail(): string { return $this->email; }
    public function setEmail(string $email): void {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException("Email invalide");
        }
        $this->email = $email;
    }
}
?>
```

---

#### **TP 2 : Méthodes métier**
```php
public function sePresenter(): string {
    return "Je m'appelle {$this->prenom} {$this->nom} et j'ai {$this->age} ans.";
}
```

---

#### **TP 3 : Système de compte bancaire**
```php
<?php
declare(strict_types=1);

class CompteBancaire {
    public function __construct(
        private string $numero,
        private string $titulaire,
        private float $solde = 0
    ) {}

    public function deposer(float $montant): void {
        if ($montant <= 0) throw new InvalidArgumentException("Montant invalide");
        $this->solde += $montant;
    }

    public function retirer(float $montant): void {
        if ($montant <= 0) throw new InvalidArgumentException("Montant invalide");
        if ($montant > $this->solde) throw new Exception("Solde insuffisant");
        $this->solde -= $montant;
    }

    public function consulterSolde(): float {
        return $this->solde;
    }
}
?>
```

---

#### **TP 4 : Héritage simple**
```php
<?php
declare(strict_types=1);

class Vehicule {
    public function __construct(
        protected string $marque,
        protected string $modele,
        protected int $annee
    ) {}

    public function afficherDetails(): void {
        echo "Marque : {$this->marque}, Modèle : {$this->modele}, Année : {$this->annee}\n";
    }
}

class Voiture extends Vehicule {
    public function __construct(
        string $marque,
        string $modele,
        int $annee,
        private int $nombrePortes
    ) {
        parent::__construct($marque, $modele, $annee);
    }

    public function afficherDetails(): void {
        parent::afficherDetails();
        echo "Nombre de portes : {$this->nombrePortes}\n";
    }
}

class Moto extends Vehicule {
    public function __construct(
        string $marque,
        string $modele,
        int $annee,
        private string $typeMoteur
    ) {
        parent::__construct($marque, $modele, $annee);
    }

    public function afficherDetails(): void {
        parent::afficherDetails();
        echo "Type de moteur : {$this->typeMoteur}\n";
    }
}
?>
```

---

#### **TP 5 : Redéfinition de méthode**
```php
public function demarrer(): void {
    echo "Le véhicule démarre.\n";
}

// Dans Voiture :
public function demarrer(): void {
    echo "La voiture démarre avec {$this->nombrePortes} portes.\n";
}

// Dans Moto :
public function demarrer(): void {
    echo "La moto démarre avec un moteur {$this->typeMoteur}.\n";
}
```

---

#### **TP 6 : Système de bibliothèque**
```php
<?php
declare(strict_types=1);

class Livre {
    public function __construct(
        private string $isbn,
        private string $titre,
        private string $auteur,
        private int $anneePublication,
        private bool $disponible = true
    ) {}

    public function emprunter(): void {
        if (!$this->disponible) throw new Exception("Livre déjà emprunté");
        $this->disponible = false;
    }

    public function retourner(): void {
        $this->disponible = true;
    }

    public function estDisponible(): bool {
        return $this->disponible;
    }
}

class Bibliotheque {
    private array $livres = [];

    public function ajouterLivre(Livre $livre): void {
        $this->livres[] = $livre;
    }

    public function emprunterLivre(string $isbn): void {
        foreach ($this->livres as $livre) {
            if ($livre->getIsbn() === $isbn) {
                $livre->emprunter();
                return;
            }
        }
        throw new Exception("Livre non trouvé");
    }

    public function afficherLivresDisponibles(): void {
        foreach ($this->livres as $livre) {
            if ($livre->estDisponible()) {
                echo "{$livre->getTitre()} par {$livre->getAuteur()}\n";
            }
        }
    }
}
?>
```

---

#### **TP 7 : Composition**
```php
<?php
declare(strict_types=1);

class Adresse {
    public function __construct(
        private string $rue,
        private string $ville,
        private string $codePostal,
        private string $pays
    ) {}

    public function afficher(): void {
        echo "{$this->rue}, {$this->codePostal} {$this->ville}, {$this->pays}\n";
    }
}

class Client {
    public function __construct(
        private string $nom,
        private string $prenom,
        private Adresse $adresse
    ) {}

    public function afficherInfos(): void {
        echo "Client : {$this->prenom} {$this->nom}\n";
        echo "Adresse : ";
        $this->adresse->afficher();
    }
}
?>
```

---

#### **TP 8 : Agrégation**
```php
<?php
declare(strict_types=1);

class Etudiant {
    public function __construct(
        private string $matricule,
        private string $nom,
        private string $prenom
    ) {}

    public function afficher(): void {
        echo "{$this->prenom} {$this->nom} (Matricule: {$this->matricule})\n";
    }
}

class Cours {
    private array $etudiants = [];

    public function inscrireEtudiant(Etudiant $etudiant): void {
        $this->etudiants[] = $etudiant;
    }

    public function afficherEtudiants(): void {
        foreach ($this->etudiants as $etudiant) {
            $etudiant->afficher();
        }
    }
}

class Universite {
    private array $cours = [];

    public function ajouterCours(Cours $cours): void {
        $this->cours[] = $cours;
    }
}
?>
```

---

#### **TP 9 : Forme géométrique**
```php
<?php
declare(strict_types=1);

abstract class Forme {
    abstract public function calculerAire(): float;
}

class Rectangle extends Forme {
    public function __construct(
        private float $largeur,
        private float $hauteur
    ) {}

    public function calculerAire(): float {
        return $this->largeur * $this->hauteur;
    }
}

class Cercle extends Forme {
    public function __construct(
        private float $rayon
    ) {}

    public function calculerAire(): float {
        return pi() * $this->rayon ** 2;
    }
}

class Triangle extends Forme {
    public function __construct(
        private float $base,
        private float $hauteur
    ) {}

    public function calculerAire(): float {
        return ($this->base * $this->hauteur) / 2;
    }
}
?>
```

---

#### **TP 10 : Interface et implémentation**
```php
<?php
declare(strict_types=1);

interface PouvantVoler {
    public function voler(): void;
}

class Oiseau implements PouvantVoler {
    public function voler(): void {
        echo "L'oiseau vole dans le ciel.\n";
    }
}

class Avion implements PouvantVoler {
    public function voler(): void {
        echo "L'avion décolle avec ses moteurs.\n";
    }
}
?>
```

---

#### **TP 11 : Gestion d'exceptions**
```php
class SoldeInsuffisantException extends Exception {}
class MontantInvalideException extends Exception {}

public function deposer(float $montant): void {
    if ($montant <= 0) throw new MontantInvalideException("Montant invalide");
    $this->solde += $montant;
}

public function retirer(float $montant): void {
    if ($montant <= 0) throw new MontantInvalideException("Montant invalide");
    if ($montant > $this->solde) throw new SoldeInsuffisantException("Solde insuffisant");
    $this->solde -= $montant;
}
```

---

#### **TP 12 : Système de paiement**
```php
<?php
declare(strict_types=1);

abstract class MoyenPaiement {
    abstract public function payer(float $montant): void;
}

class CarteBancaire extends MoyenPaiement {
    public function payer(float $montant): void {
        echo "Paiement de {$montant} FCFA par carte bancaire.\n";
    }
}

class Paypal extends MoyenPaiement {
    public function payer(float $montant): void {
        echo "Paiement de {$montant} FCFA via Paypal.\n";
    }
}

class VirementBancaire extends MoyenPaiement {
    public function payer(float $montant): void {
        echo "Paiement de {$montant} FCFA par virement bancaire.\n";
    }
}
?>
```

---

#### **TP 13 : Catalogue de produits**
```php
<?php
declare(strict_types=1);

class Produit {
    public function __construct(
        private string $reference,
        private string $nom,
        private float $prix,
        private int $stock
    ) {}

    public function getPrix(): float { return $this->prix; }
    public function getStock(): int { return $this->stock; }
}

class Categorie {
    private array $produits = [];

    public function ajouterProduit(Produit $produit): void {
        $this->produits[] = $produit;
    }

    public function calculerValeurStock(): float {
        $total = 0;
        foreach ($this->produits as $produit) {
            $total += $produit->getPrix() * $produit->getStock();
        }
        return $total;
    }
}
?>
```

---

#### **TP 14 : Système de notification**
```php
<?php
declare(strict_types=1);

interface Notification {
    public function envoyer(string $message): void;
}

class EmailNotification implements Notification {
    public function envoyer(string $message): void {
        echo "Envoi d'un email : {$message}\n";
    }
}

class SMSNotification implements Notification {
    public function envoyer(string $message): void {
        echo "Envoi d'un SMS : {$message}\n";
    }
}

class NotificationManager {
    private array $notifications = [];

    public function ajouterNotification(Notification $notification): void {
        $this->notifications[] = $notification;
    }

    public function envoyerNotifications(string $message): void {
        foreach ($this->notifications as $notification) {
            $notification->envoyer($message);
        }
    }
}
?>
```

---

#### **TP 15 : Jeu de rôle simple**
```php
<?php
declare(strict_types=1);

class Personnage {
    public function __construct(
        protected int $pointsDeVie,
        protected int $attaque,
        protected int $defense
    ) {}

    public function attaquer(Personnage $cible): void {
        $degats = $this->attaque - $cible->defense;
        $cible->recevoirDegats($degats);
    }

    public function recevoirDegats(int $degats): void {
        $this->pointsDeVie -= max(0, $degats);
        echo "Points de vie restants : {$this->pointsDeVie}\n";
    }
}

class Guerrier extends Personnage {
    public function __construct() {
        parent::__construct(100, 20, 15);
    }
}

class Mage extends Personnage {
    public function __construct() {
        parent::__construct(80, 25, 10);
    }
}
?>
```

---

#### **TP 16 : Système de fichiers**
```php
<?php
declare(strict_types=1);

abstract class ElementFichier {
    public function __construct(
        protected string $nom
    ) {}

    abstract public function afficherContenu(): void;
}

class Fichier extends ElementFichier {
    public function afficherContenu(): void {
        echo "Fichier : {$this->nom}\n";
    }
}

class Dossier extends ElementFichier {
    private array $elements = [];

    public function ajouterElement(ElementFichier $element): void {
        $this->elements[] = $element;
    }

    public function afficherContenu(): void {
        echo "Dossier : {$this->nom}\n";
        foreach ($this->elements as $element) {
            $element->afficherContenu();
        }
    }
}
?>
```

---

#### **TP 17 : Validation de formulaires**
```php
<?php
declare(strict_types=1);

class FormValidator {
    private array $regles = [];

    public function ajouterRegle(string $champ, string $regle, $parametre = null): void {
        $this->regles[$champ][] = ['regle' => $regle, 'parametre' => $parametre];
    }

    public function validate(array $data): array {
        $erreurs = [];
        foreach ($this->regles as $champ => $regles) {
            foreach ($regles as $regle) {
                $valeur = $data[$champ] ?? null;
                if (!$this->appliquerRegle($valeur, $regle['regle'], $regle['parametre'])) {
                    $erreurs[$champ][] = "Le champ {$champ} ne respecte pas la règle {$regle['regle']}";
                }
            }
        }
        return $erreurs;
    }

    private function appliquerRegle($valeur, string $regle, $parametre): bool {
        switch ($regle) {
            case 'required': return !empty($valeur);
            case 'email': return filter_var($valeur, FILTER_VALIDATE_EMAIL);
            case 'min_length': return strlen($valeur) >= $parametre;
            case 'max_value': return $valeur <= $parametre;
            default: return true;
        }
    }
}
?>
```

---

#### **TP 18 : Gestion de configuration**
```php
<?php
declare(strict_types=1);

class Configuration {
    private array $config = [];

    public function chargerDepuisFichier(string $fichier): void {
        $contenu = file_get_contents($fichier);
        $this->config = json_decode($contenu, true);
    }

    public function sauvegarderDansFichier(string $fichier): void {
        $contenu = json_encode($this->config);
        file_put_contents($fichier, $contenu);
    }

    public function obtenirValeur(string $cle) {
        return $this->config[$cle] ?? null;
    }

    public function definirValeur(string $cle, $valeur): void {
        $this->config[$cle] = $valeur;
    }
}
?>
```

---

#### **TP 19 : Générateur de rapports**
```php
<?php
declare(strict_types=1);

abstract class GenerateurRapport {
    abstract public function generer(array $donnees): string;
}

class PDFGenerator extends GenerateurRapport {
    public function generer(array $donnees): string {
        return "Génération d'un PDF avec les données : " . json_encode($donnees);
    }
}

class CSVGenerator extends GenerateurRapport {
    public function generer(array $donnees): string {
        return "Génération d'un CSV avec les données : " . json_encode($donnees);
    }
}

class HTMLGenerator extends GenerateurRapport {
    public function generer(array $donnees): string {
        return "Génération d'un HTML avec les données : " . json_encode($donnees);
    }
}
?>
```

---

#### **TP 20 : Application e-commerce complète**
```php
<?php
declare(strict_types=1);

class Produit {
    public function __construct(
        private string $reference,
        private string $nom,
        private string $description,
        private float $prix,
        private int $stock
    ) {}

    public function getPrix(): float { return $this->prix; }
}

class Client {
    public function __construct(
        private int $id,
        private string $nom,
        private string $email,
        private string $adresse
    ) {}
}

class Panier {
    private array $produits = [];

    public function __construct(
        private Client $client
    ) {}

    public function ajouterProduit(Produit $produit): void {
        $this->produits[] = $produit;
    }

    public function calculerTotal(): float {
        $total = 0;
        foreach ($this->produits as $produit) {
            $total += $produit->getPrix();
        }
        return $total;
    }
}

class Commande {
    public function __construct(
        private string $numero,
        private Client $client,
        private Panier $panier,
        private DateTime $date,
        private string $statut
    ) {}

    public function afficherDetails(): void {
        echo "Commande N° : {$this->numero}\n";
        echo "Client : {$this->client->getNom()}\n";
        echo "Total : {$this->panier->calculerTotal()} FCFA\n";
        echo "Statut : {$this->statut}\n";
    }
}
?>
```

---

### **Résumé des Points Clés**
| Concept              | Définition                                                                 | Exemple                                      |
|----------------------|-----------------------------------------------------------------------------|---------------------------------------------|
| Héritage             | Réutilisation du code d'une classe parente                                  | `class Voiture extends Vehicule {}`         |
| Composition          | Relation forte où l'objet contenu est détruit avec son conteneur            | `class Client { private Adresse $adresse; }`|
| Polymorphisme        | Même interface, comportements différents                                   | `interface PouvantVoler {}`                 |
| Gestion d'exceptions | Gestion des erreurs avec des exceptions personnalisées                     | `class SoldeInsuffisantException extends Exception {}` |

---

**En résumé** : Ces TP permettent de mettre en pratique les concepts fondamentaux de la POO dans des contextes variés et concrets. 🚀


Correction des Travaux Pratiques sur la Programmation Orientée Objet
________________________________________
TP 1 : Classe simple
Copier
<?php
declare(strict_types=1);

class Personne {
    public function __construct(
        private string $nom,
        private string $prenom,
        private int $age,
        private string $email
    ) {
        $this->setAge($age);
        $this->setEmail($email);
    }

    public function getNom(): string { return $this->nom; }
    public function setNom(string $nom): void { $this->nom = $nom; }

    public function getPrenom(): string { return $this->prenom; }
    public function setPrenom(string $prenom): void { $this->prenom = $prenom; }

    public function getAge(): int { return $this->age; }
    public function setAge(int $age): void {
        if ($age < 0) throw new InvalidArgumentException("L'âge doit être positif");
        $this->age = $age;
    }

    public function getEmail(): string { return $this->email; }
    public function setEmail(string $email): void {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException("Email invalide");
        }
        $this->email = $email;
    }
}
?>
________________________________________

TP 2 : Méthodes métier
public function sePresenter(): string {
    return "Je m'appelle {$this->prenom} {$this->nom} et j'ai {$this->age} ans.";
}
________________________________________
TP 3 : Système de compte bancaire
Copier
<?php
declare(strict_types=1);

class CompteBancaire {
    public function __construct(
        private string $numero,
        private string $titulaire,
        private float $solde = 0
    ) {}

    public function deposer(float $montant): void {
        if ($montant <= 0) throw new InvalidArgumentException("Montant invalide");
        $this->solde += $montant;
    }

    public function retirer(float $montant): void {
        if ($montant <= 0) throw new InvalidArgumentException("Montant invalide");
        if ($montant > $this->solde) throw new Exception("Solde insuffisant");
        $this->solde -= $montant;
    }

    public function consulterSolde(): float {
        return $this->solde;
    }
}
?>
________________________________________
TP 4 : Héritage simple
Copier
<?php
declare(strict_types=1);

class Vehicule {
    public function __construct(
        protected string $marque,
        protected string $modele,
        protected int $annee
    ) {}

    public function afficherDetails(): void {
        echo "Marque : {$this->marque}, Modèle : {$this->modele}, Année : {$this->annee}\n";
    }
}

class Voiture extends Vehicule {
    public function __construct(
        string $marque,
        string $modele,
        int $annee,
        private int $nombrePortes
    ) {
        parent::__construct($marque, $modele, $annee);
    }

    public function afficherDetails(): void {
        parent::afficherDetails();
        echo "Nombre de portes : {$this->nombrePortes}\n";
    }
}

class Moto extends Vehicule {
    public function __construct(
        string $marque,
        string $modele,
        int $annee,
        private string $typeMoteur
    ) {
        parent::__construct($marque, $modele, $annee);
    }

    public function afficherDetails(): void {
        parent::afficherDetails();
        echo "Type de moteur : {$this->typeMoteur}\n";
    }
}
?>
________________________________________
TP 5 : Redéfinition de méthode
Copier
public function demarrer(): void {
    echo "Le véhicule démarre.\n";
}

// Dans Voiture :
public function demarrer(): void {
    echo "La voiture démarre avec {$this->nombrePortes} portes.\n";
}

// Dans Moto :
public function demarrer(): void {
    echo "La moto démarre avec un moteur {$this->typeMoteur}.\n";
}
________________________________________
TP 6 : Système de bibliothèque
Copier
<?php
declare(strict_types=1);

class Livre {
    public function __construct(
        private string $isbn,
        private string $titre,
        private string $auteur,
        private int $anneePublication,
        private bool $disponible = true
    ) {}

    public function emprunter(): void {
        if (!$this->disponible) throw new Exception("Livre déjà emprunté");
        $this->disponible = false;
    }

    public function retourner(): void {
        $this->disponible = true;
    }

    public function estDisponible(): bool {
        return $this->disponible;
    }
}

class Bibliotheque {
    private array $livres = [];

    public function ajouterLivre(Livre $livre): void {
        $this->livres[] = $livre;
    }

    public function emprunterLivre(string $isbn): void {
        foreach ($this->livres as $livre) {
            if ($livre->getIsbn() === $isbn) {
                $livre->emprunter();
                return;
            }
        }
        throw new Exception("Livre non trouvé");
    }

    public function afficherLivresDisponibles(): void {
        foreach ($this->livres as $livre) {
            if ($livre->estDisponible()) {
                echo "{$livre->getTitre()} par {$livre->getAuteur()}\n";
            }
        }
    }
}
?>
________________________________________
TP 7 : Composition
Copier
<?php
declare(strict_types=1);

class Adresse {
    public function __construct(
        private string $rue,
        private string $ville,
        private string $codePostal,
        private string $pays
    ) {}

    public function afficher(): void {
        echo "{$this->rue}, {$this->codePostal} {$this->ville}, {$this->pays}\n";
    }
}

class Client {
    public function __construct(
        private string $nom,
        private string $prenom,
        private Adresse $adresse
    ) {}

    public function afficherInfos(): void {
        echo "Client : {$this->prenom} {$this->nom}\n";
        echo "Adresse : ";
        $this->adresse->afficher();
    }
}
?>
________________________________________
TP 8 : Agrégation
Copier
<?php
declare(strict_types=1);

class Etudiant {
    public function __construct(
        private string $matricule,
        private string $nom,
        private string $prenom
    ) {}

    public function afficher(): void {
        echo "{$this->prenom} {$this->nom} (Matricule: {$this->matricule})\n";
    }
}

class Cours {
    private array $etudiants = [];

    public function inscrireEtudiant(Etudiant $etudiant): void {
        $this->etudiants[] = $etudiant;
    }

    public function afficherEtudiants(): void {
        foreach ($this->etudiants as $etudiant) {
            $etudiant->afficher();
        }
    }
}

class Universite {
    private array $cours = [];

    public function ajouterCours(Cours $cours): void {
        $this->cours[] = $cours;
    }
}
?>
________________________________________
TP 9 : Forme géométrique
Copier
<?php
declare(strict_types=1);

abstract class Forme {
    abstract public function calculerAire(): float;
}

class Rectangle extends Forme {
    public function __construct(
        private float $largeur,
        private float $hauteur
    ) {}

    public function calculerAire(): float {
        return $this->largeur * $this->hauteur;
    }
}

class Cercle extends Forme {
    public function __construct(
        private float $rayon
    ) {}

    public function calculerAire(): float {
        return pi() * $this->rayon ** 2;
    }
}

class Triangle extends Forme {
    public function __construct(
        private float $base,
        private float $hauteur
    ) {}

    public function calculerAire(): float {
        return ($this->base * $this->hauteur) / 2;
    }
}
?>
________________________________________
TP 10 : Interface et implémentation
Copier
<?php
declare(strict_types=1);

interface PouvantVoler {
    public function voler(): void;
}

class Oiseau implements PouvantVoler {
    public function voler(): void {
        echo "L'oiseau vole dans le ciel.\n";
    }
}

class Avion implements PouvantVoler {
    public function voler(): void {
        echo "L'avion décolle avec ses moteurs.\n";
    }
}
?>
________________________________________
TP 11 : Gestion d'exceptions
Copier
class SoldeInsuffisantException extends Exception {}
class MontantInvalideException extends Exception {}

public function deposer(float $montant): void {
    if ($montant <= 0) throw new MontantInvalideException("Montant invalide");
    $this->solde += $montant;
}

public function retirer(float $montant): void {
    if ($montant <= 0) throw new MontantInvalideException("Montant invalide");
    if ($montant > $this->solde) throw new SoldeInsuffisantException("Solde insuffisant");
    $this->solde -= $montant;
}
________________________________________
TP 12 : Système de paiement
Copier
<?php
declare(strict_types=1);

abstract class MoyenPaiement {
    abstract public function payer(float $montant): void;
}

class CarteBancaire extends MoyenPaiement {
    public function payer(float $montant): void {
        echo "Paiement de {$montant} FCFA par carte bancaire.\n";
    }
}

class Paypal extends MoyenPaiement {
    public function payer(float $montant): void {
        echo "Paiement de {$montant} FCFA via Paypal.\n";
    }
}

class VirementBancaire extends MoyenPaiement {
    public function payer(float $montant): void {
        echo "Paiement de {$montant} FCFA par virement bancaire.\n";
    }
}
?>
________________________________________
TP 13 : Catalogue de produits
Copier
<?php
declare(strict_types=1);

class Produit {
    public function __construct(
        private string $reference,
        private string $nom,
        private float $prix,
        private int $stock
    ) {}

    public function getPrix(): float { return $this->prix; }
    public function getStock(): int { return $this->stock; }
}

class Categorie {
    private array $produits = [];

    public function ajouterProduit(Produit $produit): void {
        $this->produits[] = $produit;
    }

    public function calculerValeurStock(): float {
        $total = 0;
        foreach ($this->produits as $produit) {
            $total += $produit->getPrix() * $produit->getStock();
        }
        return $total;
    }
}
?>
________________________________________
TP 14 : Système de notification
Copier
<?php
declare(strict_types=1);

interface Notification {
    public function envoyer(string $message): void;
}

class EmailNotification implements Notification {
    public function envoyer(string $message): void {
        echo "Envoi d'un email : {$message}\n";
    }
}

class SMSNotification implements Notification {
    public function envoyer(string $message): void {
        echo "Envoi d'un SMS : {$message}\n";
    }
}

class NotificationManager {
    private array $notifications = [];

    public function ajouterNotification(Notification $notification): void {
        $this->notifications[] = $notification;
    }

    public function envoyerNotifications(string $message): void {
        foreach ($this->notifications as $notification) {
            $notification->envoyer($message);
        }
    }
}
?>
________________________________________
TP 15 : Jeu de rôle simple
Copier
<?php
declare(strict_types=1);

class Personnage {
    public function __construct(
        protected int $pointsDeVie,
        protected int $attaque,
        protected int $defense
    ) {}

    public function attaquer(Personnage $cible): void {
        $degats = $this->attaque - $cible->defense;
        $cible->recevoirDegats($degats);
    }

    public function recevoirDegats(int $degats): void {
        $this->pointsDeVie -= max(0, $degats);
        echo "Points de vie restants : {$this->pointsDeVie}\n";
    }
}

class Guerrier extends Personnage {
    public function __construct() {
        parent::__construct(100, 20, 15);
    }
}

class Mage extends Personnage {
    public function __construct() {
        parent::__construct(80, 25, 10);
    }
}
?>
________________________________________
TP 16 : Système de fichiers
Copier
<?php
declare(strict_types=1);

abstract class ElementFichier {
    public function __construct(
        protected string $nom
    ) {}

    abstract public function afficherContenu(): void;
}

class Fichier extends ElementFichier {
    public function afficherContenu(): void {
        echo "Fichier : {$this->nom}\n";
    }
}

class Dossier extends ElementFichier {
    private array $elements = [];

    public function ajouterElement(ElementFichier $element): void {
        $this->elements[] = $element;
    }

    public function afficherContenu(): void {
        echo "Dossier : {$this->nom}\n";
        foreach ($this->elements as $element) {
            $element->afficherContenu();
        }
    }
}
?>
________________________________________
TP 17 : Validation de formulaires
Copier
<?php
declare(strict_types=1);

class FormValidator {
    private array $regles = [];

    public function ajouterRegle(string $champ, string $regle, $parametre = null): void {
        $this->regles[$champ][] = ['regle' => $regle, 'parametre' => $parametre];
    }

    public function validate(array $data): array {
        $erreurs = [];
        foreach ($this->regles as $champ => $regles) {
            foreach ($regles as $regle) {
                $valeur = $data[$champ] ?? null;
                if (!$this->appliquerRegle($valeur, $regle['regle'], $regle['parametre'])) {
                    $erreurs[$champ][] = "Le champ {$champ} ne respecte pas la règle {$regle['regle']}";
                }
            }
        }
        return $erreurs;
    }

    private function appliquerRegle($valeur, string $regle, $parametre): bool {
        switch ($regle) {
            case 'required': return !empty($valeur);
            case 'email': return filter_var($valeur, FILTER_VALIDATE_EMAIL);
            case 'min_length': return strlen($valeur) >= $parametre;
            case 'max_value': return $valeur <= $parametre;
            default: return true;
        }
    }
}
?>
________________________________________
TP 18 : Gestion de configuration
Copier
<?php
declare(strict_types=1);

class Configuration {
    private array $config = [];

    public function chargerDepuisFichier(string $fichier): void {
        $contenu = file_get_contents($fichier);
        $this->config = json_decode($contenu, true);
    }

    public function sauvegarderDansFichier(string $fichier): void {
        $contenu = json_encode($this->config);
        file_put_contents($fichier, $contenu);
    }

    public function obtenirValeur(string $cle) {
        return $this->config[$cle] ?? null;
    }

    public function definirValeur(string $cle, $valeur): void {
        $this->config[$cle] = $valeur;
    }
}
?>
________________________________________
TP 19 : Générateur de rapports
Copier
<?php
declare(strict_types=1);

abstract class GenerateurRapport {
    abstract public function generer(array $donnees): string;
}

class PDFGenerator extends GenerateurRapport {
    public function generer(array $donnees): string {
        return "Génération d'un PDF avec les données : " . json_encode($donnees);
    }
}

class CSVGenerator extends GenerateurRapport {
    public function generer(array $donnees): string {
        return "Génération d'un CSV avec les données : " . json_encode($donnees);
    }
}

class HTMLGenerator extends GenerateurRapport {
    public function generer(array $donnees): string {
        return "Génération d'un HTML avec les données : " . json_encode($donnees);
    }
}
?>
________________________________________
TP 20 : Application e-commerce complète
Copier
<?php
declare(strict_types=1);

class Produit {
    public function __construct(
        private string $reference,
        private string $nom,
        private string $description,
        private float $prix,
        private int $stock
    ) {}

    public function getPrix(): float { return $this->prix; }
}

class Client {
    public function __construct(
        private int $id,
        private string $nom,
        private string $email,
        private string $adresse
    ) {}
}

class Panier {
    private array $produits = [];

    public function __construct(
        private Client $client
    ) {}

    public function ajouterProduit(Produit $produit): void {
        $this->produits[] = $produit;
    }

    public function calculerTotal(): float {
        $total = 0;
        foreach ($this->produits as $produit) {
            $total += $produit->getPrix();
        }
        return $total;
    }
}

class Commande {
    public function __construct(
        private string $numero,
        private Client $client,
        private Panier $panier,
        private DateTime $date,
        private string $statut
    ) {}

    public function afficherDetails(): void {
        echo "Commande N° : {$this->numero}\n";
        echo "Client : {$this->client->getNom()}\n";
        echo "Total : {$this->panier->calculerTotal()} FCFA\n";
        echo "Statut : {$this->statut}\n";
    }
}
?>
________________________________________
Résumé des Points Clés
Concept	Définition	Exemple
Héritage	Réutilisation du code d'une classe parente	class Voiture extends Vehicule {}
Composition	Relation forte où l'objet contenu est détruit avec son conteneur	class Client { private Adresse $adresse; }
Polymorphisme	Même interface, comportements différents	interface PouvantVoler {}
Gestion d'exceptions	Gestion des erreurs avec des exceptions personnalisées	class SoldeInsuffisantException extends Exception {}
________________________________________
En résumé : Ces TP permettent de mettre en pratique les concepts fondamentaux de la POO dans des contextes variés et concrets. 🚀











































Correction des Exercices Théoriques et Travaux Pratiques sur la Programmation Orientée Objet
________________________________________
Exercices Théoriques
1. Définir la POO
La Programmation Orientée Objet (POO) est un paradigme de programmation qui organise le code autour des objets, qui sont des instances de classes. Elle permet de structurer le code en regroupant les données (attributs) et les comportements (méthodes) dans des entités logiques.
Exemple : Une classe Voiture peut avoir des attributs comme marque, modèle et vitesse, ainsi que des méthodes comme démarrer() ou accélérer().
________________________________________
2. Les 4 Piliers de la POO
1.	Encapsulation : Regrouper données et méthodes dans une classe et contrôler l'accès à ces données.
Exemple : Une télévision cache ses circuits internes, mais permet d'interagir via des boutons.
2.	Abstraction : Simplifier la complexité en ne montrant que l'essentiel.
Exemple : Un volant permet de conduire sans comprendre le fonctionnement du moteur.
3.	Héritage : Réutiliser le code d'une classe parente dans une classe enfant.
Exemple : Une voiture électrique hérite des propriétés d'une voiture classique.
4.	Polymorphisme : Même interface, comportements différents.
Exemple : Un bouton "play" fonctionne pour musique, vidéo, etc.
________________________________________
3. Classe vs Objet
•	Classe : Un modèle ou plan pour créer des objets.
•	Objet : Une instance concrète d'une classe.
Exemple : Voiture est une classe, et une Tesla Model 3 est un objet.
________________________________________
4. Encapsulation
L'encapsulation protège les données internes d'une classe, empêchant leur modification directe depuis l'extérieur.
Exemple de code PHP :
Copier
class Voiture {
    private string $marque;
    public function setMarque(string $marque): void {
        $this->marque = $marque;
    }
    public function getMarque(): string {
        return $this->marque;
    }
}
________________________________________
5. Héritage
L'héritage permet à une classe enfant de réutiliser les attributs et méthodes d'une classe parente.
Exemple de code PHP :
Copier
class Vehicule {
    protected string $marque;
}
class Voiture extends Vehicule {
    public function afficherMarque(): void {
        echo $this->marque;
    }
}
________________________________________
6. Polymorphisme
Le polymorphisme permet à des objets de classes différentes d'utiliser la même interface avec des comportements spécifiques.
Exemple de code PHP :
Copier
interface Roulant {
    public function rouler(): void;
}
class Voiture implements Roulant {
    public function rouler(): void {
        echo "La voiture roule.";
    }
}
________________________________________
7. Constructeur
Le constructeur initialise les attributs d'un objet lors de sa création.
Exemple de code PHP :
Copier
class Voiture {
    public function __construct(private string $marque) {}
}
________________________________________
8. Getters et Setters
Les getters et setters permettent de contrôler l'accès aux attributs privés.
Exemple de code PHP :
Copier
class Voiture {
    private string $marque;
    public function getMarque(): string {
        return $this->marque;
    }
    public function setMarque(string $marque): void {
        $this->marque = $marque;
    }
}
________________________________________
9. Visibilité des Propriétés
•	Public : Accessible partout.
•	Protected : Accessible dans la classe et ses enfants.
•	Private : Accessible uniquement dans la classe.
Exemple : Un attribut private ne peut pas être accédé directement depuis une classe enfant ou l'extérieur.
________________________________________
10. Composition vs Agrégation
•	Composition : Relation forte où l'objet contenu est détruit avec son conteneur.
Exemple : Une voiture a un moteur. Si la voiture est détruite, le moteur aussi.
•	Agrégation : Relation faible où l'objet contenu existe indépendamment.
Exemple : Une université a des étudiants. Si l'université ferme, les étudiants continuent d'exister.
________________________________________
11. Abstraction
L'abstraction consiste à simplifier la complexité en ne montrant que l'essentiel.
Exemple de code PHP :
Copier
abstract class Forme {
    abstract public function calculerAire(): float;
}
class Rectangle extends Forme {
    public function calculerAire(): float {
        return 10 * 5;
    }
}
________________________________________
12. Redéfinition de Méthodes
La redéfinition permet à une classe enfant de modifier le comportement d'une méthode héritée.
Exemple de code PHP :
Copier
class Vehicule {
    public function demarrer(): void {
        echo "Le véhicule démarre.";
    }
}
class Voiture extends Vehicule {
    public function demarrer(): void {
        echo "La voiture démarre rapidement.";
    }
}
________________________________________
13. Interface vs Classe Abstraite
•	Interface : Définit un contrat que les classes doivent respecter.
Exemple : interface Roulant { public function rouler(): void; }
•	Classe Abstraite : Fournit une structure de base avec des méthodes abstraites.
Exemple : abstract class Animal { abstract public function son(): void; }
________________________________________
14. Design Patterns
1.	Singleton : Garantit qu'une classe n'a qu'une seule instance.
2.	Factory : Crée des objets sans spécifier la classe exacte.
3.	Observer : Permet à un objet de notifier d'autres objets lors d'un événement.
________________________________________
15. Avantages de la POO
•	Modularité : Code facile à organiser et à réutiliser.
•	Maintenabilité : Modifications locales sans affecter l'ensemble du système.
•	Extensibilité : Ajout de nouvelles fonctionnalités sans modifier le code existant.
•	Sécurité : Encapsulation protège les données internes.
________________________________________
Travaux Pratiques
TP 1 : Création de Classe
Copier
class Voiture {
    public function __construct(
        private string $marque,
        private string $modele,
        private int $vitesse = 0
    ) {}

    public function demarrer(): void {
        echo "La voiture démarre.";
    }

    public function accelerer(int $vitesse): void {
        $this->vitesse += $vitesse;
    }

    public function freiner(): void {
        $this->vitesse = 0;
    }
}
________________________________________
TP 2 : Encapsulation
Copier
class Voiture {
    private string $marque;
    private string $modele;
    private int $vitesse = 0;

    public function setMarque(string $marque): void {
        $this->marque = $marque;
    }
    public function getMarque(): string {
        return $this->marque;
    }
}
________________________________________
TP 3 : Héritage
Copier
class VoitureElectrique extends Voiture {
    public function __construct(
        string $marque,
        string $modele,
        private int $autonomie
    ) {
        parent::__construct($marque, $modele);
    }
}
________________________________________
TP 4 : Constructeur
Copier
class Voiture {
    public function __construct(
        private string $marque,
        private string $modele,
        private int $vitesse = 0
    ) {}
}
________________________________________
TP 5 : Polymorphisme
Copier
class Voiture {
    public function afficherDetails(): void {
        echo "Marque : {$this->marque}, Modèle : {$this->modele}";
    }
}
class VoitureElectrique extends Voiture {
    public function afficherDetails(): void {
        parent::afficherDetails();
        echo ", Autonomie : {$this->autonomie} km";
    }
}
________________________________________
TP 6 : Composition
Copier
class Moteur {
    public function __construct(private string $type) {}
}
class Voiture {
    public function __construct(private Moteur $moteur) {}
}
________________________________________
TP 7 : Agrégation
Copier
class Garage {
    private array $voitures = [];
    public function ajouterVoiture(Voiture $voiture): void {
        $this->voitures[] = $voiture;
    }
}
________________________________________
TP 8 : Interface
Copier
interface Roulant {
    public function rouler(): void;
}
class Voiture implements Roulant {
    public function rouler(): void {
        echo "La voiture roule.";
    }
}
________________________________________
TP 9 : Classe Abstraite
Copier
abstract class Animal {
    abstract public function son(): void;
}
class Chien extends Animal {
    public function son(): void {
        echo "Woof!";
    }
}
________________________________________
TP 10 : Design Pattern Singleton
Copier
class Configuration {
    private static ?self $instance = null;
    private function __construct() {}
    public static function getInstance(): self {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
________________________________________
TP 11 : Design Pattern Factory
Copier
class VoitureFactory {
    public static function creerVoiture(string $type): Voiture {
        return match ($type) {
            'electrique' => new VoitureElectrique(),
            default => new Voiture(),
        };
    }
}
________________________________________
TP 12 : Design Pattern Observer
Copier
class Sujet {
    private array $observateurs = [];
    public function notifier(): void {
        foreach ($this->observateurs as $observateur) {
            $observateur->actualiser();
        }
    }
}
________________________________________
TP 13 : Gestion d'Employés
Copier
class Employe {
    public function __construct(
        private string $nom,
        private string $poste,
        private float $salaire
    ) {}

    public function augmenterSalaire(float $montant): void {
        $this->salaire += $montant;
    }
}
________________________________________
TP 14 : Système Bancaire
Copier
class CompteBancaire {
    public function __construct(private float $solde = 0) {}

    public function deposer(float $montant): void {
        $this->solde += $montant;
    }

    public function retirer(float $montant): void {
        $this->solde -= $montant;
    }
}
________________________________________
TP 15 : Gestion de Bibliothèque
Copier
class Livre {
    public function __construct(
        private string $titre,
        private string $auteur,
        private bool $disponible = true
    ) {}

    public function emprunter(): void {
        $this->disponible = false;
    }
}
________________________________________
TP 16 : Calculatrice
Copier
class Calculatrice {
    public function additionner(float $a, float $b): float {
        return $a + $b;
    }
}
________________________________________
TP 17 : Gestion de Stock
Copier
class Produit {
    public function __construct(
        private string $nom,
        private float $prix,
        private int $stock
    ) {}

    public function vendre(int $quantite): void {
        $this->stock -= $quantite;
    }
}
________________________________________
TP 18 : Système de Réservation
Copier
class Reservation {
    public function __construct(
        private string $client,
        private DateTime $date,
        private string $statut = 'en attente'
    ) {}

    public function confirmer(): void {
        $this->statut = 'confirmée';
    }
}
________________________________________
TP 19 : Gestion de Cours
Copier
class Cours {
    public function __construct(
        private string $titre,
        private string $enseignant,
        private array $etudiants = []
    ) {}

    public function ajouterEtudiant(string $etudiant): void {
        $this->etudiants[] = $etudiant;
    }
}
________________________________________
TP 20 : Système de Notation
Copier
class Etudiant {
    public function __construct(
        private string $nom,
        private array $notes = []
    ) {}

    public function calculerMoyenne(): float {
        return array_sum($this->notes) / count($this->notes);
    }
}
________________________________________
Résumé des Points Clés
Concept	Définition	Exemple
Encapsulation	Protection des données via private et contrôle via méthodes	private string $marque;
Héritage	Réutilisation du code d'une classe parente	class VoitureElectrique extends Voiture {}
Polymorphisme	Même interface, comportements différents	interface Roulant {}
Abstraction	Simplification de la complexité via classes abstraites et interfaces	abstract class Animal {}
________________________________________
En résumé : Ces TP permettent de mettre en pratique les concepts fondamentaux de la POO dans des contextes variés et concrets. 🚀

### **Correction des Exercices Théoriques et Travaux Pratiques sur la Programmation Orientée Objet**

---

### **Exercices Théoriques**

#### **1. Définir la POO**
La **Programmation Orientée Objet (POO)** est un paradigme de programmation qui organise le code autour des objets, qui sont des instances de classes. Elle permet de structurer le code en regroupant les données (attributs) et les comportements (méthodes) dans des entités logiques.  
*Exemple* : Une classe `Voiture` peut avoir des attributs comme `marque`, `modèle` et `vitesse`, ainsi que des méthodes comme `démarrer()` ou `accélérer()`.

---

#### **2. Les 4 Piliers de la POO**
1. **Encapsulation** : Regrouper données et méthodes dans une classe et contrôler l'accès à ces données.  
   *Exemple* : Une télévision cache ses circuits internes, mais permet d'interagir via des boutons.
2. **Abstraction** : Simplifier la complexité en ne montrant que l'essentiel.  
   *Exemple* : Un volant permet de conduire sans comprendre le fonctionnement du moteur.
3. **Héritage** : Réutiliser le code d'une classe parente dans une classe enfant.  
   *Exemple* : Une voiture électrique hérite des propriétés d'une voiture classique.
4. **Polymorphisme** : Même interface, comportements différents.  
   *Exemple* : Un bouton "play" fonctionne pour musique, vidéo, etc.

---

#### **3. Classe vs Objet**
- **Classe** : Un modèle ou plan pour créer des objets.  
- **Objet** : Une instance concrète d'une classe.  
*Exemple* : `Voiture` est une classe, et une `Tesla Model 3` est un objet.

---

#### **4. Encapsulation**
L'encapsulation protège les données internes d'une classe, empêchant leur modification directe depuis l'extérieur.  
*Exemple de code PHP* :
```php
class Voiture {
    private string $marque;
    public function setMarque(string $marque): void {
        $this->marque = $marque;
    }
    public function getMarque(): string {
        return $this->marque;
    }
}
```

---

#### **5. Héritage**
L'héritage permet à une classe enfant de réutiliser les attributs et méthodes d'une classe parente.  
*Exemple de code PHP* :
```php
class Vehicule {
    protected string $marque;
}
class Voiture extends Vehicule {
    public function afficherMarque(): void {
        echo $this->marque;
    }
}
```

---

#### **6. Polymorphisme**
Le polymorphisme permet à des objets de classes différentes d'utiliser la même interface avec des comportements spécifiques.  
*Exemple de code PHP* :
```php
interface Roulant {
    public function rouler(): void;
}
class Voiture implements Roulant {
    public function rouler(): void {
        echo "La voiture roule.";
    }
}
```

---

#### **7. Constructeur**
Le constructeur initialise les attributs d'un objet lors de sa création.  
*Exemple de code PHP* :
```php
class Voiture {
    public function __construct(private string $marque) {}
}
```

---

#### **8. Getters et Setters**
Les getters et setters permettent de contrôler l'accès aux attributs privés.  
*Exemple de code PHP* :
```php
class Voiture {
    private string $marque;
    public function getMarque(): string {
        return $this->marque;
    }
    public function setMarque(string $marque): void {
        $this->marque = $marque;
    }
}
```

---

#### **9. Visibilité des Propriétés**
- **Public** : Accessible partout.  
- **Protected** : Accessible dans la classe et ses enfants.  
- **Private** : Accessible uniquement dans la classe.  
*Exemple* : Un attribut `private` ne peut pas être accédé directement depuis une classe enfant ou l'extérieur.

---

#### **10. Composition vs Agrégation**
- **Composition** : Relation forte où l'objet contenu est détruit avec son conteneur.  
  *Exemple* : Une voiture **a un** moteur. Si la voiture est détruite, le moteur aussi.  
- **Agrégation** : Relation faible où l'objet contenu existe indépendamment.  
  *Exemple* : Une université **a des** étudiants. Si l'université ferme, les étudiants continuent d'exister.

---

#### **11. Abstraction**
L'abstraction consiste à simplifier la complexité en ne montrant que l'essentiel.  
*Exemple de code PHP* :
```php
abstract class Forme {
    abstract public function calculerAire(): float;
}
class Rectangle extends Forme {
    public function calculerAire(): float {
        return 10 * 5;
    }
}
```

---

#### **12. Redéfinition de Méthodes**
La redéfinition permet à une classe enfant de modifier le comportement d'une méthode héritée.  
*Exemple de code PHP* :
```php
class Vehicule {
    public function demarrer(): void {
        echo "Le véhicule démarre.";
    }
}
class Voiture extends Vehicule {
    public function demarrer(): void {
        echo "La voiture démarre rapidement.";
    }
}
```

---

#### **13. Interface vs Classe Abstraite**
- **Interface** : Définit un contrat que les classes doivent respecter.  
  *Exemple* : `interface Roulant { public function rouler(): void; }`  
- **Classe Abstraite** : Fournit une structure de base avec des méthodes abstraites.  
  *Exemple* : `abstract class Animal { abstract public function son(): void; }`

---

#### **14. Design Patterns**
1. **Singleton** : Garantit qu'une classe n'a qu'une seule instance.  
2. **Factory** : Crée des objets sans spécifier la classe exacte.  
3. **Observer** : Permet à un objet de notifier d'autres objets lors d'un événement.

---

#### **15. Avantages de la POO**
- **Modularité** : Code facile à organiser et à réutiliser.  
- **Maintenabilité** : Modifications locales sans affecter l'ensemble du système.  
- **Extensibilité** : Ajout de nouvelles fonctionnalités sans modifier le code existant.  
- **Sécurité** : Encapsulation protège les données internes.

---

### **Travaux Pratiques**

#### **TP 1 : Création de Classe**
```php
class Voiture {
    public function __construct(
        private string $marque,
        private string $modele,
        private int $vitesse = 0
    ) {}

    public function demarrer(): void {
        echo "La voiture démarre.";
    }

    public function accelerer(int $vitesse): void {
        $this->vitesse += $vitesse;
    }

    public function freiner(): void {
        $this->vitesse = 0;
    }
}
```

---

#### **TP 2 : Encapsulation**
```php
class Voiture {
    private string $marque;
    private string $modele;
    private int $vitesse = 0;

    public function setMarque(string $marque): void {
        $this->marque = $marque;
    }
    public function getMarque(): string {
        return $this->marque;
    }
}
```

---

#### **TP 3 : Héritage**
```php
class VoitureElectrique extends Voiture {
    public function __construct(
        string $marque,
        string $modele,
        private int $autonomie
    ) {
        parent::__construct($marque, $modele);
    }
}
```

---

#### **TP 4 : Constructeur**
```php
class Voiture {
    public function __construct(
        private string $marque,
        private string $modele,
        private int $vitesse = 0
    ) {}
}
```

---

#### **TP 5 : Polymorphisme**
```php
class Voiture {
    public function afficherDetails(): void {
        echo "Marque : {$this->marque}, Modèle : {$this->modele}";
    }
}
class VoitureElectrique extends Voiture {
    public function afficherDetails(): void {
        parent::afficherDetails();
        echo ", Autonomie : {$this->autonomie} km";
    }
}
```

---

#### **TP 6 : Composition**
```php
class Moteur {
    public function __construct(private string $type) {}
}
class Voiture {
    public function __construct(private Moteur $moteur) {}
}
```

---

#### **TP 7 : Agrégation**
```php
class Garage {
    private array $voitures = [];
    public function ajouterVoiture(Voiture $voiture): void {
        $this->voitures[] = $voiture;
    }
}
```

---

#### **TP 8 : Interface**
```php
interface Roulant {
    public function rouler(): void;
}
class Voiture implements Roulant {
    public function rouler(): void {
        echo "La voiture roule.";
    }
}
```

---

#### **TP 9 : Classe Abstraite**
```php
abstract class Animal {
    abstract public function son(): void;
}
class Chien extends Animal {
    public function son(): void {
        echo "Woof!";
    }
}
```

---

#### **TP 10 : Design Pattern Singleton**
```php
class Configuration {
    private static ?self $instance = null;
    private function __construct() {}
    public static function getInstance(): self {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
```

---

#### **TP 11 : Design Pattern Factory**
```php
class VoitureFactory {
    public static function creerVoiture(string $type): Voiture {
        return match ($type) {
            'electrique' => new VoitureElectrique(),
            default => new Voiture(),
        };
    }
}
```

---

#### **TP 12 : Design Pattern Observer**
```php
class Sujet {
    private array $observateurs = [];
    public function notifier(): void {
        foreach ($this->observateurs as $observateur) {
            $observateur->actualiser();
        }
    }
}
```

---

#### **TP 13 : Gestion d'Employés**
```php
class Employe {
    public function __construct(
        private string $nom,
        private string $poste,
        private float $salaire
    ) {}

    public function augmenterSalaire(float $montant): void {
        $this->salaire += $montant;
    }
}
```

---

#### **TP 14 : Système Bancaire**
```php
class CompteBancaire {
    public function __construct(private float $solde = 0) {}

    public function deposer(float $montant): void {
        $this->solde += $montant;
    }

    public function retirer(float $montant): void {
        $this->solde -= $montant;
    }
}
```

---

#### **TP 15 : Gestion de Bibliothèque**
```php
class Livre {
    public function __construct(
        private string $titre,
        private string $auteur,
        private bool $disponible = true
    ) {}

    public function emprunter(): void {
        $this->disponible = false;
    }
}
```

---

#### **TP 16 : Calculatrice**
```php
class Calculatrice {
    public function additionner(float $a, float $b): float {
        return $a + $b;
    }
}
```

---

#### **TP 17 : Gestion de Stock**
```php
class Produit {
    public function __construct(
        private string $nom,
        private float $prix,
        private int $stock
    ) {}

    public function vendre(int $quantite): void {
        $this->stock -= $quantite;
    }
}
```

---

#### **TP 18 : Système de Réservation**
```php
class Reservation {
    public function __construct(
        private string $client,
        private DateTime $date,
        private string $statut = 'en attente'
    ) {}

    public function confirmer(): void {
        $this->statut = 'confirmée';
    }
}
```

---

#### **TP 19 : Gestion de Cours**
```php
class Cours {
    public function __construct(
        private string $titre,
        private string $enseignant,
        private array $etudiants = []
    ) {}

    public function ajouterEtudiant(string $etudiant): void {
        $this->etudiants[] = $etudiant;
    }
}
```

---

#### **TP 20 : Système de Notation**
```php
class Etudiant {
    public function __construct(
        private string $nom,
        private array $notes = []
    ) {}

    public function calculerMoyenne(): float {
        return array_sum($this->notes) / count($this->notes);
    }
}
```

---

### **Résumé des Points Clés**
| Concept              | Définition                                                                 | Exemple                                      |
|----------------------|-----------------------------------------------------------------------------|---------------------------------------------|
| Encapsulation        | Protection des données via `private` et contrôle via méthodes               | `private string $marque;`                  |
| Héritage             | Réutilisation du code d'une classe parente                                  | `class VoitureElectrique extends Voiture {}`|
| Polymorphisme        | Même interface, comportements différents                                   | `interface Roulant {}`                     |
| Abstraction          | Simplification de la complexité via classes abstraites et interfaces       | `abstract class Animal {}`                 |

---

**En résumé** : Ces TP permettent de mettre en pratique les concepts fondamentaux de la POO dans des contextes variés et concrets. 🚀
