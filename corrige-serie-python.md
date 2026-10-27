# Corrigé proposé — Série de programmation Python (Terminale NSI)

**Document de travail à valider avant la création de l’application.** Les réponses ci-dessous suivent l’ordre de la série. Les formulations peuvent varier si le raisonnement et le résultat sont corrects.

## Proposition de notation

Pour obtenir un total de **100 points**, je propose de noter cinq exercices plus longs sur 8 et les dix autres sur 6 :

| Exercice | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Barème proposé | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 8 | 8 | 6 | 6 | 8 | 6 | 8 | 8 |

Total : **100 points**. Les points sont modifiables ; l’application pourra aussi ramener le résultat sur 20.

> **Éléments manquants dans le PDF fourni :** l’exercice 2 renvoie aux annexes cartographiques 1 et 2, absentes du fichier joint. Les liaisons exactes du joueur 2 et la vérification graphique des trajets ne peuvent donc pas être établies avec certitude. Dans l’exercice 9, la numérotation passe de la question 4 à la question 6 : la question 5 ne figure pas dans le PDF. Je le signale au lieu d’inventer une réponse.

---

## Exercice 1 — Labyrinthe (6 points)

### 1. Fonction `mur`

```python
def mur(laby, i, j):
    return laby[i][j] == 1
```

La valeur `1` signifie « mur » ; la valeur `0` signifie « case accessible ».

### 2.a. Pourquoi `voisine` fonctionne

Les différences `l1-l2` et `c1-c2` sont des entiers. La somme de leurs carrés vaut 1 seulement si l’une des différences vaut `1` ou `-1` et l’autre vaut 0. Les deux cases sont donc sur la même ligne et dans deux colonnes consécutives, ou sur la même colonne et dans deux lignes consécutives : elles partagent un côté.

### 2.b. Fonction `adjacentes`

```python
def adjacentes(cases):
    for i in range(len(cases) - 1):
        if not voisine(cases[i], cases[i + 1]):
            return False
    return True
```

### 3. Terminaison de `teste`

À chaque passage dans la boucle, `i` augmente de 1. Il ne peut donc y avoir plus de `len(cases)` itérations. La boucle s’arrête également dès qu’un mur est rencontré (`possible` devient faux). Elle termine donc dans tous les cas.

### 4. Fonction `echappe`

On vérifie que le chemin est compatible, qu’il commence à l’entrée `(0, 0)` et finit en bas à droite. Pour un tableau carré de côté `n` :

```python
def echappe(cases, laby):
    n = len(laby)
    return (len(cases) > 0
            and teste(cases, laby)
            and cases[0] == (0, 0)
            and cases[-1] == (n - 1, n - 1))
```

---

## Exercice 2 — Liaisons ferroviaires (6 points)

### 1. Pourquoi la liste proposée n’est pas valide

Une liaison stockée dans le tableau doit être une **liaison directe** présente sur la carte, sans ville intermédiaire. Il faut donc confronter chacune des paires à l’annexe 1. L’annexe n’étant pas incluse dans le PDF reçu, je ne peux pas certifier quelles paires précises sont absentes ou non directes.

### 2.a–b. Liaisons du joueur 2 et son dictionnaire

Ces deux réponses dépendent de l’annexe 2 (liaisons blanches), absente du fichier. Elles sont à compléter à partir de la carte. Le dictionnaire doit être symétrique : si `A` est reliée directement à `B`, alors `B` doit contenir `A` dans sa liste.

### 3.a. Assertion

```python
assert len(listeLiaisons) > 0
```

### 3.b. Résultat de la fonction fournie pour le joueur 1

La fonction ne mémorise que les villes d’arrivée depuis la première ville de chaque liaison. Avec `liaisonsJoueur1`, elle renvoie :

```python
{
    "Toulouse": ["Muret", "Montauban"],
    "Gaillac": ["St Sulpice"],
    "Muret": ["Pamiers"]
}
```

Elle omet les entrées inverses (`Muret` vers `Toulouse`, etc.) et certaines villes comme clés (`Montauban`, `St Sulpice`, `Pamiers`).

### 3.c. Construction complète du dictionnaire non orienté

