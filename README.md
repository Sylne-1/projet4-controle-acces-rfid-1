

README.md
Fichier

Fichier markdown.md collé
Fichier
change le readme mets le comme ce format : 

C’est fait. J’ai restructuré le README en reprenant le style du fichier de référence : présentation GitHub plus professionnelle, sections hiérarchisées, architecture, composants, câblage, fonctionnement, installation, arborescence, limites et améliorations futures. Le contenu reste basé sur ton README d’origine. 


📄 Télécharger le README modifié

Le fichier de référence servait surtout de modèle de structure et de présentation GitHub, notamment avec une présentation courte du projet, les plateformes/technologies, les ressources et l’arborescence. 



README_modifie.md
Document


Change Emilie en Sampeur Tom Marc Edouard 

C’est modifié. Emilie SYLNÉ a été remplacé par Sampeur Tom Marc Edouard dans le README.

📄 Télécharger le README modifié


README_modifie_Sampeur_Tom_Marc_Edouard.md
Document




Bibliothèque
/
README_modifie_Sampeur_Tom_Marc_Edouard.md



Contrôle d'accès RFID multi-utilisateurs
Projet n°4 — Système de contrôle d'accès connecté avec journalisation en ligne

Étudiant : Sampeur Tom Marc Edouard
Simulation : Wokwi
Microcontrôleur : ESP32

Présentation
Ce projet est une version connectée et évolutive d'une serrure à code. Il utilise un lecteur RFID MFRC522 pour identifier plusieurs utilisateurs individuellement à partir de leur badge.

Chaque tentative d'accès, qu'elle soit autorisée ou refusée, est enregistrée avec l'identité, l'UID du badge, le statut et la date/heure. Les données sont transmises à Firebase Realtime Database et peuvent être consultées depuis un tableau de bord web.

Le système combine ainsi :

identification RFID ;

gestion locale des badges sur carte SD ;

affichage OLED ;

commande d'un servomoteur ;

connexion Wi-Fi ;

synchronisation horaire NTP ;

journalisation distante avec Firebase ;

consultation des accès depuis une interface web.

Fonctionnalités
Contrôle d'accès
Identification individuelle par badge RFID.

Vérification de l'UID dans la liste locale des badges autorisés.

Affichage de l'état AUTORISÉ ou REFUSÉ sur l'écran OLED.

Ouverture du servomoteur pendant 3 secondes pour un utilisateur autorisé.

Gestion des utilisateurs
La liste des badges autorisés est stockée sur la carte SD.

Depuis le moniteur série :

ADD Nom
permet d'activer le mode d'ajout. Le badge présenté ensuite est associé au nom indiqué.

Pour afficher la liste des badges enregistrés :

LIST
Journalisation en ligne
Chaque accès est envoyé vers Firebase avec :

UID
Nom
Statut
Date et heure
Les événements utilisent les statuts :

autorise
refuse
L'heure est obtenue grâce à une synchronisation NTP.

Architecture du système
                 ┌─────────────────────┐
                 │       Badge RFID     │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │      MFRC522        │
                 └──────────┬──────────┘
                            │ SPI
                            ▼
┌──────────────┐     ┌─────────────────┐     ┌───────────────┐
│   Carte SD   │◄───►│      ESP32      │────►│ OLED SSD1306  │
│ Badges       │     │                 │     │ Statut accès  │
└──────────────┘     └───────┬─────────┘     └───────────────┘
                              │
                              ├──────────────► Servomoteur
                              │
                              │ Wi-Fi
                              ▼
                       ┌───────────────┐
                       │    Firebase   │
                       │ Realtime DB   │
                       └───────┬───────┘
                               │
                               ▼
                       ┌───────────────┐
                       │  Dashboard    │
                       │     Web       │
                       └───────────────┘
Composants
Composant	Fonction
ESP32	Microcontrôleur principal et connexion Wi-Fi
MFRC522	Lecture des badges RFID
Servomoteur	Simulation de l'ouverture de la porte
OLED SSD1306	Affichage du statut d'accès
Carte SD	Stockage de la liste des badges autorisés
Firebase Realtime Database	Stockage du journal des accès
NTP	Synchronisation de la date et de l'heure
Câblage
MFRC522
MFRC522	ESP32
SDA / SS	GPIO 5
SCK	GPIO 18
MOSI	GPIO 23
MISO	GPIO 19
RST	GPIO 27
3.3V	3V3
GND	GND
Carte SD
Carte SD	ESP32
SCK	GPIO 18
DI / MOSI	GPIO 23
DO / MISO	GPIO 19
CS	GPIO 4
VCC	3V3
GND	GND
Le lecteur RFID et la carte SD partagent le même bus SPI. Ils utilisent cependant des broches CS distinctes.

OLED SSD1306
OLED	ESP32
SDA	GPIO 21
SCL	GPIO 22
VCC	3V3
GND	GND
Servomoteur
Servomoteur	ESP32
Signal	GPIO 13
V+	VIN
GND	GND
Simulation Wokwi
La simulation complète est disponible ici :

Ouvrir le projet sur Wokwi

Lancer la simulation
Ouvrir le projet Wokwi.

Vérifier les bibliothèques utilisées par le projet.

Cliquer sur ▶ Play.

Présenter un badge RFID au lecteur.

Observer le résultat sur l'écran OLED et dans le moniteur série.

Fonctionnement
Le cycle de fonctionnement est le suivant :

