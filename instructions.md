# La Collita de Paula — Instructions pour Claude Code

## C'est quoi ce projet ?

Un site catalogue pour un grossiste en fruits et légumes (Santi, numéro WhatsApp : +34 657 27 67 60). Les restaurateurs consultent le catalogue en ligne, remplissent un panier, et envoient leur commande via WhatsApp. Ça remplace un abonnement Shopify à 30€/mois qui servait juste à montrer des prix.

Le site est un fichier HTML unique (`index.html`), zéro dépendance, zéro framework. Il lit ses données depuis un Google Sheet public (format CSV). Santi met à jour ses prix dans Google Sheets, le site se met à jour automatiquement.

---

## Étape 1 : Créer le Google Sheet

Créer un nouveau Google Sheet nommé **"Catálogo Collita de Paula"**.

Ligne 1 (en-têtes, exactement comme ça) :

```
Categoría | Producto | Descripción | Precio | Unidad | Activo
```

Soit 6 colonnes : A, B, C, D, E, F.

Remplir avec les produits. Le PDF `TARIFA_COLLITA_DE_PAULA.pdf` contient toute la tarifa actuelle, organisée par familles. Voici le mapping des familles vers les catégories :

| Famille dans le PDF | Catégorie dans le Sheet |
|---|---|
| FAMILIA: 5 FRUTAS | Frutas |
| FAMILIA: 6 VERDURAS | Verduras |
| FAMILIA: 7 PATATAS/CEBOLLAS | Patatas y Cebollas |
| FAMILIA: 8 IV-V GAMMA | IV-V Gamma |
| FAMILIA: 9 FRUTOS SECOS/OTROS | Frutos Secos y Otros |
| FAMILIA: 100 ARTICULOS ALMUERZO POPULAR | Almuerzo Popular |

Format de chaque ligne :

| Categoría | Producto | Descripción | Precio | Unidad | Activo |
|---|---|---|---|---|---|
| Frutas | Aguacate 1ª | KG | 4,65 | KG | SI |
| Frutas | Plátano Canarias Extra | KG | 2,25 | KG | SI |
| Verduras | Tomate Daniela Extra | KG | 2,55 | KG | SI |
| Patatas y Cebollas | Patata Agria Saco Yute | KG | 0,85 | KG | SI |

La colonne **Activo** sert à masquer un produit sans le supprimer : mettre "NO" pour le cacher du site.

Le prix utilise la virgule comme séparateur décimal (format espagnol).

---

## Étape 2 : Publier le Google Sheet en CSV

1. Ouvrir le Google Sheet
2. Menu **Archivo** → **Compartir** → **Publicar en la web**
3. Dans le premier menu déroulant, sélectionner la feuille (ex: "Hoja 1")
4. Dans le deuxième menu déroulant, sélectionner **CSV**
5. Cliquer **Publicar**
6. Confirmer

Ça ne rend PAS le sheet éditable par le public, ça le rend juste lisible. Santi garde le contrôle total en écriture.

---

## Étape 3 : Brancher le site sur le Google Sheet

1. Copier l'URL du Google Sheet, par exemple :
   `https://docs.google.com/spreadsheets/d/1aBcDeFgHiJkLmNoPqRsTuVwXyZ/edit`

2. L'ID du Sheet c'est la partie entre `/d/` et `/edit` :
   `1aBcDeFgHiJkLmNoPqRsTuVwXyZ`

3. Ouvrir `collita-de-paula.html` dans un éditeur de texte

4. Chercher cette ligne en haut du JavaScript :
   ```js
   const SHEET_ID = '';
   ```

5. Coller l'ID entre les guillemets :
   ```js
   const SHEET_ID = '1aBcDeFgHiJkLmNoPqRsTuVwXyZ';
   ```

6. Si la feuille ne s'appelle pas "Hoja1", modifier aussi :
   ```js
   const SHEET_NAME = 'Hoja1';
   ```

7. Sauvegarder. C'est tout.

---

## Étape 4 : Héberger le site

Option recommandée : **GitHub Pages** (gratuit, fiable, rien à maintenir).

1. Créer un repo GitHub (ex: `collita-de-paula`)
2. Uploader `collita-de-paula.html` renommé en `index.html`
3. Dans Settings → Pages → Source : sélectionner la branche `main`
4. Le site sera live sur `https://username.github.io/collita-de-paula/`

Alternative : **Cloudflare Pages**, **Netlify**, ou n'importe quel hébergement statique.

Si Santi veut un nom de domaine custom (ex: `lacollitadepaula.com`), il suffit de l'acheter et de configurer le DNS pour pointer vers GitHub Pages.

---

## Comment ça marche au quotidien pour Santi

**Modifier un prix :** ouvrir Google Sheets sur son téléphone, changer le chiffre, c'est tout. Le site se met à jour au prochain chargement de page par un client.

**Ajouter un produit :** ajouter une nouvelle ligne dans le Google Sheet avec la catégorie, le nom, la description, le prix, l'unité, et "SI" dans Activo.

**Retirer un produit temporairement :** mettre "NO" dans la colonne Activo au lieu de supprimer la ligne.

**Ajouter une catégorie :** juste écrire un nouveau nom dans la colonne Categoría. Le site la détecte automatiquement.

---

## Comment ça marche côté client (restaurateur)

1. Le restaurateur ouvre le site
2. Il parcourt le catalogue ou cherche un produit
3. Il appuie sur "+" pour ajouter au panier
4. Il ouvre le panier et appuie sur "Enviar pedido por WhatsApp"
5. WhatsApp s'ouvre avec un message pré-rempli contenant la liste de produits, les quantités et le total
6. Santi reçoit la commande sur son WhatsApp

---

## Détails techniques

- Fichier unique HTML/CSS/JS, aucune dépendance externe (sauf Google Fonts)
- Données lues via l'API publique Google Sheets (format CSV, pas de clé API nécessaire)
- Panier stocké en localStorage (persiste entre les visites sur le même navigateur)
- Le numéro WhatsApp est configuré dans la variable `WHATSAPP_NUMBER` (actuellement : `34657276760`)
- Le site fonctionne sur mobile et desktop
- Aucun backend, aucune base de données, aucun serveur à maintenir