```python
def construireDict(listeLiaisons):
    assert len(listeLiaisons) > 0
    d = {}
    for villeA, villeB in listeLiaisons:
        if villeA not in d:
            d[villeA] = []
        if villeB not in d[villeA]:
            d[villeA].append(villeB)

        if villeB not in d:
            d[villeB] = []
        if villeA not in d[villeB]:
            d[villeB].append(villeA)
    return d
```

Le second bloc (ajout de `villeB` comme clé et de `villeA` dans sa liste) s’insère dans la boucle, après le traitement de `villeA`.

---

## Exercice 3 — Fonction `genre` et CSV (6 points)

### 1.a. CSV

CSV signifie *Comma-Separated Values* : format textuel de données tabulaires, où les champs d’une ligne sont séparés par un délimiteur (souvent une virgule ou un point-virgule).

### 1.b. Types

`prenom` est une chaîne (`str`) ; la fonction renvoie une chaîne parmi `"M"`, `"F"` et `"I"`.

### 2.a. Import

```python
import csv
```

### 2.b. Assertion de type

```python
assert isinstance(prenom, str)
```

### 2.c. Traitement robuste

Une chaîne vide ferait aussi échouer l’accès au dernier caractère. On peut renvoyer « indéterminé » :

```python
if not isinstance(prenom, str) or prenom == "":
    return "I"
```

### 3. Prise en compte des deux dernières lettres

```python
fin = prenom[-2:].lower()
if fin in liste_M2:
    return "M"
elif fin in liste_F2:
    return "F"
else:
    return "I"
```

Cette adaptation suppose que `liste_M2` et `liste_F2` contiennent des terminaisons en minuscules.

---

## Exercice 4 — Données CSV des villes (6 points)

### Partie 1

1.a. `utf-8` est un **codage de caractères**.  
1.b. Le mode `r` signifie ouverture en **lecture**.

2.a. `listeVilles` est une liste de dictionnaires (`list[dict]`).  
2.b. `listeVilles[1]` est le dictionnaire de Vannes ; par exemple :

```python
{'dep': '56', 'nom': 'Vannes', 'nb_hab_1999': '51759',
 'nb_hab_2012': '53000', 'densite': '1625', 'superficie': '32.3',
 'alt_min': '0', 'alt_max': '56'}
```

Les valeurs sont encore des chaînes à cette étape.  
2.c. Nombre d’habitants à Pontivy en 2012 :

```python
listeVilles[5]['nb_hab_2012']  # '13700' (chaîne dans le CSV brut)
```

### Partie 2

1. Après conversion des colonnes numériques, le résultat est :

```python
[('Pontivy', 192), ('Saint-Avé', 136)]
```

2. Différence cumulée de population :

```python
def variations(listeV):
    total = 0
    for d in listeV:
        total += d['nb_hab_2012'] - d['nb_hab_1999']
    return total
```

3. Ville de superficie maximale :

```python
def surmax(listeV):
    meilleure = listeV[0]
    for d in listeV[1:]:
        if d['superficie'] > meilleure['superficie']:
            meilleure = d
    return meilleure['nom']
```

Avec les données fournies, le résultat est **Guidel** (52,29 km²).

---

## Exercice 5 — Dates et dictionnaires (6 points)

### 1.a–b.

`jours[1]` vaut `"lundi"`. Comme `18 % 7 == 4`, `jours[18 % 7]` vaut `"jeudi"`.

### 2. Jour de la semaine `n` jours plus tard

La méthode s’écrit avec des parenthèses : `jours.index(j)`.

```python
numero_jour = (jours.index(j) + n) % 7
```

### 3.a–b. Mois

Nombre de jours de mars : `mois[3][1]`, donc `31`.

```python
numero_futur = (numero_mois - 1 + x) % 12 + 1
nom_futur = mois[numero_futur][0]
```

### 4.a.

Pour `date = ("samedi", 21, 10, 1995)`, `mois[date[2]][1]` vaut **31**.

### 4.b. Fonction `jour_suivant`

```python
def jour_suivant(date):
    nom_jour, numero, num_mois, annee = date
    prochain_jour = jours[(jours.index(nom_jour) + 1) % 7]

    if numero < mois[num_mois][1]:
        return (prochain_jour, numero + 1, num_mois, annee)

    if num_mois == 12:
        return (prochain_jour, 1, 1, annee + 1)
    return (prochain_jour, 1, num_mois + 1, annee)
```

