# Numpy - Extended

> Rozšiřující část je nepovinná a slouží pouze jako další informační zdroj pro studenty.

Tato rozšiřující část navazuje na základní kapitolu o NumPy. Nejde o nezbytné minimum, ale o přehled možností, které se v praxi velmi často hodí a které stojí za to znát, až základní práci s poli zvládnete. Podíváme se na rozdíl mezi kopií a pohledem, výběr prvků pomocí podmínek, univerzální funkce, lineární algebru, statistické míry rozptylu a práci s chybějícími či nekonečnými hodnotami. Ukázky předpokládají, že máte naimportováno `import numpy as np`.

## Kopie a pohledy na pole (Copy vs. View)

V základní části jsme viděli, že řez pole není kopie. NumPy z důvodu efektivity často nevytváří nová data, ale jen nový **pohled** (*view*) na stejný blok paměti. Změna prvku v pohledu se projeví i v původním poli a naopak. **Kopie** (*copy*) je naproti tomu nezávislé pole s vlastními daty.

```python
a = np.arange(6)         # [0 1 2 3 4 5]

pohled = a[1:4]          # řez je pohled
pohled[0] = 99
print(a)                 # [ 0 99  2  3  4  5], změnilo se i původní pole

kopie = a[1:4].copy()    # výslovná kopie
kopie[0] = -1
print(a)                 # beze změny
```

Zda pole sdílí data s jiným polem, lze zjistit funkcí `np.shares_memory` nebo atributem `.base`, který u pohledu odkazuje na původní pole. Pohledy vytváří řezy a většina operací měnících tvar (`reshape`, `.T`), kopie naopak vznikají při výběru pomocí seznamu indexů nebo logické masky a při aritmetických operacích.

```python
b = a.reshape(2, 3)
print(np.shares_memory(a, b))        # True, reshape vrací pohled
print(np.shares_memory(a, a.copy())) # False
```

{% hint style="info" %}
Pokud potřebujete výsledek, který se při dalších úpravách nebude ovlivňovat s původními daty, vždy použijte `.copy()`. Je to častý zdroj chyb u funkcí, které pole upravují „na místě“.
{% endhint %}

## Boolean Masking

Relační operace vracejí pole logických hodnot. Toto pole lze použít jako **masku**, tedy jej vložit do hranatých závorek a vybrat tak jen prvky, kterým odpovídá `True`. Je to jedna z nejpoužívanějších technik v NumPy i v Pandas.

```python
teploty = np.array([12.5, 18.0, 25.3, 30.1, 8.2, 22.7])

maska = teploty > 20
print(maska)              # [False False  True  True False  True]
print(teploty[maska])     # [25.3 30.1 22.7]

# zkrácený zápis
print(teploty[teploty > 20])

# kombinace podmínek, každá v závorce
print(teploty[(teploty > 10) & (teploty < 25)])   # [12.5 18.  22.7]
```

Maskou lze také hodnoty měnit. Například ořezání hodnot nad nějakou mez:

```python
teploty[teploty > 28] = 28.0
print(teploty)    # [12.5 18.  25.3 28.  8.2 22.7]
```

Výběr pomocí masky vždy vrací jednorozměrné pole, i když byl vstup vícerozměrný, protože tvar výsledku závisí na počtu vyhovujících prvků. Počet prvků splňujících podmínku zjistíme jako `np.sum(maska)`.

## Podmíněný výběr (where)

Funkce **`np.where`** je vektorová obdoba podmíněného výrazu `a if podmínka else b`. Pro každý prvek rozhodne podle podmínky, zda vezme hodnotu z druhého, nebo třetího argumentu.

```python
body = np.array([35, 80, 55, 92, 40])

vysledek = np.where(body >= 50, "uspěl", "neuspěl")
print(vysledek)    # ['neuspěl' 'uspěl' 'uspěl' 'uspěl' 'neuspěl']

# číselná varianta: záporná čísla nahradit nulou
x = np.array([-2, 5, -1, 8])
print(np.where(x < 0, 0, x))    # [0 5 0 8]
```

Pokud `np.where` voláme jen s podmínkou, vrátí **indexy** prvků, pro které platí:

```python
indexy = np.where(body > 50)
print(indexy)            # (array([1, 2, 3]),)
print(body[indexy])      # [80 55 92]
```

## Univerzální funkce (ufuncs)

**Univerzální funkce** (*universal functions*, zkráceně *ufuncs*) jsou funkce, které se aplikují na pole po prvcích, rychle a s podporou broadcastingu. S řadou z nich jsme se již setkali: operátory `+`, `*` i funkce `np.sin` nebo `np.sqrt` jsou ufuncs.

```python
x = np.array([1.0, 4.0, 9.0])

print(np.sqrt(x))         # [1. 2. 3.]
print(np.log(x))          # přirozený logaritmus
print(np.exp(np.array([0, 1, 2])))
print(np.maximum(np.array([1, 5, 3]), np.array([4, 2, 6])))   # [4 5 6], po prvcích větší hodnota
print(np.clip(np.array([-5, 3, 12]), 0, 10))                  # [ 0  3 10], ořezání na interval
```

