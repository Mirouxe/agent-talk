# Agent Talk

Une application web complète qui vous permet d'enregistrer des messages vocaux, de les transcrire automatiquement, de les nettoyer avec l'IA, et de les envoyer directement dans Notion.

## 🚀 Fonctionnalités

- **🎤 Enregistrement vocal** : Interface intuitive type WhatsApp pour enregistrer des messages vocaux
- **🎧 Transcription automatique** : Utilise Whisper API d'OpenAI pour transcrire en texte
- **✨ Nettoyage intelligent** : ChatGPT nettoie et structure le texte (supprime les "euh", "bah", etc.)
- **📄 Intégration Notion** : Crée automatiquement une nouvelle page dans votre base de données Notion

## 📋 Pipeline de traitement

```
Vocal → Transcription (Whisper) → Nettoyage (ChatGPT) → Page Notion
```

## 🛠️ Installation

### Prérequis

- Python 3.8 ou supérieur
- Compte OpenAI avec accès aux APIs Whisper et ChatGPT
- Compte Notion avec une intégration configurée

### Étape 1 : Cloner et installer les dépendances

```bash
# Créer un environnement virtuel
python3 -m venv venv

# Activer l'environnement virtuel
source venv/bin/activate  # Sur macOS/Linux
# ou
venv\Scripts\activate  # Sur Windows

# Installer les dépendances
pip install -r requirements.txt
```

### Étape 2 : Configuration des clés API

1. Créez un fichier `.env` à partir du template :

```bash
cp .env.example .env
```

2. Ouvrez le fichier `.env` et remplissez vos clés API :

```env
OPENAI_API_KEY=sk-...
NOTION_API_KEY=secret_...
NOTION_DATABASE_ID=...
PORT=5000
```

### Comment obtenir les clés ?

#### OpenAI API Key