---

## Exercice 6 — Chiffrement par substitution (6 points)

### 1.a–b.

`alpha["D"]` vaut `"C"`. Pour `"BAGAGE"`, on remplace chaque lettre : `B→D`, `A→B`, `G→E`, `A→B`, `G→E`, `E→F`. Résultat : **`"DBEBEF"`**.

### 2. Fonction de chiffrement

```python
def chiffrer(mot, alpha):
    resultat = ""
    for lettre in mot:
        resultat += alpha[lettre]
    return resultat
```

### 3.a–c. Déchiffrement

Le dictionnaire inverse est :

```python
{"B": "A", "D": "B", "A": "C", "C": "D",
 "F": "E", "G": "F", "E": "G"}
```

Fonction générale :

```python
def dico_dechiffrement(dico):
    nouveau = {}
    for lettre, code in dico.items():
        nouveau[code] = lettre
    return nouveau

def dechiffre(mot, dico):
    return chiffrer(mot, dico_dechiffrement(dico))
```

### 4. Création d’un dictionnaire aléatoire

```python
from random import shuffle

def dico_chiffrement(alphabet):
    melange = alphabet.copy()
    shuffle(melange)
    return {alphabet[i]: melange[i] for i in range(len(alphabet))}
```

---

## Exercice 7 — Salle d’attente et tri (6 points)

### 1. Ajouter le patient

```python
attente.append((50, 4))
```

### 2.a–b.

Le code réalise un **tri par sélection**. Le tri par sélection est quadratique (`O(n²)`) ; le tri par insertion est quadratique dans le cas moyen et le pire cas, mais linéaire (`O(n)`) dans le meilleur cas (liste déjà triée).

### 3.a–b.

Dans une liste triée par priorité croissante, le patient prioritaire est en première position :

```python
def quitte(attente):
    return attente[1:]
```

Après le départ du patient prioritaire, les priorités restantes diminuent de 1 :

```python
def maj(attente):
    return [(patient, priorite - 1) for patient, priorite in attente]
```

### 4.a. Priorité d’un patient

```python
def priorite(attente, p):
    for patient, prio in attente:
        if patient == p:
            return prio
    return None  # si le patient n'est pas dans la liste
```

### 4.b. Révision de priorité

```python
def revise(attente, p):
    nouvelle = []
    n = priorite(attente, p)
    for patient, prio in attente:
        if patient == p:
            nouvelle.append((patient, 1))
        elif prio < n:
            nouvelle.append((patient, prio + 1))
        else:
            nouvelle.append((patient, prio))
    return nouvelle
```

---

## Exercice 8 — Recherche des k plus proches voisins (8 points)

### 1. Bloc manquant pour `k = 1`

```python
d = distance(t[idx], cible)
if d < dmin:
    dmin = d
    idx_ppv = idx
```

### 2. Complexité

La boucle examine les `n` éléments une fois ; la complexité est **O(n)** (en supposant le coût de `distance` constant).

### 3.a. Éviter les appels répétés à `distance`

Calculer une fois `d = distance(obj, cible)` avant le `if`, puis réutiliser `d` dans les tests et l’appel à `insertion`.

### 3.b. Intérêt de l’ordre décroissant

Le premier élément de `kppv` est alors le plus éloigné parmi les k voisins actuellement retenus. On peut comparer rapidement le nouveau candidat au plus mauvais des k retenus et le supprimer si nécessaire.

### 3.c. Insertion en ordre décroissant

```python
def insertion(kppv, idx, d):
    i = 0
    while i < len(kppv) and kppv[i][1] >= d:
        i += 1
    kppv.insert(i, (idx, d))
```

La liste conserve un ordre décroissant sur la distance.

---

## Exercice 9 — Anagrammes et jeu de lettres (8 points)

### Partie A

1. Signatures : `BEAU → ABEU`, `PIRATE → AEIPRT`, `PATRIE → AEIPRT`.

2. Les affichages de `signature('PLANTE')` sont, dans l’ordre :

```text
LP
ALP
ALNP
ALNPT
AELNPT
```

3. Fonction de test :

```python
def sont_anagrammes(mot1, mot2):
    return sorted(mot1) == sorted(mot2)
```

