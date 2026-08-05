# Carnet Poker — version solo, pour les potes

Version individuelle du carnet : **un joueur, un carnet, un fichier**. Chacun ouvre la
même URL et se retrouve chez lui. Aucun compte, aucune installation.

## Ce qu'il y a dans ce dossier

| Fichier | Rôle |
|---|---|
| `index.html` | Page d'accueil. C'est l'URL à envoyer aux potes. |
| `carnet.html` | L'application. Huit onglets, autonome, vanilla JS. |
| `guide.html` | Le guide d'étude en 12 blocs. **Les tableaux de ranges sont en attente** — voir plus bas. |
| `manifest.webmanifest`, `icon.svg` | Installation en application sur mobile (PWA). |

## Mise en ligne

Le dossier est publié tel quel par GitHub Pages, sans étape de build :

```bash
git push
```

L'URL à envoyer devient `https://croqguillaume-source.github.io/carnet-poker/amis/`.

Tous les chemins internes sont **relatifs**, donc le dossier fonctionne aussi bien
ouvert en local, déposé sur n'importe quel hébergeur, ou envoyé par mail en pièces
jointes — à condition de garder les quatre fichiers ensemble.

## Ce qui diffère du carnet à trois

**Un seul joueur.** Le sélecteur « consulter », la comparaison des trois, la revue de
groupe et l'écran « qui utilise cet appareil » ont disparu. À leur place :

- **Histogramme de discipline sur 12 semaines** — la régularité plutôt que le pic ;
- **30 derniers jours vs tout l'historique** — la comparaison qui reste possible seul,
  et la seule qui réponde à « est-ce que mon jeu a bougé » ;
- **Leaks du mois vs avant**, dans l'onglet Review, avec l'avertissement qui va avec :
  ces colonnes comptent des fiches, pas des erreurs ;
- **Bloc « par où commencer »** sur le dashboard, qui disparaît au premier import.

Le modèle de données garde `pid: 'p1'` partout. C'est volontaire : les filtres n'ont pas
bougé d'une ligne, et un export JSON reste lisible d'une version à l'autre. `shapeUp()`
ne retient que le premier joueur — un export du carnet à trois s'importe donc ici, amputé
des deux autres registres, plutôt que de les mélanger en silence.

**Clés `localStorage` distinctes** (`carnet-solo-*`, `guide-solo-done`). Indispensable :
les deux applications vivent sur le même domaine, et `localStorage` est partagé par
origine, pas par dossier. Avec les mêmes clés, ouvrir l'une écraserait l'autre.

**La synchronisation devient une sauvegarde personnelle.** Même projet Supabase, mêmes
tables, mais le code est individuel — ils l'utilisent pour retrouver leurs données entre
téléphone et ordinateur. Un bouton génère un code à six caractères aléatoires, et les
codes de moins de huit caractères sont refusés. L'encart de l'onglet Bankroll leur dit
franchement que le code est la seule clé, que tu héberges le serveur et que tu peux
techniquement y lire ce qu'ils écrivent.

**Trois bugs connus corrigés au passage :** le câblage des gestionnaires d'événements
passe désormais avant l'écran d'accueil (un carnet neuf n'a plus de boutons inertes
jusqu'au premier rechargement) ; `manifest` et `icon` sont en chemins relatifs ; le lien
retour du guide pointe vers `carnet.html`.

## Ce qu'il reste à faire

**Les ranges.** Le bloc 01 du guide affiche six emplacements « en préparation » : EP, CO,
BTN et SB à 60 bb, EP et BTN à 40 bb. Envoie-moi les grilles et je les intègre — n'importe
quel format convient (capture GTO Wizard, export Equilab, ou juste la liste des mains).

Deux remarques avant de les produire :

- Les seuils du moteur de leaks (`RULES[].def`) restent des **valeurs théoriques**, jamais
  calibrées sur vos pools. Si tu construis les ranges, tu auras au passage de quoi
  recaler les seuils — ce serait le vrai gain.
- Les cases « bloc travaillé » du guide et celles du carnet sont toujours **indépendantes**,
  comme dans la version à trois. Deux `localStorage` séparés, deux pages sans lien.
  Corrigeable si ça les gêne à l'usage.
