




























































































 






































































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
