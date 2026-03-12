# 🎬 CH1NF0 — Générateur NFO & Présentation pour Trackers Privés

<p align="center">
  <strong>v0.1</strong> — par Corridor
</p>

**CH1NF0** est un outil tout-en-un pour préparer vos uploads sur trackers privés. Il analyse vos fichiers vidéo et génère automatiquement :

- ✅ Le **fichier NFO** technique complet (codec, bitrate, résolution, audio, chapitres, paramètres x264/x265…)
- ✅ La **présentation BBCode** prête à copier-coller sur le tracker (synopsis, casting, poster, tableaux…)
- ✅ Le **nom de release** au format standard (`Titre.Année.Langue.Résolution.Source.CodecAudio.CodecVidéo-TAG`)
- ✅ Le **fichier .torrent** avec votre URL d'annonce

Interface graphique cross-platform — fonctionne sur **Windows**, **Linux** et **macOS**.

---

## 📦 Installation

### Prérequis système

| Outil | Obligatoire | Rôle |
|-------|:-----------:|------|
| **Python 3.8+** | ✅ | Exécution de l'application |
| **mediainfo** | ✅ | Analyse des fichiers vidéo |
| **ffmpeg / ffprobe** | Recommandé | Calcul des bitrates quand absents des headers |

### Linux (Debian / Ubuntu)

```bash
# 1. Dépendances système
sudo apt install mediainfo ffmpeg python3 python3-pip python3-tk

# 2. Dépendances Python
pip install --break-system-packages pymediainfo requests torf pyinstaller
```

### macOS

```bash
# 1. Dépendances système (via Homebrew)
brew install mediainfo ffmpeg python3

# 2. Dépendances Python
pip3 install pymediainfo requests torf pyinstaller
```

### Windows