4. Résultat de `mystere(...)` (ordre des clés non essentiel) :

```python
{
    'AEIPRT': ['PIRATE', 'PATRIE', 'PARTIE'],
    'ABEU': ['AUBE', 'BEAU'],
    'AEEHLNPT': ['ELEPHANT']
}
```

5. Insertion d’une lettre :

```python
def inserer_lettre(mot, lettre, pos):
    return mot[:pos] + lettre + mot[pos:]
```

### Partie B

La numérotation saute de la question 4 à la question 6 dans le PDF : **l’énoncé de la question 5 manque**.

6. Score d’un mot :

```python
def score_mot(mot):
    score_lettres = {'A': 1, 'B': 3, 'C': 3, 'D': 2, 'E': 1,
                     'F': 4, 'G': 2, 'H': 4, 'I': 1, 'J': 8,
                     'K': 10, 'L': 1, 'M': 2, 'N': 1, 'O': 1,
                     'P': 3, 'Q': 8, 'R': 1, 'S': 1, 'T': 1,
                     'U': 1, 'V': 4, 'W': 10, 'X': 10,
                     'Y': 10, 'Z': 10}
    total = 0
    for lettre in mot:
        total += score_lettres[lettre]
    return total
```

7. Mot de score maximal (en cas d’égalité, le premier est conservé) :

```python
def meilleur_mot(liste_mots):
    meilleur = liste_mots[0]
    for mot in liste_mots[1:]:
        if score_mot(mot) > score_mot(meilleur):
            meilleur = mot
    return meilleur
```

Le mot indiqué par l’énoncé est `CHAPEAU`.

8. Compléter le chevalet :

```python
while m < 7:
    # ...
    tirage = lettres[nb_alea]
```

9. Dans `open(..., 'r', ...)`, `r` signifie **lecture** (*read*).

10. Script de recherche des mots, choix du meilleur et recomplètement du chevalet :

```python
chevalet = pioche([])
mots_possibles = []
liste_signatures = signatures_depuis_tirage(chevalet)

for sig in liste_signatures:
    for mot in dico.get(sig.lower(), []):  # le fichier de mots est en minuscules
        mot_maj = mot.upper()
        if mot_maj not in mots_possibles:
            mots_possibles.append(mot_maj)

if mots_possibles:
    mot_choisi = meilleur_mot(mots_possibles)
    print(mots_possibles)
    print(mot_choisi)
    chevalet = chevalet_apres_depot(chevalet, list(mot_choisi))
    chevalet = pioche(chevalet)
else:
    print("Aucun mot valide")
```

Pour la question 8, les compléments sont `while m < 7:` et `tirage = lettres[nb_alea]`.

---

## Exercice 10 — Réductions commerciales (6 points)

### 1.a–b.

```python
def total_hors_reduction(tab):
    return sum(tab)

def offre_bienvenue(tab):
    somme = 0
    if len(tab) > 0:
        somme = tab[0] * 0.8
    if len(tab) > 1:
        somme += tab[1] * 0.7
    for i in range(2, len(tab)):
        somme += tab[i]
    return somme
```

### 2. Prix soldé selon le nombre d’articles

```python
def prix_solde(tab):
    n = len(tab)
    if n == 0:
        return 0
    taux_a_payer = 0.5 if n >= 5 else 1 - n / 10
    return total_hors_reduction(tab) * taux_a_payer
```

### 3.a–b.

```python
def minimum(tab):
    mini = tab[0]  # tableau supposé non vide
    for x in tab[1:]:
        if x < mini:
            mini = x
    return mini

def offre_bon_client(tab):
    if len(tab) < 2:
        return total_hors_reduction(tab)
    return total_hors_reduction(tab) - minimum(tab)
```

### 4. Offre de déstockage

Prix total de l’exemple : `122 €`. Les deux articles offerts dans l’ordre fourni coûtent `6 + 5 = 11 €`, d’où `111 €`.

(a) Un ordre différent donnant un total différent :

```python
[6, 5, 10.5, 15, 20, 30.5, 35]
```

Les cadeaux sont `5 €` et `15 €`, donc le total payé est `122 - 20 = 102 €`.

(b) Le meilleur total possible est **111 €** : au plus deux groupes complets de trois articles sont formés ; les deux articles les moins chers, `5 €` et `6 €`, peuvent être offerts. On ne peut pas offrir moins que ces deux prix.

