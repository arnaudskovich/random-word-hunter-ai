# Le Mot Caché

Un jeu de lettres façon pendu, en français, dans un seul fichier HTML. Un mot est tiré au hasard, caché derrière des cases, et il faut le deviner lettre par lettre avant de perdre ses 5 vies.

## Aperçu

- Mot tiré au hasard via l'API gratuite [trouve-mot.fr](https://trouve-mot.fr), de longueur quelconque (4 lettres minimum).
- Une case par lettre, toujours sur une seule ligne : les cases rétrécissent pour les mots longs.
- Un champ d'une seule lettre qui valide automatiquement à la frappe.
- 5 vies affichées sous forme de cœurs, qui se grisent à chaque erreur.
- Les lettres écartées sont listées discrètement sous le jeu, barrées comme des ratures.
- Deux thèmes : clair (« papier ») et sombre (« bibliothèque à la lampe »).

## Lancer le jeu

Aucune installation ni dépendance. Il suffit d'ouvrir le fichier :

```bash
# Option 1 : double-clic sur devine-le-mot.html

# Option 2 : serveur local
python3 -m http.server 8000
# puis http://localhost:8000/devine-le-mot.html
```

Une connexion internet est nécessaire pour appeler l'API et charger la police Lora. Sans connexion, le jeu reste jouable (voir « Mode hors-ligne »).

## Comment jouer

1. Tape une lettre dans le champ : elle est validée immédiatement.
2. Si elle est dans le mot, elle apparaît dans toutes les cases concernées. Sinon, tu perds une vie et la lettre rejoint la liste des « Écartées ».
3. Tu gagnes en révélant toutes les lettres, tu perds à la cinquième erreur (le mot est alors dévoilé).
4. Clique sur « Nouveau mot » ou appuie sur Entrée pour rejouer.

Les accents sont ignorés pour la saisie : taper `e` révèle aussi `é`, `è`, `ê`, etc.

## Fonctionnement technique

| Élément | Détail |
|---|---|
| Source des mots | `GET https://trouve-mot.fr/api/random`, qui renvoie un mot au format JSON |
| Filtrage | Lettres uniquement (accents compris), 4 caractères minimum ; jusqu'à 4 essais si le mot est rejeté |
| Délai d'attente | 3,5 secondes avant de basculer en mode hors-ligne |
| Comparaison | Mot et saisie normalisés (sans accents, en minuscules) |
| Thème | Variables CSS sur `:root`, surchargées par `data-theme="light\|dark"` ; choix mémorisé dans `localStorage` |
| Dépendances | Aucune, hormis la police Google Fonts « Lora » (avec Georgia en secours) |

### Mode hors-ligne

Si l'API est injoignable (pas de réseau, erreur, délai dépassé, ou restriction du navigateur), le jeu pioche dans une liste intégrée de 30 mots (`FALLBACK`). L'indicateur en haut à droite de la carte affiche alors `mode hors-ligne` au lieu de `API trouve-mot.fr`.

> **Hébergement sur claude.ai** : la page publiée comme artifact bloque les requêtes vers les sites externes. Elle fonctionne donc toujours en mode hors-ligne. Pour utiliser réellement l'API, ouvre le fichier en local ou héberge-le toi-même.
>
> **À vérifier** : la structure de la réponse de l'API a été gérée de façon défensive (tableau ou objet), mais l'appel n'a pas pu être testé depuis l'environnement de développement. Si l'indicateur reste sur `mode hors-ligne` malgré une connexion, ouvre la console du navigateur pour vérifier la réponse et d'éventuelles erreurs CORS.

## Personnalisation

Tout se règle en haut du script, dans `devine-le-mot.html` :

```js
const MAX = 5;                                  // nombre de vies
const API = "https://trouve-mot.fr/api/random"; // source des mots
const FALLBACK = ["dragon", "volcan", ...];     // mots du mode hors-ligne
```

- **Longueur minimale des mots** : modifier `{4,}` dans l'expression régulière de `fetchWord()`.
- **Couleurs** : modifier les variables CSS (`--bg`, `--card`, `--ink`, `--acc`…) dans les trois blocs `:root`, pour le thème clair, le thème sombre automatique et le thème sombre forcé.
- **Police** : remplacer l'URL Google Fonts et la propriété `font-family` du `body`.

## Structure

```
devine-le-mot.html   # HTML, CSS et JavaScript du jeu, tout-en-un
README.md
```

## Idées d'évolution

- Texture de papier discrète sur le fond.
- Marque-pages à la place des cœurs.
- Score et série de victoires, mémorisés dans `localStorage`.
- Indice optionnel : catégorie du mot (nom, verbe…), si l'API la fournit.
- Clavier visuel pour les écrans tactiles.
