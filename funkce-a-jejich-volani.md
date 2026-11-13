# Funkce a jejich volání

Funkce jsou základním stavebním kamenem programů. Umožňují rozdělit složitý problém na menší části, kód použít opakovaně a dát mu srozumitelný název. V Pythonu jsou funkce navíc velmi flexibilní: parametry lze předávat podle pozice i podle jména, mohou mít výchozí hodnoty, funkce lze ukládat do proměnných, předávat jako argumenty a vracet z jiných funkcí. V této kapitole si ukážeme, jak funkce definovat a volat, jak pracovat s pojmenovanými a výchozími parametry, jak funguje `lambda` a v čem spočívá myšlenka, že funkce je objekt. Na závěr si vysvětlíme zápisy `*args` a `**kwargs`, které umožňují psát funkce s proměnným počtem parametrů.

## Definice a volání funkce

Funkci definujeme klíčovým slovem `def`, za kterým následuje název, parametry v závorkách a dvojtečka. Tělo funkce je odsazený blok. Hodnotu vrátíme příkazem `return`.

```python
def pozdrav(jmeno):
    return f"Ahoj, {jmeno}!"

zprava = pozdrav("Jana")
print(zprava)    # Ahoj, Jana!
```

Funkce, která neobsahuje `return` nebo používá `return` bez hodnoty, vrací `None`. Z funkce lze vrátit více hodnot najednou, ve skutečnosti se vrací jedna n-tice, kterou si lze hned rozbalit:

```python
def min_max(cisla):
    return min(cisla), max(cisla)

nejmensi, nejvetsi = min_max([4, 8, 1, 9])
print(nejmensi, nejvetsi)    # 1 9
```

První řádek tvoří označení funkce a bezprostředně pod ním se podle konvence píše **dokumentační řetězec** (*docstring*) v trojitých uvozovkách. Zobrazí ho nápověda `help()` a editory. Pro čitelnost lze doplnit i type hints z druhé kapitoly:

```python
def obsah_obdelniku(sirka: float, vyska: float) -> float:
    """Vrátí obsah obdélníku o zadané šířce a výšce."""
    return sirka * vyska

help(obsah_obdelniku)
```

Proměnné vytvořené uvnitř funkce jsou **lokální**: existují jen během jejího volání a nejsou vidět zvenčí. Funkce může číst proměnné z vnějšího (např. globálního) rozsahu, ale pro jejich přepsání by musela použít klíčová slova `global` nebo `nonlocal`. V praxi je lepší tomu se vyhnout a data funkci předávat parametry a výsledky vracet přes `return`.

```python
pocitadlo = 0

def spatne():
    pocitadlo = 5      # vytvoří novou lokální proměnnou
    return pocitadlo

spatne()
print(pocitadlo)       # 0, globální proměnná se nezměnila
```

### Jak se předávají argumenty

Při volání funkce se nepředává kopie hodnoty, ale **odkaz na objekt**. Důsledky jsou stejné jako u přiřazení z předchozí kapitoly. Pokud funkce dostane měnitelný objekt, například seznam, a změní ho, změna je viditelná i mimo funkci. U neměnitelných objektů (čísla, řetězce, n-tice) taková změna není možná.

```python
def pridej_prvek(seznam):
    seznam.append(99)

moje = [1, 2, 3]
pridej_prvek(moje)
print(moje)    # [1, 2, 3, 99]

def zvys(cislo):
    cislo += 1      # vytvoří nový int, původní proměnná se nezmění

x = 5
zvys(x)
print(x)       # 5
```

Pokud nechceme měnit původní data, je třeba vytvořit kopii, nebo ještě lépe nový výsledek vrátit a původní seznam nechat být.

## Pojmenované argumenty a výchozí parametry

Argumenty lze předávat podle pozice, jak je zvykem z jiných jazyků, nebo podle jména. U **pojmenovaných argumentů** (*keyword arguments*) uvedeme při volání název parametru. Pořadí pak nehraje roli a kód je výrazně čitelnější, zvláště u funkcí s více parametry.

```python
def vytvor_uzivatele(jmeno, vek, mesto):
    return {"jmeno": jmeno, "vek": vek, "mesto": mesto}

# podle pozice
vytvor_uzivatele("Jana", 25, "Brno")

# podle jména, pořadí je libovolné
vytvor_uzivatele(mesto="Brno", jmeno="Jana", vek=25)

# kombinace: nejprve poziční, potom pojmenované
vytvor_uzivatele("Jana", 25, mesto="Brno")
```

Při kombinaci musí poziční argumenty vždy předcházet pojmenovaným.

**Výchozí hodnoty** parametrů se zadávají při definici funkce. Pokud volající argument vynechá, použije se výchozí hodnota. Díky tomu lze funkci volat jednoduše v běžných případech, ale přesto umožnit podrobné nastavení.

```python
def pozdrav(jmeno, osloveni="Ahoj", konec="!"):
    return f"{osloveni}, {jmeno}{konec}"

print(pozdrav("Jana"))                       # Ahoj, Jana!
print(pozdrav("Jana", "Dobrý den"))          # Dobrý den, Jana!
print(pozdrav("Jana", konec="."))            # Ahoj, Jana.
```