(c) Pour garantir ce minimum, placer chacun des `q = len(tab)//3` articles les moins chers dans un groupe différent, puis compléter ces groupes avec des articles plus chers. Les articles restants (0, 1 ou 2) ne forment pas un groupe et ne bénéficient pas de l’offre. Cela permet d’offrir les `q` articles les moins chers.

---

## Exercice 11 — Palindromes (6 points)

### 1.a. Cas de base récursif

La bonne proposition est la **proposition 3** :

```python
if len(txt) < 2:
    return True
```

Un texte vide ou d’une seule lettre est un palindrome.

### 1.b. Appel `palindrome("bonjour")`

`txt[0] = "b"`, `txt[taille - 1] = "r"` et `interieur = "onjou"`.

### 2. Deux tests de nature différente

```python
palindrome("radar")  # True : palindrome
palindrome("python") # False : non-palindrome
```

On peut aussi tester les cas limites `""` et un texte d’une seule lettre.

### 3. Version non récursive

```python
def palindrome(txt):
    gauche = 0
    droite = len(txt) - 1
    while gauche < droite:
        if txt[gauche] != txt[droite]:
            return False
        gauche += 1
        droite -= 1
    return True
```

### 4.a–c. Complémentarité

```python
def complementaire(txt):
    comp = {"A": "T", "T": "A", "G": "C", "C": "G"}
    return "".join(comp[lettre] for lettre in txt)
```

Le complément de `GATCGT` est `CTAGCA`. La concaténation `GATCGTCTAGCA` n’est **pas** un palindrome (première et dernière lettres différentes). La séquence n’est donc pas palindromique au sens défini.

```python
def est_palindromique(txt):
    return palindrome(txt + complementaire(txt))
```

---

## Exercice 12 — Automate cellulaire et conversions binaires (8 points)

### Partie A — Pixels

Règle : un pixel intérieur vaut 1 si les deux pixels de la ligne précédente situés en haut à gauche et en haut à droite sont différents ; sinon il vaut 0.

1.a. Avec première ligne `00010010`, les lignes suivantes sont :

```text
Ligne 0 : 00010010
Ligne 1 : 00101100
Ligne 2 : 01001110
Ligne 3 : 00111010
Ligne 4 : 01101000
```

1.b. Pour le pixel `(ligne 4, colonne 1)`, les voisins demandés sont `(ligne 3, colonne 0)` et `(ligne 3, colonne 2)`.

1.c. Pour un pixel `(li, co)`, ils sont `(li - 1, co - 1)` et `(li - 1, co + 1)`.

2.a. Le pixel `image[li][co]` vaut 1 si et seulement si :

```python
image[li - 1][co - 1] != image[li - 1][co + 1]
```

2.b–c. Fonctions :

```python
def remplir_ligne(image, li):
    image[li][0] = 0
    image[li][7] = 0
    for co in range(1, 7):
        image[li][co] = int(image[li - 1][co - 1] != image[li - 1][co + 1])

def remplir(image):
    for li in range(1, len(image)):
        remplir_ligne(image, li)
```

### Partie B — Binaire

1.a. `[0, 0, 1, 0, 1, 1, 0, 0]` représente `00101100₂`, soit **44**.

1.b. Conversion binaire vers décimal :

```python
def conversion2_10(tab):
    total = 0
    for i in range(8):
        total += tab[i] * 2 ** (7 - i)
    return total
```

1.c. `78 = 64 + 8 + 4 + 2`, donc son écriture sur 8 bits est `01001110` :

```python
[0, 1, 0, 0, 1, 1, 1, 0]
```

2.a. Pour que la première ligne ait ses bords blancs, `n` doit avoir un bit de poids fort nul et un bit de poids faible nul : `0 <= n < 128` et `n % 2 == 0` (avec la contrainte donnée `0 <= n <= 255`).

2.b. Fonction `generer` :

```python
def generer(n, k):
    tab = [None for i in range(k)]
    image = [[0 for j in range(8)] for i in range(k + 1)]
    image[0] = conversion10_2(n)
    for i in range(1, k + 1):
        remplir_ligne(image, i)
        tab[i - 1] = conversion2_10(image[i])
    return tab
```

