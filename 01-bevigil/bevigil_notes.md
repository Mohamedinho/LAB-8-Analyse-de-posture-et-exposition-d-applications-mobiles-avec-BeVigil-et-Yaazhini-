# Rapport d'Analyse OSINT & Statique - BeVigil

## 1. Informations de l'Application
- **Nom** : DIVA (Damn Insecure and Vulnerable App)
- **Package** : jakhar.aseem.diva
- **Taille** : 1.4 MB
- **Activités** : 17 (Surface d'attaque élevée)

## 2. Analyse de la Sécurité (APKiD)
- **Compilateur** : dx (possible dexmerge)
- **Anti-Reverse** : Aucun (Pas d'obfuscation, pas de Packer détecté).
- **Risque** : Le code source est entièrement exposé et facile à décompiler.

## 3. Fuites de Données (Secrets & Strings)
L'analyse des ressources strings.xml révèle des identifiants sensibles codés en dur :
- ids1_password : Mot de passe de test exposé.
- pic2_label : Référence à des clés API.
- pic_label : Clés d'accès aux services tiers.

## 4. Permissions Critiques
| Permission | État | Risque |
| :--- | :--- | :--- |
| **WRITE_EXTERNAL_STORAGE** | RISKY | Permet d'écrire des secrets sur la carte SD (Public). |
| **READ_EXTERNAL_STORAGE** | RISKY | Permet à d'autres apps de lire les données de DIVA. |
| **INTERNET** | NORMAL | Utilisé pour les communications HTTP non sécurisées. |

## 5. Bibliothèques Tierces
- **Android Support v4/v7** : Bibliothèques obsolètes pouvant contenir des vulnérabilités connues (CVE).