Ufuncs mají užitečné metody pro opakované použití operace. Například `np.add.reduce` je součet a `np.add.accumulate` kumulativní součet (u stejné funkce `np.cumsum`):

```python
a = np.array([1, 2, 3, 4])
print(np.add.reduce(a))        # 10
print(np.cumsum(a))            # [ 1  3  6 10]
print(np.cumprod(a))           # [ 1  2  6 24]
```

Hlavním důvodem, proč dávat přednost ufuncs před vlastními cykly, je rychlost. Pokud potřebujete aplikovat vlastní funkci napsanou pro jednu hodnotu, lze ji zabalit pomocí `np.vectorize`, ale ta je jen pohodlnou obálkou nad cyklem a rychlost nezlepší.

## Matice a lineární algebra (numpy.linalg)

Dvourozměrná pole slouží jako matice. Maticové násobení provádí operátor `@`, který jsme už zmínili. Modul **`numpy.linalg`** obsahuje další běžné operace.

```python
A = np.array([[2.0, 1.0],
              [1.0, 3.0]])
b = np.array([5.0, 10.0])

print(A @ A)                    # maticový součin
print(np.linalg.det(A))         # determinant: 5.0
print(np.linalg.inv(A))         # inverzní matice

x = np.linalg.solve(A, b)       # řešení soustavy A·x = b
print(x)                        # [1. 3.]
print(A @ x)                    # [ 5. 10.], kontrola

print(np.linalg.norm(b))        # euklidovská norma vektoru
print(np.linalg.matrix_rank(A)) # hodnost matice
```

Soustavu rovnic je vždy lepší řešit pomocí `solve` než počítat inverzní matici, protože je to rychlejší a číselně stabilnější. Pro pokročilejší úlohy, jako jsou vlastní čísla (`np.linalg.eig`) nebo rozklady matic (`svd`, `qr`), modul poskytuje další funkce. Podrobněji se těmto tématům věnuje kapitola o SciPy.

## Standardní odchylka a rozptyl

Kromě průměru z základní části se k popisu dat často používají míry rozptylu. **Rozptyl** (*variance*) udává průměrný čtverec odchylky od průměru a **standardní odchylka** (*standard deviation*) je jeho odmocnina, takže má stejnou jednotku jako data.

```python
data = np.array([2, 4, 4, 4, 5, 5, 7, 9])

print(data.mean())    # 5.0
print(data.var())     # 4.0
print(data.std())     # 2.0

m = np.array([[1, 2, 3],
              [4, 8, 12]])
print(m.std(axis=0))  # směrodatná odchylka po sloupcích
```

{% hint style="warning" %}
Výchozí chování `var` a `std` je výpočet pro celou populaci (dělení počtem prvků N). Pro **výběrovou** směrodatnou odchylku z dat, která jsou jen vzorkem, se použije `data.std(ddof=1)`, která dělí N − 1.
{% endhint %}

Užitečnou pomůckou je i `np.median` pro medián, `np.percentile` pro percentily a `np.corrcoef` pro korelační koeficient dvou veličin.

## Práce s NaN a Inf

Při práci s reálnými daty se často setkáme s chybějícími nebo nedefinovanými hodnotami. NumPy pro ně používá speciální hodnoty typu `float`: **`np.nan`** (*Not a Number*, nedefinovaná hodnota) a **`np.inf`** (nekonečno). Vznikají například při dělení nulou nebo načtením prázdných polí z dat.

```python
a = np.array([1.0, np.nan, 3.0])

print(np.sum(a))              # nan, jediné NaN „otráví“ celý výsledek
print(np.isnan(a))            # [False  True False]

print(np.nansum(a))           # 4.0, nan se ignoruje
print(np.nanmean(a))          # 2.0
print(np.nanmax(a))           # 3.0

print(np.array([1.0]) / 0)    # [inf], s varováním
print(np.isinf(np.array([1, np.inf])))    # [False  True]
```

Pro ošetření NaN se hodí kombinace s maskou a funkcí `np.nan_to_num`, která nahradí NaN nulou a nekonečna největšími reprezentovatelnými čísly:

```python
cista = a[~np.isnan(a)]          # [1. 3.], odstranění NaN
print(np.nan_to_num(a, nan=0.0)) # [1. 0. 3.]
```

Pozor na to, že `np.nan == np.nan` je `False`, proto se na NaN musí ptát funkce `np.isnan`, nikoli porovnání. U celočíselných polí NaN neexistuje. Pro řadu těchto úloh bude praktičtější knihovna Pandas, která s chybějícími hodnotami pracuje pohodlněji.

## Shrnutí

Rozšiřující funkce NumPy se točí kolem efektivní práce s daty bez cyklů. Pohledy a kopie určují, zda se data sdílejí. Boolean masking a `np.where` umožňují vybírat a upravovat prvky podle podmínek. Univerzální funkce zajišťují rychlé operace po prvcích a `numpy.linalg` nabízí základy lineární algebry. K popisu dat slouží rozptyl a standardní odchylka a funkce rodiny `nan*` umožňují počítat s chybějícími hodnotami.