Pour `n = 18`, les premiers termes calculés sont bien `44`, puis `78`.

---

## Exercice 13 — Détection de palindrome et complexité (6 points)

### 1. Trace de `palindrome1("rotor")`

| Étape | `i <= j` | Comparaison | `i` après | `j` après | `p` |
|---|---|---|---:|---:|---|
| Initialisation | — | — | 0 | 4 | True |
| 1 | True | `r != r` → False | 1 | 3 | True |
| 2 | True | `o != o` → False | 2 | 2 | True |
| 3 | True | `t != t` → False | 3 | 1 | True |
| Sortie | False | — | 3 | 1 | True |

### 2. Comparaisons

(a) Pour `rotor`, **3** comparaisons sont exécutées, y compris celle du caractère central avec lui-même.  
(b) Pour un mot de longueur `n`, le pire cas effectue `⌈n/2⌉` comparaisons avec la condition `i <= j` de l’énoncé.

### 3. Terminaison

À chaque tour, `i` augmente de 1 et `j` diminue de 1 ; `j - i` diminue donc de 2. Tant que la boucle est active, `i <= j`, donc la différence est bornée inférieurement : elle ne peut rester active indéfiniment. **Précision formelle :** si un variant doit être strictement positif à chaque entrée, on choisit plutôt `j - i + 1` ; `j-i` vaut 0 au tour où `i == j`.

### 4. Mot `routeur` et amélioration

`routeur` contient 7 lettres. La boucle d’origine effectue **4 tours** (`ceil(7/2)`), même après avoir trouvé une différence. Une version évitant les comparaisons inutiles s’arrête dès que le résultat est faux :

```python
def palindrome2(mot):
    i = 0
    j = len(mot) - 1
    p = True
    while i < j and p:
        if mot[i] != mot[j]:
            p = False
        i += 1
        j -= 1
    return p
```

Pour `routeur`, elle ne fait que **2 tours** : `r/r`, puis `o/u`.

---

## Exercice 14 — Recadrage intelligent (seam carving) (8 points)

### 1. Taille des données de pixels

`1000 × 1500 × 3 = 4 500 000` octets, soit **4,5 Mo** en base décimale (environ 4,29 Mio). L’en-tête est exclu.

### 2. Indices compatibles

Pour une image de hauteur `n` et de largeur `m` : `0 <= i < n` et `0 <= j < m`.

### 3.a–b. Tableau des énergies

`tab_energie` est une liste de listes de nombres (int ou float). La fonction appelle `energie` une fois par pixel, donc **n × m appels**.

### 4. Énergie d’une couture

```python
def calcule_energie(couture, tab_energie):
    total = 0
    for i, j in couture:
        total += tab_energie[i][j]
    return total
```

### 5. Positions voisines compatibles

```python
def indices_proches(m, i, j):
    resultat = []
    for colonne in (j - 1, j, j + 1):
        if 0 <= colonne < m:
            resultat.append((i, colonne))
    return resultat
```

### 6. Couture gloutonne à partir de la colonne `j`

```python
def calcule_couture(j, tab_energie):
    n = len(tab_energie)
    m = len(tab_energie[0])
    couture = [(0, j)]
    for i in range(1, n):
        voisins = indices_proches(m, i, couture[-1][1])
        couture.append(min_energie(tab_energie, voisins))
    return couture
```

### 7. Meilleure couture parmi les colonnes de départ

```python
def meilleure_couture(tab_energie):
    m = len(tab_energie[0])
    meilleure = calcule_couture(0, tab_energie)
    energie_min = calcule_energie(meilleure, tab_energie)
    for j in range(1, m):
        candidate = calcule_couture(j, tab_energie)
        e = calcule_energie(candidate, tab_energie)
        if e < energie_min:
            meilleure = candidate
            energie_min = e
    return meilleure
```

---

## Exercice 15 — QCM, notes et classement (8 points)

### 1. Liste des réponses correctes

```python
def corrige(cop, corr):
    return [cop[i] == corr[i] for i in range(len(cop))]
```

### 2. Note sans liste auxiliaire

```python
def note(cop, corr):
    total = 0
    for i in range(len(cop)):
        if cop[i] == corr[i]:
            total += 1
    return total
```

Pour Tom Matt, la note est **6**.

