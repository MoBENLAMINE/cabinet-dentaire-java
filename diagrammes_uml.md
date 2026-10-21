# Diagrammes UML - Gestion d'un cabinet dentaire

Version corrigée (21/10/2026) : elle suit exactement le diagramme de la prof (sujet, page 2) et le code du projet `CabinetDentaire`.
Pour les transformer en images : coller chaque bloc dans https://mermaid.live puis exporter en PNG/SVG.

## 1. Diagramme de classes

```mermaid
classDiagram
    direction TB

    class Utilisateur {
        <<abstract>>
        -String login
        -String pwd
        +getLogin() String
        +setLogin(String) void
        +authentifier(String, String) boolean
        +getRole()* String
    }

    class Administrateur {
        +validerRdv(RendezVous) void
        +annulerRdv(RendezVous) void
        +getRole() String
    }

    class Patient {
        -String cin
        -String nom
        -String prenom
        -String adresse
        -String telephone
        -LocalDate dateNaissance
        +getRole() String
        +getters / setters
    }

    class Dentiste {
        -int idDentiste
        -String nom
        -String prenom
        -String specialite
        -List~Creneau~ planning
        +getPlanning() List~Creneau~
    }

    class Creneau {
        -int idCreneau
        -LocalDate date
        -LocalTime heureDebut
        -LocalTime heureFin
        -String type
        +estLibre() boolean
        +contient(Acte) boolean
    }

    class CreneauReserve {
        -RendezVous rendezVous
        +estLibre() boolean
        +getRendezVous() RendezVous
    }

    class Acte {
        -int idActe
        -String libelle
        -int duree
        -double tarif
    }

    class RendezVous {
        -int idRdv
        -LocalDate dateReservation
        -LocalDate dateRdv
        -String etat
        +valider() void
        +annuler() void
        +genererFiche() String
    }

    class Imprimable {
        <<interface>>
        +genererFiche() String
    }

    Utilisateur <|-- Administrateur
    Utilisateur <|-- Patient
    Creneau <|-- CreneauReserve
    Imprimable <|.. RendezVous

    Dentiste "1" --> "*" Creneau : possède
    RendezVous "*" --> "1" Patient : concerne
    RendezVous "*" --> "1" Acte : pour
    RendezVous "1" -- "1" CreneauReserve : occupe
```

## 2. Diagramme de packages

```mermaid
flowchart LR
    ui["cabinet.ui<br/>fenêtres JavaFX"] -->|utilise| service["cabinet.service<br/>Cabinet, Persistance, FichePDF"]
    service -->|utilise| model["cabinet.model<br/>classes métier"]
    service -->|lève| exception["cabinet.exception<br/>exceptions personnalisées"]
    ui -->|attrape| exception
```

## 3. Cas d'utilisation

Mermaid n'a pas de vrai diagramme de cas d'utilisation : ce schéma en donne l'équivalent.

```mermaid
flowchart LR
    A(["Administrateur<br/>(secrétaire)"])
    P(["Patient"])

    subgraph Systeme["Système de gestion du cabinet"]
        UC1("S'authentifier")
        UC2("Gérer les patients")
        UC3("Gérer les dentistes")
        UC4("Gérer les actes")
        UC5("Gérer les créneaux")
        UC6("Réserver pour un patient")
        UC7("Valider / annuler un RDV")
        UC8("Consulter dentistes et créneaux libres")
        UC9("Réserver un rendez-vous")
        UC10("Imprimer / exporter la fiche PDF")
    end

    A --- UC1 & UC2 & UC3 & UC4 & UC5 & UC6 & UC7 & UC10
    P --- UC1 & UC8 & UC9 & UC10
```

## 4. Séquence : réservation par un patient

```mermaid
sequenceDiagram
    actor P as Patient
    participant F as Fenêtre réservation
    participant C as Cabinet
    participant Cr as Creneau
    participant D as Dentiste

    P->>F: choisit dentiste + acte, clique un créneau vert
    P->>F: clique "Réserver"
    F->>C: reserver(patient, acte, creneau)
    C->>Cr: estLibre()
    alt créneau déjà pris
        C-->>F: CreneauIndisponibleException
        F-->>P: message d'erreur
    else créneau libre
        C->>Cr: contient(acte)
        alt acte trop long
            C-->>F: DureeInsuffisanteException
            F-->>P: message d'erreur
        else ok
            C->>C: new CreneauReserve(creneau) + new RendezVous(...)
            C->>D: remplacerCreneau(ancien, nouveau)
            C-->>F: rendez-vous (En attente)
            F->>F: redessine le planning (case rouge) + sauvegarde
            F-->>P: affiche la fiche
        end
    end
```

## 5. États d'un rendez-vous

```mermaid
stateDiagram-v2
    [*] --> EnAttente : réservation
    EnAttente --> Valide : l'admin valide
    EnAttente --> Annule : l'admin annule
    Valide --> Annule : l'admin annule
    Annule --> [*] : le créneau redevient libre
```