1. Installer **Python 3.8+** depuis [python.org](https://www.python.org/downloads/) (cocher « Add to PATH »)
2. Télécharger **MediaInfo CLI** depuis [mediaarea.net](https://mediaarea.net/fr/MediaInfo/Download/Windows) et l'ajouter au PATH
3. Télécharger **ffmpeg** depuis [ffmpeg.org](https://ffmpeg.org/download.html) et l'ajouter au PATH
4. Installer les dépendances Python :
```cmd
pip install pymediainfo requests torf pyinstaller
```

---

## 🚀 Lancer l'application

### Sans compilation (recommandé pour tester)

```bash
cd nfo-generator
python3 nfo_generator.py
```

Vous pouvez aussi passer des fichiers en argument :
```bash
python3 nfo_generator.py /chemin/vers/film.mkv
python3 nfo_generator.py /chemin/vers/dossier-serie/
```

### Compiler en exécutable autonome

#### Linux / macOS
```bash
cd nfo-generator
python3 -m PyInstaller --onefile --windowed --name "CH1NF0" --clean nfo_generator.py
./dist/CH1NF0
```

#### Windows
```cmd
cd nfo-generator
python -m PyInstaller --onefile --windowed --name "CH1NF0" --clean nfo_generator.py
dist\CH1NF0.exe
```

### Créer un raccourci bureau (Linux / XFCE)

```bash
cat > ~/Bureau/CH1NF0.desktop << 'EOF'
[Desktop Entry]
Type=Application
Name=CH1NF0 - NFO Generator
Exec=/chemin/vers/nfo-generator/dist/CH1NF0
Icon=video-x-generic
Terminal=false
Categories=Utility;
EOF
chmod +x ~/Bureau/CH1NF0.desktop
```

---

## 🎯 Guide d'utilisation

### 1. Ajouter les fichiers

- Cliquez sur **« ➕ Ajouter fichier(s) »** pour sélectionner un ou plusieurs fichiers vidéo
- Ou cliquez sur **« 📁 Ajouter dossier »** pour charger tout un dossier (utile pour les mini-séries en plusieurs parts)
- Formats supportés : `.mkv`, `.mp4`, `.avi`, `.m4v`, `.ts`, `.wmv`, `.flv`, `.mov`

> **Multi-fichiers** : si vous ajoutez plusieurs fichiers (ex: une mini-série en 4 parts), un seul NFO sera généré avec toutes les parts et un tableau récapitulatif.

### 2. Configurer les options

#### Clé API TMDB *(recommandé)*

Permet de récupérer automatiquement le synopsis, le casting, le poster, les genres, la date de sortie, le lien IMDB et la note TMDB. Cliquez sur 💾 pour sauvegarder la clé — elle sera pré-remplie aux prochains lancements.

> Pour obtenir une clé gratuite, voir la section [Obtenir une clé API TMDB](#-obtenir-une-clé-api-tmdb-gratuit).

#### Recherche TMDB

Laissez vide pour une détection automatique à partir du nom de fichier. Renseignez un titre manuellement si la détection auto ne trouve pas le bon résultat.

#### Nom du release

Laissez vide pour une génération automatique au format tracker :

```
Titre.Année.Langue.Résolution.Source.CodecAudio.Canaux.CodecVidéo-TAG
```

Exemple : `Les.Insurgés.2008.Multi.VFF.1080p.HDLight.AC3.5.1.x264-Sybelius`

L'application détecte automatiquement :

| Élément | Valeurs détectées |
|---------|------------------|
| Langue | `FRENCH`, `ENGLISH`, `Multi.VFF` (multi-pistes dont FR) |
| Résolution | `480p`, `576p`, `720p`, `1080p`, `2160p` |
| Source | `DVDRip`, `HDLight`, `BluRay`, `WEB-DL`, `WEBRip`, `HDTV`… |
| Codec audio | `AC3`, `DTS`, `DTS-HD.MA`, `AAC`, `FLAC`, `TrueHD`… + canaux |
| Codec vidéo | `x264`, `x265`, `XviD`, `H.264`, `H.265` |

#### TAG (team)

Le nom de votre team / groupe de release (ex: `Sybelius`). Si laissé vide, l'application essaie de le détecter depuis le nom de fichier.

#### Bannières par

Nom et URL du profil de l'auteur des bannières de présentation (optionnel, affiché en bas de la présentation BBCode).

### 3. Création du .torrent *(optionnel)*

Cochez **« Créer le fichier .torrent »** pour activer cette fonctionnalité.

| Champ | Description |
|-------|-------------|
| **URL d'annonce** | L'announce URL de votre tracker privé (obligatoire). Cliquez 💾 pour la sauvegarder. |
| **Source tag** | Tag source si le tracker l'exige (ex: `C411`). Sauvegardé avec 💾. |
| **Taille pièces** | `Auto` par défaut — adapté automatiquement à la taille des fichiers. Choix manuel possible : 256 KiB à 16 MiB. |
| **Privé** | Coché par défaut — **obligatoire pour les trackers privés**. |

L'application utilise `torf` (Python) en priorité, sinon `mktorrent`, sinon `transmission-create` comme fallback.

### 4. Générer

Cliquez sur **« ⚡ GÉNÉRER NFO + Présentation BBCode »**.

L'application va :

1. **Analyser** chaque fichier vidéo (codec, résolution, audio, chapitres…)
2. **Calculer** les bitrates manquants via ffprobe si nécessaire (encodages CRF)
3. **Rechercher** les infos sur TMDB (si activé)
4. **Générer** le release name au format tracker
5. **Créer** le NFO technique complet
6. **Créer** la présentation BBCode prête à coller
7. **Créer** le .torrent (si activé)
8. **Afficher** un popup avec le titre à copier-coller sur le tracker

### Fichiers générés

| Fichier | Contenu |
|---------|---------|
| `NomDuRelease.nfo` | NFO technique complet avec toutes les infos MediaInfo |
| `NomDuRelease.prez.txt` | Présentation BBCode prête à coller sur le tracker |
| `NomDuRelease.torrent` | Fichier torrent pour le tracker (si activé) |

---

## 📄 Structure du NFO généré

```
╔═══════════════════════════════════╗
║   CH1NF0 — MediaInfo vers NFO    ║
╚═══════════════════════════════════╝

═══ INFORMATIONS SUR L'OEUVRE ═══          ← via TMDB
  Titre, Année, Pays, Genre, Réalisateur
  Casting complet, Synopsis, Note TMDB

═══ INFORMATIONS SUR LE RELEASE ═══       ← résumé global
  Conteneur, Codec, Langues, Sous-titres
  Nombre de fichiers, Taille totale

═══ PART 1 — fichier.mkv ═══              ← détail par fichier
  ┌ GENERAL ┐
  │ Taille, Durée, Débit global/vidéo/audio
  ┌ VIDEO ┐
  │ Codec, Profil, Débit, Résolution
  │ Paramètres x264 : CRF, ref, bframes, ME, preset…
  ┌ AUDIO ┐
  │ Format, Débit, Canaux, Langue
  ┌ SOUS-TITRES ┐
  │ Format, Langue
  ┌ CHAPITRES ┐
  │ Timecodes

═══ RÉCAPITULATIF ═══                     ← si multi-fichiers
  Tableau comparatif de toutes les parts
  Points communs + Avertissements
```

---

## 📋 Structure de la présentation BBCode

La présentation est générée au format BBCode compatible avec les trackers francophones :

```
[h1]Titre du Film[/h1]
[h2](Année)[/h2]
[img]poster TMDB[/img]
[img]bannière[/img]
Pays, Genres, Date de sortie, Durée
Réalisateur(s), Acteur(s), Note TMDB, lien IMDB

[img]bannière synopsis[/img]
Synopsis complet en français

[img]bannière vidéo[/img]
Source, Résolution, Codec Vidéo, Débit vidéo

[img]bannière audio[/img]
Tableau : # | Canaux | Codec | Bitrate

[img]bannière sous-titres[/img]

[img]bannière release[/img]
Release name, Team, Nombre de fichiers, Poids total
Crédit bannières
```

---

## 🔑 Obtenir une clé API TMDB (gratuit)

1. Créez un compte sur [themoviedb.org](https://www.themoviedb.org/signup)
2. Allez dans **Paramètres** → **API**
3. Cliquez sur **Créer** → choisissez « Developer »
4. Remplissez le formulaire (une description basique suffit)
5. Copiez la clé **API Key (v3 auth)**
6. Collez-la dans CH1NF0 et cliquez 💾

La configuration est stockée localement :

| OS | Emplacement |
|----|-------------|
| Linux | `~/.config/ch1nf0/config.json` |
| macOS | `~/Library/Application Support/ch1nf0/config.json` |
| Windows | `%APPDATA%\ch1nf0\config.json` |

---

## ⚠️ Avertissements automatiques

L'application détecte et signale :

- **ref trop bas** : si `ref < 3` dans les paramètres x264, un avertissement est affiché car certains trackers exigent un minimum de ref=3
- **Bitrates manquants** : si les bitrates ne sont pas dans les headers (courant avec les encodages CRF via HandBrake), l'application les calcule automatiquement en scannant les paquets via ffprobe
- **Incohérences multi-parts** : résolutions différentes entre les parts, métadonnées couleur mixtes, versions d'encodeur différentes

---

## 🛠️ Dépendances Python

| Package | Rôle | Obligatoire |
|---------|------|:-----------:|
| `pymediainfo` | Analyse MediaInfo des fichiers vidéo | ✅ |
| `requests` | Requêtes API TMDB | Recommandé |
| `torf` | Création de fichiers .torrent | Optionnel* |
| `pyinstaller` | Compilation en exécutable | Optionnel |

> \* Si `torf` n'est pas installé, l'application cherche `mktorrent` ou `transmission-create` sur le système.

---

## 📁 Structure du projet

```
nfo-generator/
├── nfo_generator.py      # Application principale
├── requirements.txt      # Dépendances Python
├── build.sh              # Script de compilation Linux/macOS
├── build.bat             # Script de compilation Windows
└── README.md             # Ce fichier
```

---

## 🤝 Contribuer

Les contributions sont les bienvenues ! N'hésitez pas à ouvrir une issue ou une pull request.

Idées d'améliorations futures :
- Support du drag & drop natif (tkdnd)
- Personnalisation des bannières BBCode via l'interface
- Templates de présentation multiples selon le tracker
- Mode batch (traitement de plusieurs releases à la suite)
- Prévisualisation du NFO et du BBCode dans l'application
- Intégration directe avec l'API des trackers pour upload automatique

---

## 📜 Licence

Libre d'utilisation et de modification.

Fait avec ❤️ par Corridor.