Parametry s výchozí hodnotou musí v definici následovat až za parametry bez výchozí hodnoty. Se stejným principem se setkáte v knihovnách všude: například `print("a", "b", sep="-", end="")` používá pojmenované parametry `sep` a `end` s výchozími hodnotami.

{% hint style="danger" %}
**Měnitelná výchozí hodnota je past.** Výchozí hodnota se vyhodnotí jen jednou, při definici funkce, a pak je sdílena mezi všemi voláními. Pokud je to seznam nebo slovník, jednotlivá volání se začnou navzájem ovlivňovat.

```python
def pridej(prvek, seznam=[]):      # špatně
    seznam.append(prvek)
    return seznam

print(pridej(1))    # [1]
print(pridej(2))    # [1, 2], překvapení!
```

Správný postup je použít `None` a nový seznam vytvořit až v těle funkce:

```python
def pridej(prvek, seznam=None):
    if seznam is None:
        seznam = []
    seznam.append(prvek)
    return seznam
```
{% endhint %}

### Povinně pojmenované a pouze poziční parametry

Pro zvýšení přehlednosti lze vynutit způsob předávání. Parametry uvedené za samostatnou hvězdičkou `*` je nutné zadávat jménem, parametry před lomítkem `/` zase jen pozicí:

```python
def prevod(castka, *, mena="CZK"):
    return f"{castka} {mena}"

prevod(100, mena="EUR")    # v pořádku
# prevod(100, "EUR")       # TypeError

def delka(objekt, /):
    return len(objekt)
```

U funkcí s několika parametry stejného typu, kde by se pořadí snadno pletlo, je povinné pojmenování dobrým obranným nástrojem.

## Funkce jako objekt

V Pythonu je funkce **plnohodnotný objekt**, stejně jako číslo nebo seznam. Lze ji uložit do proměnné, vložit do kolekce, předat jako argument jiné funkci nebo ji z funkce vrátit. Při volání používáme závorky, ale samotný název funkce bez závorek označuje funkci jako objekt.

```python
def zdvoj(x):
    return 2 * x

f = zdvoj           # nevoláme, jen přiřazujeme odkaz
print(f(5))         # 10
print(f.__name__)   # zdvoj

operace = {
    "zdvoj": zdvoj,
    "umocni": lambda x: x ** 2,
}
print(operace["zdvoj"](4))    # 8
```

Funkce, která přijímá jinou funkci jako argument (nebo ji vrací), se nazývá **funkce vyššího řádu**. Typickým příkladem je parametr `key` při řazení:

```python
slova = ["pomeranč", "jablko", "kiwi", "banán"]

print(sorted(slova))                    # abecedně
print(sorted(slova, key=len))           # podle délky: ['kiwi', 'jablko', 'banán', 'pomeranč']
print(max(slova, key=len))              # pomeranč
```

Vlastní funkci vyššího řádu napíšeme snadno:

```python
def aplikuj(funkce, hodnoty):
    return [funkce(h) for h in hodnoty]

print(aplikuj(zdvoj, [1, 2, 3]))        # [2, 4, 6]
print(aplikuj(str.upper, ["a", "b"]))   # ['A', 'B']
```

Vestavěné funkce `map()` a `filter()` fungují na stejném principu: `map(f, kolekce)` použije funkci na každý prvek a `filter(f, kolekce)` ponechá jen ty, pro které funkce vrátí pravdivou hodnotu. Obě vracejí iterátory, proto je případně převádíme na seznam. V moderním kódu se ale pro tyto účely častěji používají comprehensions.

```python
cisla = [1, 2, 3, 4, 5, 6]

print(list(map(zdvoj, cisla)))               # [2, 4, 6, 8, 10, 12]
print(list(filter(lambda n: n % 2 == 0, cisla)))   # [2, 4, 6]

# ekvivalent pomocí comprehensions
print([zdvoj(n) for n in cisla])
print([n for n in cisla if n % 2 == 0])
```

Funkce lze také definovat uvnitř jiných funkcí. Vnitřní funkce si pamatuje proměnné z okolí, ve kterém vznikla, což se nazývá **uzávěr** (*closure*). Díky tomu lze vyrábět „továrny na funkce“:

```python
def nasobicka(faktor):
    def vynasob(x):
        return x * faktor
    return vynasob

trojnasobek = nasobicka(3)
print(trojnasobek(10))    # 30
```

Na tomto principu stojí i **dekorátory**, tedy funkce, které upravují chování jiných funkcí. S jedním z nich, `@dataclass`, jsme se už setkali. Jednoduchý dekorátor měřící čas může vypadat takto:

```python
import time

def mer_cas(funkce):
    def obalena(*args, **kwargs):
        zacatek = time.perf_counter()
        vysledek = funkce(*args, **kwargs)
        print(f"{funkce.__name__} trvala {time.perf_counter() - zacatek:.4f} s")
        return vysledek
    return obalena

@mer_cas
def pomala_funkce():
    time.sleep(0.5)

pomala_funkce()    # pomala_funkce trvala 0.5005 s
```