### 3. Notes d’un paquet

```python
def notes_paquet(p, corr):
    resultat = {}
    for nom, copie in p.items():
        resultat[nom] = note(copie, corr)
    return resultat
```

Pour `p1` et `corr0`, le résultat fourni par l’énoncé est `{('Tom','Matt'): 6, ('Lambert','Ginne'): 4, ('Carl','Roth'): 2, ('Kurt','Jett'): 4, ('Ayet','Finzerb'): 3}`.

### 4–5. Clés et identifiants

Une liste ne peut pas servir de clé d’un dictionnaire Python car elle est mutable et non hachable. Un tuple dont les éléments sont hachables convient. Pour éviter les homonymes, attribuer à chaque copie un identifiant pseudonyme unique, stocké séparément des données nominatives, avec accès limité et durée de conservation maîtrisée.

### 6. Résultat de `enigme`

```python
(
    (('Tom', 'Matt'), 6),
    (('Lambert', 'Ginne'), 4),
    (('Kurt', 'Jett'), 4),
    {('Carl', 'Roth'): 2, ('Ayet', 'Finzerb'): 3}
)
```

La fonction conserve les trois meilleures notes dans `a`, `b`, `c` et place les autres candidats dans `d`. En cas d’égalité, l’ordre d’insertion peut influer sur la répartition.

### 7–8. Rôle et moins de trois entrées

`enigme` sélectionne les trois candidats de meilleures notes, dans l’ordre décroissant, et renvoie les autres dans un dictionnaire. Avec moins de trois entrées, les variables correspondant aux places non occupées valent `None` ; `d` est vide. Pour zéro entrée : `(None, None, None, {})`.

### 9. Classement complet en réutilisant `enigme`

```python
def classement(notes):
    if not notes:
        return []
    a, b, c, reste = enigme(notes)
    resultat = [x for x in (a, b, c) if x is not None]
    return resultat + classement(reste)
```

Pour les données proposées, on obtient l’ordre de l’exemple : Tom (6), Lambert (4), Kurt (4), Ayet (3), Carl (2).

### 10. Recherche dichotomique du préfixe vrai

```python
def renote_express2(copcorr):
    gauche = 0
    droite = len(copcorr)  # borne droite exclusive, sentinelle après la liste
    while droite - gauche > 1:
        milieu = (gauche + droite) // 2
        if copcorr[milieu]:
            gauche = milieu
        else:
            droite = milieu
    if copcorr[gauche]:
        return droite
    else:
        return gauche
```

Cette version renvoie la longueur du préfixe de `True`. Elle fonctionne aussi lorsque tout est `True` (renvoie `len(copcorr)`). La fonction linéaire donnée dans le sujet devrait, elle, traiter explicitement ce cas pour éviter un dépassement d’indice.

### 11. Coûts

`renote_express` est en **O(n)** dans le pire cas ; `renote_express2` est en **O(log n)**, grâce à la recherche dichotomique.

### 12. Calcul direct de la note

Avec la propriété « vrai puis faux », on peut tester directement `cop[i] == corr[i]` au milieu : cette valeur est vraie exactement dans le préfixe correct. Il n’est pas nécessaire de construire une liste de booléens.

```python
def note_express(cop, corr):
    gauche = 0
    droite = len(cop)
    while droite - gauche > 1:
        milieu = (gauche + droite) // 2
        if cop[milieu] == corr[milieu]:
            gauche = milieu
        else:
            droite = milieu
    if cop[gauche] == corr[gauche]:
        return droite
    return gauche
```

Cette méthode requiert l’hypothèse forte de l’énoncé : dès la première erreur, toutes les réponses suivantes sont fausses. Sans elle, il faut examiner toute la copie.

---

## Points à confirmer avant l’application

1. Validez-vous la répartition proposée (**E8, E9, E12, E14 et E15 sur 8 ; les autres sur 6**, total 100) ?
2. Pouvez-vous fournir les annexes cartographiques manquantes de l’exercice 2 si vous souhaitez un corrigé complet des questions 1 et 2 ?
3. Le PDF transmis ne contient pas la question 5 de l’exercice 9 : souhaitez-vous que l’application l’ignore, ou avez-vous une page complémentaire ?

**Je n’entame pas la création de l’application avant votre validation.**