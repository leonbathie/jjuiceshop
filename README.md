# Audit de Sécurité - OWASP Juice Shop

Projet d'évaluation de sécurité applicative et d'intégration DevSecOps réalisé dans le cadre de l'examen final de Sécurité des Données.

## Structure du Dépôt
- `Jenkinsfile` : Définition du pipeline automatisé CI/CD (6 étapes).
- `reports/` : Rapports générés par les outils d'analyse (SCA et Secrets).
- `screenshots/` : Captures d'écran des vulnérabilités identifiées et des résultats.
- `security-config/` : Fichiers de configuration des outils de sécurité.
- `remediation/` : Documentation et correctifs proposés pour les failles.

## Outils Utilisés
- **npm audit** : Analyse de composition logicielle (SCA) pour les dépendances.
- **Gitleaks** : Détection des secrets et identifiants codés en dur.
- **Jenkins** : Automatisation et orchestration des contrôles de sécurité.

## Exécution des Analyses
1. Lancer le pipeline Jenkins configuré avec ce dépôt SCM.
2. Les rapports d'analyse sont automatiquement exportés dans le dossier `reports/`.