Zápis `@mer_cas` nad definicí je zkratkou pro `pomala_funkce = mer_cas(pomala_funkce)`. Dekorátor využívá zápis `*args` a `**kwargs`, který vysvětlujeme níže.

## Lambda funkce

**Lambda funkce** je krátká anonymní funkce zapsaná jediným výrazem. Má tvar `lambda parametry: výraz` a výsledek výrazu se automaticky vrací. Hodí se tam, kde potřebujeme jednoduchou funkci na jedno použití, nejčastěji jako argument `key`.

```python
druha_mocnina = lambda x: x ** 2
print(druha_mocnina(4))        # 16

soucet = lambda a, b: a + b
print(soucet(2, 3))            # 5

studenti = [("Jana", 90), ("Petr", 75), ("Eva", 82)]
studenti.sort(key=lambda s: s[1], reverse=True)
print(studenti)    # [('Jana', 90), ('Eva', 82), ('Petr', 75)]
```

Lambda může obsahovat pouze jeden výraz, nikoli více příkazů, cykly nebo přiřazení. Pro složitější logiku je třeba použít klasickou funkci pomocí `def`. Přiřazování lambdy do proměnné, jak je ukázáno v prvním příkladu, je pouze demonstrace: pro funkci, která má jméno, je podle stylových doporučení lepší `def`, protože se pak lépe čte ve výpisu chyb.

## Proměnný počet argumentů: *args a **kwargs

Někdy předem nevíme, kolik argumentů funkce dostane. Pro tyto případy slouží dva speciální zápisy.

Parametr s jednou hvězdičkou, obvykle pojmenovaný **`*args`**, sebere všechny nadbytečné poziční argumenty do **n-tice**. Parametr se dvěma hvězdičkami, **`**kwargs`**, sebere všechny nadbytečné pojmenované argumenty do **slovníku**. Názvy `args` a `kwargs` jsou pouze konvence, podstatné jsou hvězdičky.

```python
def soucet(*args):
    print(args)           # n-tice všech argumentů
    return sum(args)

print(soucet(1, 2, 3))        # (1, 2, 3) → 6
print(soucet(10, 20))         # (10, 20) → 30

def vypis_info(**kwargs):
    for klic, hodnota in kwargs.items():
        print(f"{klic} = {hodnota}")

vypis_info(jmeno="Jana", vek=25)
# jmeno = Jana
# vek = 25
```

Oba zápisy lze kombinovat s běžnými parametry. Pořadí v definici je pevné: běžné parametry, potom `*args`, pak pojmenované parametry a nakonec `**kwargs`.

```python
def vse(a, b=0, *args, volba=None, **kwargs):
    print(a, b, args, volba, kwargs)

vse(1, 2, 3, 4, volba="x", barva="červená")
# 1 2 (3, 4) x {'barva': 'červená'}
```

Hvězdičky lze použít i při **volání** funkce, kde hodnoty naopak rozbalují. Zápis `*kolekce` rozbalí kolekci na poziční argumenty a `**slovnik` na pojmenované. Jde o stejný princip rozbalování, který jsme poznali u kolekcí.

```python
def vytvor(jmeno, vek, mesto):
    return f"{jmeno} ({vek}), {mesto}"

parametry = ["Jana", 25, "Brno"]
print(vytvor(*parametry))                 # Jana (25), Brno

data = {"jmeno": "Petr", "vek": 30, "mesto": "Plzeň"}
print(vytvor(**data))                     # Petr (30), Plzeň

print(*[1, 2, 3], sep=", ")               # 1, 2, 3
```

Využití je zejména v situacích, kdy funkce jen předává parametry dál jiné funkci. Právě tak funguje výše uvedený dekorátor, který nezná parametry obalené funkce, a proto je přijme všechny pomocí `*args` a `**kwargs` a dál je beze změny předá.

{% hint style="info" %}
Zápisy `*args` a `**kwargs` vypadají jako výhodná zkratka, ale funkce s nimi hůře dokumentují, co skutečně očekávají. Pokud počet a názvy parametrů znáte, uveďte je v hlavičce funkce výslovně. Proměnné parametry používejte tam, kde je skutečně potřebujete, například u dekorátorů, obalů nebo funkcí jako `print`.
{% endhint %}

## Shrnutí

Funkce se definují pomocí `def`, vrací hodnotu příkazem `return` a pracují s lokálními proměnnými. Argumenty se předávají jako odkazy na objekty, proto je nutné dávat pozor na úpravy měnitelných objektů. Parametry lze předávat pozicí nebo jménem a opatřit je výchozími hodnotami, přičemž měnitelné výchozí hodnoty je nutné nahrazovat hodnotou `None`. Funkce jsou objekty, které lze přiřazovat do proměnných a předávat jako argumenty, což využívají funkce vyššího řádu, uzávěry a dekorátory. Lambda je zkrácený zápis jednoduché anonymní funkce. Konečně `*args` a `**kwargs` umožňují psát funkce s libovolným počtem argumentů a hvězdičkami lze argumenty při volání rozbalovat.
