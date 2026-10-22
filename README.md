# Cabinet Dentaire (projet Java EMI 2026-2027)

## Ouvrir le projet
1. Installer IntelliJ IDEA Community + un JDK 17 ou 21.
2. IntelliJ : File > Open > choisir le fichier `pom.xml` > "Open as Project".
   IntelliJ telecharge tout seul JavaFX et PDFBox (il faut Internet la premiere fois).

## Lancer
- Tests console : clic droit sur `cabinet/TestConsole.java` > Run.
- Planning colore (demo) : onglet Maven a droite > Plugins > javafx > `javafx:run`.

## Organisation
| Package | Contenu |
|---|---|
| `cabinet.model` | Classes metier du sujet (Utilisateur, Patient, Dentiste, Acte, Creneau, CreneauReserve, RendezVous) + interface Imprimable |
| `cabinet.exception` | Exceptions personnalisees (double reservation, duree, login) |
| `cabinet.service` | Cabinet (toutes les donnees + regles), Persistance (serialisation), FichePDF (PDFBox) |
| `cabinet.ui` | Fenetres JavaFX : PlanningView, ReservationDemo, Impression |

## Deja fait
- Classes metier avec getters/setters, heritage, classe abstraite, interface, polymorphisme
- Regles : pas de double reservation, duree de l'acte <= duree du creneau
- Sauvegarde/chargement (fichier `cabinet.dat`)
- Planning colore (vert/rouge/orange/bleu) + reservation cote patient (demo)
- Export PDF de la fiche, fonction d'impression

## Reste a faire
- Fenetre de connexion (LoginView) qui ouvre la fenetre Admin ou Patient selon `getRole()`
- Fenetre Admin : CRUD patients, dentistes, actes, creneaux (TableView + formulaires)
- Fenetre Admin : liste des rendez-vous + boutons Valider / Annuler + reservation pour un patient
- Fenetre Fiche : afficher la fiche + boutons Imprimer et Exporter PDF
- Bonus : recherche/filtre, historique patient, statistiques
