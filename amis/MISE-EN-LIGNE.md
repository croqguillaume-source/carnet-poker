# Publier — la marche à suivre

Trois étapes, dans cet ordre. Compte 25 minutes en tout.

---

## Étape 1 — Projet Supabase des amis ✅ FAIT

Projet `toyhycogwjuzfcbjfysf`, distinct de celui des trois. Les deux tables sont créées,
l'URL et la clé `anon` sont en place dans `carnet.html`, et l'aller-retour complet a été
vérifié : écriture des tournois et de l'état, puis relecture sur un carnet vierge avec le
même code — nom, bankroll, tournois et fiches reviennent tous. Les lignes de test ont été
supprimées, les tables sont vides.

<details><summary>Le détail, si tu dois refaire cette étape un jour</summary>

### Créer le projet (~10 min)

**Pourquoi un second projet.** La clé `anon` part en clair dans `carnet.html`, qui sera
une page publique. Or la policy livrée avec le carnet est `using (true)` : elle n'autorise
pas seulement à écrire ses propres lignes, elle autorise à **lire toutes celles de la
table**, quel que soit le code de groupe. Pointer la version amis sur le projet
`csotvmnwpvezgakbhvbr` reviendrait à publier les sessions, les fiches, les notes de tilt
et les bankrolls des trois — et ce qui vit dans les tables du carnet longévité.

Un projet distinct ne rend pas le système étanche entre les amis eux-mêmes : quelqu'un du
groupe qui devine le code d'un autre peut lire ses données. C'est pour ça que le carnet
génère des codes aléatoires et refuse ceux de moins de huit caractères. Mais il met vos
données à vous hors de portée, et c'est le point qui compte ici.

1. Sur [supabase.com](https://supabase.com) → **New project**. Nom : `carnet-poker-amis`.
   Région : Europe (West). Le mot de passe de base de données ne servira pas ici, mais
   note-le quand même.
2. Le temps que le projet démarre (2-3 min), ouvre **SQL Editor** → **New query**, colle
   le bloc ci-dessous, puis **Run**.
3. **Settings → API**. Copie deux valeurs et envoie-les-moi :
   - **Project URL** — de la forme `https://xxxxxxxxxxxx.supabase.co`
   - **anon public** — la longue clé. Surtout **pas** `service_role`, qui ignore les
     policies et donnerait un accès total à qui ouvre la page.

```sql
create table poker_state (
  code text not null,
  pid  text not null,
  data jsonb not null default '{}'::jsonb,
  updated_at timestamptz not null default now(),
  primary key (code, pid)
);

create table poker_tournament (
  code text not null,
  pid  text not null,
  tid  text not null,
  data jsonb not null,
  updated_at timestamptz not null default now(),
  primary key (code, pid, tid)
);
create index on poker_tournament (code);

alter table poker_state      enable row level security;
alter table poker_tournament enable row level security;
create policy "groupe" on poker_state
  for all to anon using (true) with check (true);
create policy "groupe" on poker_tournament
  for all to anon using (true) with check (true);
```

Tant que l'URL et la clé ne sont pas renseignées, **le carnet fonctionne quand même** :
la sauvegarde en ligne s'affiche désactivée, avec un encart qui explique que ce n'est pas
une panne et renvoie vers l'export JSON.

Un garde-fou est en place : si les identifiants du projet des trois se retrouvaient un
jour dans ce fichier, la sauvegarde se coupe et le refus s'écrit dans la console. Ne le
retire pas.

</details>

---

## Étape 2 — Déposer les fichiers sur GitHub (~5 min)

Sur [github.com/croqguillaume-source/carnet-poker](https://github.com/croqguillaume-source/carnet-poker),
branche `main` : **Add file** → **Upload files**.

**Dépôt A — la version amis.** Glisse le dossier `amis/` entier depuis
`C:\Claude\Carnet Poker\`. GitHub conserve le nom du dossier. Six fichiers :

```
amis/index.html   amis/carnet.html   amis/guide.html
amis/icon.svg     amis/manifest.webmanifest   amis/LISEZ-MOI.md
```

Aucun ne porte le nom d'un fichier existant : **rien n'est écrasé**.

**Dépôt B — le carnet à trois, resté en local.** Glisse ces deux fichiers, pris à la
racine de `C:\Claude\Carnet Poker\` :

```
carnet-poker.html      (remplace celui en ligne, périmé)
index.html             (nouveau, page d'entrée du dépôt)
```

Message de commit : `Version solo pour les amis + grand chantier du carnet a trois`.
Puis **Commit changes**. GitHub Pages republie en 1 à 2 minutes.

---

## Étape 3 — Vérifier avant d'envoyer le lien (~5 min)

Ouvre `https://croqguillaume-source.github.io/carnet-poker/amis/` **en navigation
privée** — c'est le seul moyen de voir ce que verra quelqu'un qui arrive sans rien, ton
navigateur habituel ayant déjà des données en `localStorage`.

- [ ] L'accueil s'affiche, les deux tuiles mènent au carnet et au guide
- [ ] Le carnet demande un prénom, puis les boutons répondent **sans recharger la page**
- [ ] Onglet Bankroll → la sauvegarde en ligne est soit active, soit désactivée avec son
      encart d'explication
- [ ] Le lien « Retour au carnet » du guide ne tombe pas en 404
- [ ] Sur ton téléphone : rien ne déborde en largeur

Puis ouvre `https://croqguillaume-source.github.io/carnet-poker/` et vérifie que **ton**
carnet à trois est bien passé à la nouvelle version : l'onglet **Base de mains** doit
apparaître dans la barre de navigation. S'il n'y est pas, le fichier n'a pas été remplacé.

### Avant que tes frères ouvrent la nouvelle version

Le grand chantier fait passer le schéma en v2 : `migrate()` s'exécute au premier
chargement et transforme les données en place, sur le même `localStorage` et le même
groupe Supabase qu'avant. C'est prévu et testé, mais ça ne se défait pas.

**Dites-vous d'exporter un JSON chacun avant d'ouvrir la nouvelle version** — onglet
Bankroll → Exporter en JSON, sur l'ancienne. Trente secondes, et c'est le seul retour en
arrière possible.

Bonne nouvelle en revanche : les deux versions cohabitent. Les lignes d'état ne portent
pas de numéro de schéma, et l'ancienne version ignore les champs qu'elle ne connaît pas.
Si l'un de tes frères garde une page ouverte sur l'ancienne pendant une semaine, la sync
ne casse pas — il n'aura simplement ni le détail positionnel ni la base de mains.

---

## Ce que tu envoies aux potes

> Salut — je t'ai fait un carnet pour suivre tes sessions :
> https://croqguillaume-source.github.io/carnet-poker/amis/
>
> Tu ouvres, tu donnes ton prénom, et tu glisses tes fichiers d'historique
> Betclic ou Winamax dans l'onglet Import. Il en sort tes sessions, ton volume,
> tes fréquences, et il te dit où tu fuis. Rien à installer, pas de compte.
>
> Deux trucs : chez Winamax, prends bien aussi les fichiers `_summary`, sinon il
> manque les classements. Et beaucoup de cases resteront vides au début — c'est
> voulu, il refuse d'afficher un chiffre tant qu'il n'a pas assez de mains pour
> qu'il veuille dire quelque chose.

Sur iPhone comme sur Android, « Ajouter à l'écran d'accueil » depuis le navigateur
installe le carnet comme une application.