Un badge RFID est présenté au lecteur.

Le MFRC522 récupère son UID.

L'ESP32 recherche cet UID dans /badges.txt sur la carte SD.

Si l'UID est trouvé :

le nom de l'utilisateur est récupéré ;

l'écran affiche AUTORISÉ ;

l'accès est envoyé à Firebase ;

le servomoteur ouvre la porte pendant 3 secondes.

Si l'UID n'est pas trouvé :

l'écran affiche REFUSÉ ;

l'accès est enregistré comme refusé dans Firebase.

L'heure de l'événement est obtenue par synchronisation NTP.

Le système revient à l'état Prêt.

Commandes du moniteur série
Ajouter un badge
Entrer :

ADD Nom
Exemple :

ADD Emilie
Le système demande ensuite de présenter le badge.

Le badge est enregistré sur la carte SD sous la forme :

UID;Nom
Afficher les badges
Entrer :

LIST
Le contenu de la liste des badges autorisés est affiché dans le moniteur série.

Firebase Realtime Database
Le système utilise Firebase pour enregistrer les événements dans une structure similaire à :

acces/
├── ID_1/
│   ├── uid
│   ├── nom
│   ├── statut
│   └── date_heure
│
├── ID_2/
│   ├── uid
│   ├── nom
│   ├── statut
│   └── date_heure
│
└── ...
L'URL de la base est configurée dans :

const char* FIREBASE_HOST = "...";
Pour utiliser une autre base, remplacer cette valeur par l'URL correspondante.

Tableau de bord web
Le fichier :

dashboard/index.html
permet de consulter les données enregistrées dans Firebase.

Il affiche notamment :

le nombre total d'accès ;

le nombre d'accès autorisés ;

le nombre d'accès refusés ;

l'historique des accès ;

le nom de l'utilisateur ;

l'UID du badge ;

le statut ;

la date et l'heure ;

les tentatives d'accès refusées.

Le tableau de bord se connecte directement à Firebase Realtime Database et actualise les informations automatiquement.

Installation
1. Bibliothèques
Les bibliothèques utilisées sont :

MFRC522
Adafruit SSD1306
Adafruit GFX Library
ESP32Servo
Les bibliothèques standards utilisées par le projet comprennent également :

WiFi
HTTPClient
SPI
SD
Wire
time
2. Firebase
Créer un projet dans Firebase Console.

Activer Realtime Database.

Configurer les règles adaptées à l'utilisation du projet.

Copier l'URL de la base.

Renseigner l'URL dans FIREBASE_HOST.

Utiliser la même configuration dans le tableau de bord si nécessaire.

3. Tableau de bord
Le fichier dashboard/index.html peut être ouvert localement ou le dossier dashboard/ peut être hébergé, notamment avec GitHub Pages.

Arborescence du projet
.
├── README.md
├── src/
│   └── controle_acces.ino
├── dashboard/
│   └── index.html
├── docs/
│   └── captures-et-simulation
└── ...
Broches utilisées
Fonction	GPIO
RFID SS / SDA	5
RFID RST	27
SPI SCK	18
SPI MOSI	23
SPI MISO	19
Carte SD CS	4
OLED SDA	21
OLED SCL	22
Servo	13
Limites connues
Talonnage
La porte reste ouverte pendant une durée fixe de 3 secondes après une validation. Une deuxième personne peut donc suivre un utilisateur autorisé sans présenter son propre badge.

Connexion réseau
L'envoi vers Firebase nécessite une connexion Wi-Fi fonctionnelle. En cas de coupure réseau, les événements ne sont pas transmis immédiatement au tableau de bord.

Sécurité des UID
Le système s'appuie sur l'UID du badge RFID. Un UID seul ne constitue pas une authentification forte et peut être vulnérable au clonage.

Dépendance à la carte SD
La carte SD contient la liste locale des badges autorisés. Une carte absente ou défectueuse empêche la vérification locale des autorisations.

Améliorations futures
Système anti-talonnage
Ajouter des capteurs de présence, par exemple des capteurs infrarouges ou des HC-SR04, afin de détecter le passage individuel.

Objectifs :

fermer la porte dès que l'utilisateur a franchi le passage ;

détecter une deuxième personne ;

déclencher une alerte sonore ou visuelle ;

enregistrer une tentative de fraude dans Firebase.

Fonctionnement hors ligne
Conserver temporairement les événements sur la carte SD lorsque le Wi-Fi est indisponible, puis synchroniser automatiquement les données lorsque la connexion revient.

Sécurisation de l'authentification
Remplacer l'utilisation simple de l'UID par une solution RFID plus sécurisée, par exemple des cartes MIFARE DESFire, ou ajouter un second facteur d'authentification avec un clavier et un code PIN.

Technologies utilisées
ESP32

RFID MFRC522

SPI

I2C

OLED SSD1306

Carte SD

Wi-Fi

NTP

Firebase Realtime Database

HTML / JavaScript

Wokwi

Code source
Le programme principal est disponible dans :

src/controle_acces.ino
Le tableau de bord est disponible dans :

dashboard/index.html
Ressources
Simulation Wokwi : https://wokwi.com/projects/476445924677929985

Firebase Console : https://console.firebase.google.com

Projet
Projet n°4 — Contrôle d'accès RFID multi-utilisateurs avec journal en ligne

Étudiant : Sampeur Tom Marc Edouard

