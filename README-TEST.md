# Le Bon Deal — kit GitHub prêt à configurer

Ce kit contient une interface web mobile (`index.html`) et le Worker Cloudflare qui réalise l'analyse IA et la recherche web (`worker.js`).

## Architecture
- Interface : GitHub Pages.
- API : Cloudflare Workers + Workers AI.
- Recherche web : Brave Search API.
- Les clés secrètes restent côté Cloudflare, jamais dans `index.html`.
- OCR photo/PDF : exécuté dans le navigateur. Le texte extrait n'est envoyé au Worker que lorsque l'utilisateur lance l'analyse.

## 1. Déposer les fichiers sur GitHub
Dans `https://github.com/delmehdito/le-bon-deal`, branche `main`, remplacer `index.html` et ajouter `worker.js`, `wrangler.toml`, `.gitignore` et ce `README.md`. Ne pas publier de clés API dans GitHub.

## 2. Configurer le Worker Cloudflare
Le nom existant à conserver est `le-bon-deal-ia`.
1. Ouvrir Cloudflare > Workers & Pages > `le-bon-deal-ia`.
2. Remplacer le code du Worker par le contenu de `worker.js`, puis déployer.
3. Vérifier qu'un binding Workers AI nommé `AI` est configuré.
4. Ajouter les secrets suivants dans Settings > Variables and Secrets :
   - `BRAVE_SEARCH_API_KEY` : clé Brave Search API.
   - `ADMIN_TEST_TOKEN` : un secret aléatoire long (au moins 32 caractères).
5. Le code autorise l'origine `https://delmehdito.github.io`. Pour un autre domaine, modifier `ALLOWED_ORIGINS` dans `worker.js`, puis redéployer.
6. Ouvrir `https://le-bon-deal-ia.mehdi-larissi.workers.dev/`. Le JSON doit afficher `aiConfigured: true`, `webSearchConfigured: true` et `adminTestConfigured: true`.

## 3. Activer GitHub Pages
Dans GitHub > Settings > Pages, sélectionner le déploiement depuis la branche `main`, dossier `/ (root)`. Attendre la fin du déploiement puis ouvrir `https://delmehdito.github.io/le-bon-deal/`.

## 4. Vérifier le fonctionnement
- Tester la route de santé `/` dans un navigateur.
- Dans l'interface, saisir un produit, un prix et une ville puis lancer l'analyse.
- Contrôler que la réponse contient des sources cliquables. Si aucune source avec prix explicite n'est disponible, le verdict doit rester prudent.
- Si une erreur 502 apparaît, vérifier le binding Workers AI, le modèle disponible dans le compte Cloudflare et les secrets.

## 5. Paiements et publicité : non activés
Les tarifs affichés dans l'interface sont les choix prévus :
- Analyse approfondie : 0,99 €
- 30 jours : 3,99 €
- Annuel : 39,99 €

Les boutons de paiement restent désactivés. Il faut d'abord créer/configurer un compte de paiement (Stripe est une option, pas encore configurée), créer les produits/prix, ajouter des webhooks vérifiés, et gérer les droits d'accès côté serveur. Ne jamais activer les paiements avec une simple vérification côté navigateur. Les emplacements publicitaires sont des placeholders ; aucun revenu publicitaire n'est généré avant l'intégration d'un réseau et l'approbation de son compte.

## Limitations à traiter avant une ouverture commerciale
- Le quota gratuit affiché côté navigateur n'est pas une protection anti-abus. Il faut mettre en place un stockage et un contrôle de quota côté serveur.
- Ce kit ne conserve pas les devis dans une base de données et ne constitue pas une base de prix fiable à lui seul.
- Brave Search fournit des résultats de recherche, pas une garantie de prix à jour ou de comparables identiques. Les vendeurs et les frais doivent être vérifiés.
- Avant de conserver ou d'analyser des devis, supprimer/anonymiser les données personnelles et publier une politique de confidentialité adaptée.
- L'OCR dans le navigateur nécessite une connexion pour télécharger les bibliothèques OCR/PDF si elles ne sont pas déjà en cache.

## Sécurité
- Ne jamais mettre `BRAVE_SEARCH_API_KEY`, `ADMIN_TEST_TOKEN`, un secret de paiement ou une clé privée dans `index.html`.
- Si une clé est publiée accidentellement, la révoquer et en générer une nouvelle.
