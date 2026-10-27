État initial : le bouton 🍔 ne fait rien, et la <nav> s'affiche en permanence sous le bouton (en double). On cherche à mettre en place un menu avec `popover`.

Pour chaque étape réalisée : faire un commit reprenant le titre.

# Mise en place de `popover`

Travail à faire dans le fichier `index.html`.

## Déclarer le menu comme `popover`

- Sur l'élément `<nav class="menu">`, ajouter l'attribut `popover`.
- Donner-lui un identifiant unique : `id="menu"`.

```html
<nav class="menu" id="menu" popover aria-label="Main menu"></nav>
```

**Vérification** : recharger la page, le menu à disparu.

## Ouvrir le menu `popover` depuis le bouton

Sur l'élément `<button class="burger">`, ajouter l'attribut `popovertarget="menu"`

```html
<button class="burger" type="button" popovertarget="menu" aria-label="Menu">
  🍔
</button>
```

**Vérification** : cliquez sur le bouton, le menu s'affiche (sans JavaScript). Cliquez à nouveau sur le bouton il se ferme. Appuyez sur Échap, il se ferme aussi. Cliquez en dehors, il se ferme encore.

## Un bouton de fermeture explicite

Ajoutez `popovertarget="menu" popovertargetaction="hide"` au bouton de fermeture `<button class="menu__close>` (dans le menu).

```html
<button
  class="menu__close burger burger--close"
  type="button"
  popovertarget="menu"
  popovertargetaction="hide"
  aria-label="Close the menu"
>
  🍔
</button>
```

**Vérification** : ouvrez le menu, puis cliquez sur le bouton dans le menu : il se ferme.

## Focus pour l'accessibilité

Ajouter l'attribut `autofocus` sur le bouton de fermeture :

```html
<button
  class="menu__close burger burger--close"
  type="button"
  popovertarget="menu"
  popovertargetaction="hide"
  aria-label="Close the menu"
  autofocus
></button>
```

**Vérification** : ouvrez le menu et appuyez sur Entrée sans toucher la souris : le menu se ferme. Le focus a bien été placé sur le bouton de fermeture.

# Styles CSS

Travail à faire dans le fichier `css/style.css`

## Le menu plein écran

Ajoute une régle CSS ayant pour sélecteur `.menu` qui indique de trouvé les éléments de classe "menu" (`class="menu"`) dans l'HTML. Cette règle fixe les propriétés CSS :

- hauteur : 100% du parent
- largeur : 100% du parent
- pas de bord (épaisseur 0)

```css
/* --- menu (popover plein écran) ------------------------------------------ */
/* La feuille du navigateur a déjà fixé le panneau à l'écran
   (position: fixed; inset: 0) : on l'étire, sans positionner. */
.menu {
  width: 100%;
  height: 100%;
  border: 0;
}
```

**Vérification** : le menu s'affiche couvrant tout l'écran.

## Figer la page en arrière-plan

L'élément `html` est la racine du document HTML. On veut qu'il ne puisse pas scroller quand le menu (`.menu`) est ouvert (aka. répond au pseudosélecteur `:popover-open`).

On utilise `:has()` pour tester un élément enfant.

```css
/* Page figée tant que le menu est ouvert.
   :has() — extrait fourni : seul moyen, en CSS, de faire remonter l'état
   du menu jusqu'à la page. */
html:has(.menu:popover-open) {
  overflow: hidden;
}
```

Si le document était plus grand que la page, l'on pourrait scroller le document en arrière du menu, même si celui-ci couvre toute la page. l'ajout de `overflow: hidden` à l'ouverture du menu empêche le scroll de la page en arrière.

## Le curseur sur le bouton

La propriété CSS `cursor` permet de changer l'apparence du curseur.

```css
/* --- burger ---------------------------- */
.burger {
  cursor: pointer;
}
```

## Le fondu du menu

Sur le menu (`<nav class="menu">` par le sélecteur `.menu`).

- on veut des transitions en 400ms y compris sur les propriétés à valeurs "discretes" (Eg. `display` qui passe de `none` à `block`sans valeurs intermédiaires).
- on commence par une opacité à 0 (transparent).

Quand le menu sera visible (`popover` ouvert, testé pas pseudosélecteur `:popover-open`).

- on met l'opacité à 1 qui changera progressivement du fait de la transition mise en place.

La déclaration `@starting-style` permet d'avoir une animation (partant d'opacité 0) alors que le menu était en `display:none` (non affiché, donc en principe pas d'animation).

```css
.menu {
  /* … on complète le bloc existant … */
  transition: 400ms allow-discrete;
  opacity: 0;
}

.menu:popover-open {
  opacity: 1;
}

@starting-style {
  .menu:popover-open {
    opacity: 0;
  }
}
```

**Vérification** : testez en mettant en commentaire tout ou partie du code pour comprendre le rôle de chaque déclaration.

## Rotation du bouton de fermeture

On applique la même démarche pour animer le bouton de fermeture (`<button class="menu__close>`). On fait cela quand le menu (parent) est ouvert (`:popover-open`). Cela se fait par un sélecteur basé sur l'imbrication (`sel-parent␣element-enfant`)

```css
/* À l'ouverture, même position « hamburger » */
@starting-style {
  .menu:popover-open .burger--close {
    transform: none;
  }
}

.menu:popover-open .burger--close {
  transform: rotate(90deg);
  transition: 400ms;
}
```

## Respecter les préférences de mouvement

On utilise une média query pour désactiver les transitions si l'utilisateur préfère ne pas en voir.

L'usage de la virgule permet de regrouper deux sélecteurs pour la même règle CSS.

```css
@media (prefers-reduced-motion: reduce) {
  .menu,
  .menu .burger--close {
    transition: none;
  }
}
```

**Verification** : affichez dans l'inspecteur (`⌥⌘I`) la palette de commande `⇧⌘P`

- recherchez : `motion`

# Compréhension

Ajouter à votre code des commentaires reprenant les explications données dans ce fichier.

- HTML : `<!-- içi le commentaire -->`
- CSS : `/* ici le commentaire */`

Recherchez sur le site https://developer.mozilla.org/fr/ les pages de documentation de ce qui a été utilisé dans ce TP :

- `popover` : https://developer.mozilla.org/fr/docs/Web/HTML/Reference/Global_attributes/popover
- ...