1. Allez sur [platform.openai.com](https://platform.openai.com/)
2. Connectez-vous ou créez un compte
3. Allez dans "API Keys"
4. Créez une nouvelle clé secrète
5. Copiez-la dans votre fichier `.env`

#### Notion API Key

1. Allez sur [www.notion.so/my-integrations](https://www.notion.so/my-integrations)
2. Cliquez sur "Nouvelle intégration"
3. Donnez-lui un nom (ex: "Vocal to Notion")
4. Sélectionnez les capacités : "Lire le contenu", "Mettre à jour le contenu", "Insérer du contenu"
5. Copiez le "Internal Integration Token"

#### Notion Database ID

1. Ouvrez Notion et créez une nouvelle base de données (ou utilisez-en une existante)
2. Assurez-vous qu'elle contient ces propriétés :
   - `Name` (Title)
   - `Date` (Date)
   - `Status` (Select) avec au moins l'option "À classer"
3. Cliquez sur "⋯" (trois points) en haut à droite de votre base de données
4. Cliquez sur "Copier le lien"
5. L'ID de la base de données est la partie après le dernier `/` et avant le `?` :
   ```
   https://www.notion.so/workspace/DATABASE_ID?v=...
                                   ^^^^^^^^^^^
                                   C'est cet ID
   ```
6. **Important** : Partagez votre base de données avec votre intégration :
   - Cliquez sur "Partager" en haut à droite
   - Invitez votre intégration (elle apparaîtra dans la liste)

## 🎯 Utilisation

### Démarrer le serveur

```bash
# Assurez-vous que l'environnement virtuel est activé
python app.py
```

Le serveur démarre sur `http://localhost:5000`

### Utiliser l'interface web

1. Ouvrez votre navigateur et allez sur `http://localhost:5000`
2. Cliquez sur "Commencer l'enregistrement"
3. Autorisez l'accès au microphone si demandé
4. Parlez dans votre microphone
5. Cliquez sur "Arrêter l'enregistrement" quand vous avez terminé
6. L'application va automatiquement :
   - Transcrire votre audio
   - Nettoyer et structurer le texte
   - Créer une nouvelle page dans Notion
7. Cliquez sur "Ouvrir dans Notion" pour voir le résultat !

## 📁 Structure du projet

```
AGENT_TALK/
├── app.py                      # Application Flask principale
├── requirements.txt            # Dépendances Python
├── .env                        # Variables d'environnement (à créer)
├── .env.example               # Template pour les variables d'environnement
├── .gitignore                 # Fichiers à ignorer par Git
├── README.md                  # Cette documentation
├── services/                  # Services de traitement
│   ├── __init__.py
│   ├── transcription_service.py    # Service Whisper
│   ├── cleaning_service.py         # Service ChatGPT
│   └── notion_service.py           # Service Notion
├── static/                    # Interface web
│   ├── index.html            # Page principale
│   ├── style.css             # Styles
│   └── script.js             # Logique frontend
└── uploads/                   # Fichiers audio temporaires (créé automatiquement)
```

## 🔧 Architecture technique

### Backend (Flask)

- **Framework** : Flask avec Flask-CORS
- **API Endpoint** : `/api/process-audio` (POST)
- **Gestion des fichiers** : Stockage temporaire puis suppression automatique

### Services

1. **TranscriptionService** : Utilise l'API Whisper d'OpenAI
   - Modèle : `whisper-1`
   - Langue : Français
   
2. **CleaningService** : Utilise l'API ChatGPT
   - Modèle : `gpt-4`
   - Prompt optimisé pour nettoyer et structurer les transcriptions vocales
   
3. **NotionService** : Utilise l'API Notion
   - Crée des pages avec formatage riche
   - Inclut la transcription originale en callout
   - Ajoute automatiquement la date et le statut "À classer"

### Frontend

- **HTML5** : Structure sémantique
- **CSS3** : Design moderne avec animations
- **JavaScript** : 
  - MediaRecorder API pour l'enregistrement
  - Fetch API pour la communication avec le backend
  - Interface réactive et intuitive

## 🎨 Personnalisation

### Modifier le prompt de nettoyage

Éditez `services/cleaning_service.py` et modifiez la variable `system_prompt` :

```python
system_prompt = """Votre prompt personnalisé ici..."""
```

### Modifier la structure des pages Notion

Éditez `services/notion_service.py` dans la méthode `_prepare_page_content()` pour changer la mise en page des pages créées.

### Modifier l'interface

- **Couleurs** : Éditez les variables CSS dans `static/style.css` (section `:root`)
- **Textes** : Modifiez `static/index.html`
- **Comportement** : Éditez `static/script.js`

## 🐛 Dépannage

### Erreur "Impossible d'accéder au microphone"

- Assurez-vous d'utiliser HTTPS ou localhost
- Vérifiez les permissions du navigateur
- Testez avec un autre navigateur (Chrome/Firefox recommandés)

### Erreur "Erreur de transcription"

- Vérifiez que votre clé OpenAI est valide
- Vérifiez que vous avez du crédit sur votre compte OpenAI
- Vérifiez que le fichier audio n'est pas corrompu

### Erreur "Erreur Notion"

- Vérifiez que votre intégration a accès à la base de données
- Vérifiez que la base de données contient les bonnes propriétés
- Vérifiez que le `NOTION_DATABASE_ID` est correct

### Le serveur ne démarre pas

- Vérifiez que le port 5000 n'est pas déjà utilisé
- Vérifiez que toutes les dépendances sont installées
- Vérifiez que le fichier `.env` existe et est correctement configuré

## 📝 Prochaines améliorations possibles

- [ ] Support multi-langues
- [ ] Intégration avec d'autres plateformes (Evernote, Google Docs, etc.)
- [ ] Application mobile
- [ ] Traitement par lots de plusieurs vocaux
- [ ] Catégorisation automatique dans Notion
- [ ] Extraction de tâches et de rendez-vous

## 📄 Licence

Ce projet est libre d'utilisation pour un usage personnel.

## 🤝 Contribution

Les contributions sont les bienvenues ! N'hésitez pas à ouvrir une issue ou une pull request.

## 👨‍💻 Auteur

Créé avec ❤️ pour simplifier la prise de notes vocales.

---

**Note** : Ce projet utilise des APIs payantes (OpenAI). Vérifiez les coûts avant utilisation intensive.

# AGENT_TALK