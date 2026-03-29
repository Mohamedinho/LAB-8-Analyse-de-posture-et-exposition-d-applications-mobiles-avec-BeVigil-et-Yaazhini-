# Rapport de Scan Statistique - Yaazhini

## 1. Résumé du Scan
- **Application** : diva (jakhar.aseem.diva)
- **SDK Target** : 23 (Android 6.0)
- **Date** : 29 Mars 2026

## 2. Synthèse des Vulnérabilités
| Niveau | Type de Faille | Composant Impacté |
| :--- | :--- | :--- |
| **High** | Insecure Communication (HTTP) | APICreds2Activity.java |
| **Medium** | Debuggable Enabled | AndroidManifest.xml |
| **Medium** | Backup Vulnerability | AndroidManifest.xml |
| **Medium** | Improper Export of Providers | NotesProvider.java |
| **Low** | JavaScript Enabled in WebView | InputValidation2URISchemeActivity.java |

## 3. Preuves (Screenshots)
*(Note : Voir les images jointes dans ce dossier)*
- **high_vuln_http.png** : Communication en clair vers payatu.com.
- **medium_manifest_config.png** : Options debug et backup activées (allowBackup=true).
- **low_webview_js.png** : Activation du JavaScript (setJavaScriptEnabled).
