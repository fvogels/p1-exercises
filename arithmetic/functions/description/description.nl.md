# Functies

## Code

Om een computer een taak te laten uitvoeren, moeten we alle instructies nauwgezet uitschrijven.
De computer zal deze instructies dan één na één uitvoeren.

Beschouw bijvoorbeeld de code hieronder.
Veel zal je er vermoedelijk niet van begrijpen, maar dat is normaal.

```python
for e in es:
    for b in bs:
        if ((e.p.x - b.p.x)**2 + (e.p.y - b.p.y)**2) <= e.r
            e.h -= b.d

es = [e for e in es if e.h > 0]
```

Voor een machine is dit perfect aanvaardbare code.
Een mens zal echter veel moeite hebben om te weten wat hier allemaal gedaan wordt.

Maar maakt het iets uit dat deze code voor ons onleesbaar is?
Zolang de machine het maar kan uitvoeren, is er toch geen probleem?

Zo eenvoudig is het echter niet.

* Soms wil het gedrag van het programma wijzigen (bv. gameplay tweaken, algoritme optimiseren, AI slimmer maken, ...).
* Bugs kunnen optreden: de foutieve code moet gecorrigeerd worden.

In beide gevallen is het daarom belangrijk dat we de intentie achter de code snel kunnen doorhebben zodat we weten welk stuk code moet aangepast worden.

## Functies Invoeren

Functies kunnen ons helpen bovenstaande code stukken leesbaarder te maken.
We kunnen bovenstaande code herschrijven als

```python
def distance_between(position1, position2):
    dx = position1.x - position2.x
    dy = position2.y - position2.y

    return (dx**2 + dy**2)**0.5

def does_bullet_hit_enemy(enemy, bullet):
    distance_between_enemy_and_bullet = distance_between(enemy.position, bullet.position)
    return distance_between_enemy_and_bullet <= enemy.size

def does_any_bullet_hit_enemy(enemy, bullets):
    return any(does_bullet_hit_enemy(enemy, bullet) for bullet in bullets)

def damage_enemy(enemy, amount):
    enemy.health -= amount

def kill_enemies_with_bullets(enemies, bullets):
    for enemy in enemies:
        if does_any_bullet_hit_enemy(enemies, bullets):
            damage_enemy(enemy, bullet.damage_amount)

def remove_dead_enemies(enemies):
    return [enemy for enemy in enemies if enemy.health > 0]

kill_enemies_with_bullets(enemies, bullets)
enemies = remove_dead_enemies(enemies)
```

We hebben de code opgedeeld in _functies_.
Dit houdt in dat we samenhorende stukken logica gegroepeerd hebben en een naam toegekend hebben.
Bijvoorbeeld, de wiskundige berekening

```python
((e.p.x - b.p.x)**2 + (e.p.y - b.p.y)**2)
```

hebben we in een functie geplaatst met als naam `distance_between`:

```python
def distance_between(position1, position2):
    dx = position1.x - position2.x
    dy = position2.y - position2.y

    return dx**2 + dy**2
```

Zelfs al begrijp je de wiskundige formule niet, je weet wel wat het verondersteld is te doen dankzij de naam van de functie.
Hetzelfde is waar over de andere stukken code: de namen `does_bullet_hit_enemy`, `remove_dead_enemies`, etc. beschrijven duidelijk wat de achterliggende code doet.

## Oh nee: een bug!

Nu blijkt dat indien je het spel opstart en begint te schieten op de vijanden, dat er iets niet klopt.
De vijanden worden beschadigd terwijl de kogels niet eens in de buurt komen.
Normaalgezien moet een kogel de vijand raken om deze te beschadigen.
Er moet dus iets mis zijn met de code die nagaat of een kogel een vijand raakt.

De meest voor de hand liggende verdachte is `does_bullet_hit_enemy`, maar deze functie doet eigenlijk bijzonder weinig:

* het berekent de afstand tussen vijand en kogel
* het vergelijkt de afstand met de grootte van de vijand

Dit ziet er helemaal correct uit.
De functie steunt echter op `distance_between`, dus misschien loopt het daar fout.
`distance_between` wordt onze nieuwe verdachte.

We kunnen beginnen staren naar de code, maar we kunnen deze functie ook gewoon eens testen zonder ons hoofd te breken over de wiskunde.
We beschouwen een eenvoudig gevallen: als we de afstand tussen `(0, 0)` en `(2, 0)` laten uitrekenen, dan verwachten we als resultaat `2`.

```python
>>> distance_between(Position(0, 0), Position(2, 0))
4
```

We verwachten `2`, maar we krijgen `4` als resultaat.
`distance_between` bevat dus duidelijk een bug.
We kunnen deze verhelpen door `distance_between` aan te passen:

```python
def distance_between(position1, position2):
    dx = position1.x - position2.x
    dy = position2.y - position2.y

    return (dx**2 + dy**2)**0.5
```

## Conclusie

Hoewel functies in strikte zin nooit echt _noodzakelijk_ zijn om een correct werkend programma te kunnen verkrijgen, bieden ze toch tal voordelen, waaronder

* De namen helpen je de code te begrijpen en sneller je weg te vinden.
* Ze voegen structuur toe aan je code.

Het is belangrijk is dat je beseft dat als je code schrijft, deze ook bestemd is voor de ogen van menselijke programmeurs (inclusief jezelf), niet enkel voor de computer.
Een goed experiment is om je eigen code na een aantal weken te herbezoeken: je zal merken dat, indien je je code niet goed schreef (bv. slechte naamgevingen, gebrekkige structuur), het heel moeilijk is om je eigen eerdere gedachtengang te begrijpen.
