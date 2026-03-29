# LAB-8-Analyse-de-posture-et-exposition-d-applications-mobiles-avec-BeVigil-et-Yaazhini-

## A. Informations générales
- **Date**: 2026-03-29
- **Analyste**: Mohamed DOUASSI
- **Cible**: jakhar.aseem.diva 
- **Version/Hash**: 1.0 / [5CEFC51FCE9BD760B92AB2340477F4DDA84B4AE0C5D04A8C9493E4FE34FAB7C5]
  
  <img width="1437" height="158" alt="image" src="https://github.com/user-attachments/assets/ee4e8280-d8c4-486c-9c6e-64ab28fcd311" />

- **Outils utilisés**: BeVigil, Yazzhini

## B. Résumé exécutif
L'analyse statique de l'application DIVA révèle un niveau de risque **CRITIQUE**. L'application échoue à protéger la confidentialité des données utilisateur à plusieurs niveaux : stockage en clair sur la carte SD, exposition de bases de données via des Providers non sécurisés, et présence de secrets (clés API, mots de passe) directement dans le code source et les ressources.

## C. Top 5 constats

### 1. Secrets et identifiants codés en dur (Hardcoded Secrets)
- **Sévérité**: **High**
- **Preuve**: `res/values/strings.xml` et `APICreds2Activity.java`.
  
<img width="1108" height="525" alt="image" src="https://github.com/user-attachments/assets/d3a6f70a-d347-4d37-882f-7a7266b6953a" />

- **Impact**: Un attaquant peut extraire des clés API (Tveeter) et des mots de passe en décompilant l'APK, compromettant les services tiers.
- **Remédiation**: Ne jamais stocker de secrets côté client. Utiliser Android Keystore ou un backend proxy.
- **Référence OWASP**: MASVS-STORAGE-1 / MSTG-STORAGE-14

### 2. Stockage non sécurisé sur support externe
- **Sévérité**: **High**
- **Preuve**: `InsecureDataStorage4Activity.java` créant le fichier `/sdcard/.uinfo.txt`.
  
<img width="1197" height="413" alt="image" src="https://github.com/user-attachments/assets/56e27154-e2cf-419d-a6e1-e33440d4a086" />

- **Impact**: Les identifiants enregistrés sur la carte SD sont lisibles par n'importe quelle autre application possédant la permission READ_EXTERNAL_STORAGE.
- **Remédiation**: Utiliser le stockage interne privé (`Context.MODE_PRIVATE`) ou `EncryptedSharedPreferences`.
- **Référence OWASP**: MASVS-STORAGE-2

### 3. Exposition du Content Provider (NotesProvider)
- **Sévérité**: **Medium**
- **Preuve**: `NotesProvider.java` déclaré dans le Manifest avec `android:exported="true"` sans permissions.
  
  <img width="1501" height="508" alt="image" src="https://github.com/user-attachments/assets/ad1a810a-5555-4002-a613-0eb1217d62e9" />

- **Impact**: Vol de l'intégralité de la base de données SQLite "notes" par une application tierce via l'URI `content://jakhar.aseem.diva.provider.notesprovider/notes`.
- **Remédiation**: Définir `android:exported="false"` ou restreindre l'accès avec des permissions personnalisées.
- **Référence OWASP**: MASVS-PLATFORM-2

### 4. Communication réseau non chiffrée (HTTP)
- **Sévérité**: **Medium**
- **Preuve**: `APICreds2Activity.java` pointant vers `http://payatu.com`.
  
  <img width="1243" height="520" alt="image" src="https://github.com/user-attachments/assets/91fac07f-33d8-43d1-bf09-0e28d8499332" />

- **Impact**: Risque d'interception des données (Man-in-the-Middle) car le trafic ne bénéficie pas du chiffrement TLS/SSL.
- **Remédiation**: Forcer l'utilisation de HTTPS et configurer la Network Security Configuration.
- **Référence OWASP**: MASVS-NETWORK-1

### 5. Configuration de sécurité du Manifest dangereuse
- **Sévérité**: **Medium**
- **Preuve**: `AndroidManifest.xml` avec `android:debuggable="true"` et `android:allowBackup="true"`.
  
  <img width="1467" height="520" alt="image" src="https://github.com/user-attachments/assets/03cf4f04-6622-495d-a809-7928e738f008" />

- **Impact**: Facilite l'extraction de données via ADB (sauvegarde physique) et permet l'attachement d'un débugueur pour l'analyse dynamique.
- **Remédiation**: Désactiver le mode debug et backup pour les versions de production.
- **Référence OWASP**: MASVS-RESILIENCE-1

## D. Faux positifs notables
- **JavaScript dans WebView**: Bien que signalé par Yazzhini, c'est un risque "Low" si l'entrée utilisateur est correctement filtrée, mais reste une mauvaise pratique dans ce contexte.

## E. Recommandations prioritaires
1. **Chiffrement au repos**: Migrer tous les stockages de fichiers et SQL vers des versions chiffrées (SQLCipher/Jetpack Security).
2. **Durcissement du Manifest**: Désactiver l'exportation des composants non nécessaires et supprimer les permissions WRITE_EXTERNAL_STORAGE superflues.
3. **Obfuscation du code**: Utiliser ProGuard ou R8 pour rendre le reverse engineering plus difficile.

## F. Annexes
- Rapport détaillé BeVigil (Strings & APKiD)
- Rapport complet Yazzhini (Vulnerabilities tree)
- Capture d'écran des URI Providers identifiées
