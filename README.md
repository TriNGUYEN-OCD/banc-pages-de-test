# Pages de test — catégorie F (redirections côté client)

Pages statiques servant de vérité terrain au banc d'évaluation. Aucune ressource externe n'est chargée, afin que le résultat dépende uniquement de l'outil testé.

## Contenu

| Fichier | Rôle | Marqueur |
|---|---|---|
| `f1-meta-refresh.html` | Redirige vers `cible-f1.html` par meta-refresh après 1 s | `MARQUEUR-F1-INTERMEDIAIRE` |
| `cible-f1.html` | Page cible du test F1 | `MARQUEUR-F1-CIBLE-ATTEINTE` |
| `f2-redirection-js.html` | Redirige vers `cible-f2.html` par `window.location.replace` après 2 s | `MARQUEUR-F2-INTERMEDIAIRE` ; `MARQUEUR-F2-JS-NON-EXECUTE` si JavaScript désactivé |
| `cible-f2.html` | Page cible du test F2 | `MARQUEUR-F2-CIBLE-ATTEINTE` |
| `index.html` | Sommaire | `MARQUEUR-INDEX` |

Les pages cibles contiennent aussi des éléments de contrôle pour l'extracteur commun :
- un texte masqué par CSS : `MARQUEUR-F1-TEXTE-CACHE` / `MARQUEUR-F2-TEXTE-CACHE` (doit apparaître dans `texte_complet` et `textes_caches`, pas dans `texte_visible`) ;
- un lien externe vers `https://example.org/` ;
- un formulaire fictif avec un champ mot de passe dont l'action pointe vers `https://example.com/formulaire-de-test` (doit donner `contient_mot_de_passe = true` et `action_externe = true`). Le bouton est inactif : rien n'est jamais envoyé.

## Déploiement sur GitHub Pages

1. Créer un dépôt public, par exemple `banc-pages-de-test`.
2. Y déposer tous les fichiers de ce dossier à la racine.
3. Paramètres du dépôt → Pages → Source : branche `main`, dossier `/ (root)`.
4. Attendre la publication, puis vérifier `https://tringuyen-ocd.github.io/banc-pages-de-test/`.

### Hébergement : dépôt public ou non

Avec un compte GitHub gratuit, GitHub Pages exige un dépôt public ; un dépôt privé nécessite un abonnement payant. Dans tous les cas le site publié est public, ce qui est indispensable puisque les SaaS testés doivent pouvoir y accéder. Ces fichiers ne contiennent aucun secret, un dépôt public ne pose donc pas de problème.

Alternatives si le dépôt doit rester privé : Cloudflare Pages, Netlify ou Vercel (déploiement depuis un dépôt privé, offre gratuite), ou n'importe quel serveur web statique de l'équipe.

## Chaînes attendues (à reporter dans `config/jeux_de_test.yaml`)

`<base>` = `https://tringuyen-ocd.github.io/banc-pages-de-test`

```yaml
- id: F1
  categorie: F
  url: <base>/f1-meta-refresh.html
  marqueur_attendu: "MARQUEUR-F1-CIBLE-ATTEINTE"
  chaine_attendue:
    - {url: "<base>/f1-meta-refresh.html", type: http}
    - {url: "<base>/cible-f1.html", type: meta_refresh}
- id: F2
  categorie: F
  url: <base>/f2-redirection-js.html
  marqueur_attendu: "MARQUEUR-F2-CIBLE-ATTEINTE"
  chaine_attendue:
    - {url: "<base>/f2-redirection-js.html", type: http}
    - {url: "<base>/cible-f2.html", type: javascript}
```

## Lecture des résultats

| Ce que l'outil renvoie | Interprétation |
|---|---|
| Marqueur `CIBLE-ATTEINTE` | Redirection suivie |
| Marqueur `INTERMEDIAIRE` seul | L'outil n'a pas suivi la redirection (pas d'attente ou pas de JavaScript) |
| Marqueur `JS-NON-EXECUTE` (F2) | JavaScript non exécuté |

Remarque : GitHub Pages peut ajouter une redirection HTTP 301 si l'URL est saisie sans `/` final ou avec un domaine personnalisé ; l'étape `valider-jeux` du banc enregistre la chaîne réelle de référence.
