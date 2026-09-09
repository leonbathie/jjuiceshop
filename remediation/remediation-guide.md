# GUIDE DE REMÉDIATION TECHNIQUE DES VULNÉRABILITÉS (LIVRABLE 1)

Cible : OWASP Juice Shop (v17.1.1)
Auteur : Ousmane BA
Date : Septembre 2026

Ce document détaille les causes racines, les correctifs applicatifs et les protocoles de validation pour les vulnérabilités prioritaires identifiées lors de l'évaluation de sécurité.

---

## 1. V1 — Contrôle d'accès défaillant (IDOR) sur le panier (CWE-639)

### Cause racine
L'API consulte les informations du panier en se basant sur le paramètre numérique fourni dans l'URL (`req.params.id`), sans valider si le panier demandé correspond réellement à l'identité de l'utilisateur authentifié.

### Correctif technique
Supprimer la dépendance au paramètre d'URL dynamique. L'API extrait l'identifiant du panier (`bid`) directement depuis le jeton JWT signé et vérifié côté serveur :

```javascript
// Contrôleur showBasket sécurisé
exports.showBasket = async (req, res) => {
  try {
    // Extraction sécurisée depuis le token validé côté serveur
    const userBasketId = req.user.bid;

    const basket = await BasketModel.findOne({ 
      where: { id: userBasketId } 
    });

    if (!basket) {
      return res.status(404).json({ error: "Panier introuvable" });
    }

    return res.status(200).json(basket);
  } catch (err) {

    return res.status(500).json({ error: "Erreur lors du chargement du panier" });
  }
};
```
## 2. V2 — Exposition du répertoire sensible /ftp (CWE-200)

### Cause racine
Le middleware Express `serveIndex` est activé sur le dossier `/ftp` sans contrôle d'accès ni authentification, permettant l'exploration complète de l'arborescence et le téléchargement de fichiers de sauvegarde (.bak, scripts, clés).

### Correctif technique
Déplacer les fichiers critiques hors de la racine web publique, désactiver la directive de listing automatique et appliquer un middleware d'authentification exigeant un rôle d'administrateur :

```javascript
// Restriction de l'accès au répertoire statique
app.use(
  '/ftp', 
  verifyToken, 
  requireRole('admin'), 
  serveIndex('ftp', { icons: false })
);
```
## 3. V3 — Divulgation de Stack Trace et versions logicielles (CWE-209)

### Cause racine
L'application fonctionne avec un environnement de développement non restreint (`NODE_ENV != production`), retournant la trace d'exécution détaillée, la version d'Express (^4.22.1) et les chemins système en cas d'erreur.

### Correctif technique
Configurer la variable d'environnement sur `NODE_ENV=production` et centraliser le traitement des exceptions via un gestionnaire d'erreurs qui journalise en interne et renvoie une réponse neutre au client :

```javascript
// Gestionnaire d'erreurs global de production
app.use((err, req, res, next) => {
  // Journalisation interne sécurisée
  logger.error(err.stack);

  // Réponse générique opaque pour le client
  res.status(500).json({
    status: "error",
    message: "Une erreur interne est survenue. Veuillez réessayer ultérieurement."
  });
});
```
