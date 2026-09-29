# Projet n°4 — Contrôle d'accès RFID multi-utilisateurs avec journal en ligne

**Formation EIC 3.0 — Électronique & Prototypage (ESIH)**
**Formateur :** Carlos Yfrazin
**Projet réalisé :** Projet n°4

## Équipe

| Nom complet | Rôle |
|---|---|
| Tom Marc Edouard Sampeur | [à compléter] |
| Emilie Sylné | [à compléter] |
| Colson Jean [nom de famille] | [à compléter] |
| Giordanie Jean Maximilien [nom de famille] | [à compléter] |
| Medgine Pierre [nom de famille] | [à compléter] |

## Description

Version connectée et évolutive de la serrure à code : un lecteur RFID identifie chaque utilisateur individuellement grâce à son badge. Chaque tentative d'accès, autorisée ou refusée, est enregistrée en ligne avec la date et l'heure, puis consultable sur un tableau de bord web.

## Objectifs

- Identifier chaque utilisateur individuellement par badge RFID
- Gérer une liste de badges autorisés, modifiable et stockée sur carte SD
- Afficher localement le statut d'accès (autorisé / refusé) sur un écran OLED
- Simuler l'ouverture de la porte avec un servomoteur
- Enregistrer chaque accès (identité, date, heure) dans une base en ligne (Firebase)
- Consulter l'historique complet et les tentatives refusées sur un tableau de bord web

## Liens

- 🔗 Simulation Wokwi : **[à compléter : lien de partage Wokwi]**
- 🔗 Tableau de bord en ligne : https://tommmy-pngai.github.io/projet4-controle-acces-rfid/dashboard/index.html

## Composants (simulation Wokwi)

| Composant | Rôle |
|---|---|
| ESP32 | Microcontrôleur principal avec WiFi intégré |
| Lecteur RFID MFRC522 | Lecture des badges |
| Servomoteur | Simulation de l'ouverture de la porte |
| Écran OLED SSD1306 (I2C) | Affichage du statut d'accès |
| Module carte SD | Stockage de la liste des badges autorisés |

## Schéma de câblage

![Câblage du circuit](docs/cablage.png)

| Composant | Broche | ESP32 |
|---|---|---|
| MFRC522 | SDA | GPIO 5 |
| MFRC522 | SCK | GPIO 18 |
| MFRC522 | MOSI | GPIO 23 |
| MFRC522 | MISO | GPIO 19 |
| MFRC522 | RST | GPIO 27 |
| MFRC522 | 3.3V / GND | 3V3 / GND |
| Carte SD | SCK / DI / DO | GPIO 18 / 23 / 19 |
| Carte SD | CS | GPIO 4 |
| Carte SD | VCC / GND | 3V3 / GND |
| OLED | SDA | GPIO 21 |
| OLED | SCL | GPIO 22 |
| OLED | VCC / GND | 3V3 / GND |
| Servomoteur | Signal | GPIO 13 |
| Servomoteur | V+ / GND | VIN / GND |

Le lecteur RFID et la carte SD partagent le même bus SPI, avec des broches CS distinctes.

## Fonctionnement

1. Un badge est présenté au lecteur RFID.
2. L'ESP32 compare son UID à la liste `badges.txt` stockée sur la carte SD.
3. **Badge reconnu :** l'OLED affiche « AUTORISE » et le nom, le servomoteur s'ouvre 3 secondes, l'accès est envoyé à Firebase avec le statut `autorise`.
4. **Badge inconnu :** l'OLED affiche « REFUSE » et la tentative est envoyée à Firebase avec le statut `refuse`.
5. Chaque accès est horodaté grâce à une synchronisation NTP.

### Gestion des badges (moniteur série)

| Commande | Effet |
|---|---|
| `ADD Nom` | Puis présenter le badge : il est enregistré sur la SD et dans Firebase |
| `DEL Nom` | Supprime le badge de la SD et de Firebase |
| `LIST` | Affiche les badges enregistrés |

### Persistance de la liste des badges

La carte SD simulée par Wokwi est vidée à chaque redémarrage de la simulation. Pour que les noms restent actifs, la liste des badges est aussi sauvegardée dans Firebase (nœud `badges`). Au démarrage, l'ESP32 relit cette liste et reconstruit `badges.txt` sur la SD. Sur du matériel réel, la SD conserve elle-même les données.

## Tableau de bord web

Le fichier `dashboard/index.html` affiche en temps réel :

- le nombre total d'accès, autorisés et refusés
- un tableau de tous les accès (statut, nom, UID, date et heure)
- un onglet « Tentatives refusées » qui liste uniquement les refus

Il lit directement la base Firebase Realtime Database et s'actualise toutes les 5 secondes.

## Captures d'écran

**Moniteur série (accès autorisé et refusé)**

![Moniteur série](docs/serie.png)

**Écran OLED**

![Écran OLED](docs/oled.png)

**Tableau de bord**

![Tableau de bord](docs/dashboard.png)

**Vidéo de démonstration :** [docs/demo.mp4](docs/demo.mp4)

## Installation et utilisation

### 1. Simulation Wokwi

1. Ouvrir le projet Wokwi (lien ci-dessus).
2. Dans **Library Manager**, vérifier la présence de : MFRC522, Adafruit SSD1306, Adafruit GFX Library, ESP32Servo.
3. Lancer la simulation avec le bouton ▶.
4. Enregistrer un badge : taper `ADD Nom` dans le moniteur série, choisir une carte dans la fenêtre du MFRC522, puis cliquer sur **TAP**.
5. Présenter à nouveau la carte pour un accès autorisé, ou une autre carte pour un accès refusé.

### 2. Base de données Firebase

1. Créer un projet sur la [console Firebase](https://console.firebase.google.com).
2. Activer **Realtime Database** en mode test.
3. Copier l'URL de la base dans la variable `FIREBASE_HOST` de `src/controle_acces.ino` et de `dashboard/index.html`.

### 3. Tableau de bord

Ouvrir `dashboard/index.html` dans un navigateur, ou l'héberger avec **GitHub Pages** (Settings → Pages → branche `main`, dossier `/root`).

## Limites et améliorations possibles

- **Talonnage :** rien ne détecte deux personnes qui passent avec un seul badge (ajouter un capteur de présence ou une alarme).
- **Coupure WiFi :** le contrôle local continue, mais le journal n'est plus envoyé (sauvegarde locale sur SD puis envoi différé).
- **Clonage de badge :** l'UID des badges basiques est lisible et clonable (ajouter un code PIN ou des cartes chiffrées).
- **Sécurité de la base :** Firebase est en mode test, donc ouverte en lecture et écriture (ajouter authentification et règles strictes).
- **Coupure de courant :** comportement à définir (fail-safe ou fail-secure) avec batterie de secours.
- **Fermeture de la porte :** un capteur de porte remplacerait le délai fixe de 3 secondes.

## Structure du dépôt

```
projet4-controle-acces-rfid/
├── README.md
├── src/
│   └── controle_acces.ino
├── dashboard/
│   └── index.html
└── docs/
    ├── cablage.png
    ├── serie.png
    ├── oled.png
    ├── dashboard.png
    └── demo.mp4
```
