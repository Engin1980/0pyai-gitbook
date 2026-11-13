# OpenCV - Extended

Rozšiřující část navazuje na základní kapitolu o OpenCV a ukazuje techniky, které z jednotlivých operací skládají užitečné aplikace: histogramy a barevné kanály, rotace a zrcadlení, adaptivní prahování, další filtry, nalezení kontur a tvarů, maskování, kreslení do obrázku, zpracování videa, detekci pohybu a základní detekci objektů. Všechny ukázky předpokládají:

```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
sedy = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
```

## Histogram obrázku

**Histogram** obrázku ukazuje, kolik pixelů má kterou hodnotu jasu. Vlevo jsou tmavé tóny, vpravo světlé. Podle jeho tvaru poznáme, zda je snímek podexponovaný (histogram „natlačený“ vlevo), přeexponovaný, nebo málo kontrastní (úzký histogram uprostřed).

```python
import matplotlib.pyplot as plt

hist = cv2.calcHist([sedy], [0], None, [256], [0, 256])

plt.plot(hist)
plt.xlabel("Jas")
plt.ylabel("Počet pixelů")
plt.show()
```

Histogram lze využít i k vylepšení obrazu. **Vyrovnání histogramu** (`cv2.equalizeHist`) rozloží jas rovnoměrněji a zvýší kontrast. Adaptivní varianta CLAHE pracuje po menších dlaždicích a lépe se vyrovná s lokálními rozdíly.

```python
vyrovnany = cv2.equalizeHist(sedy)

clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
lepsi = clahe.apply(sedy)
```

TODO: obrázek – Prompt: "Two rows: top row a dark low-contrast grayscale photo and its histogram bunched to the left; bottom row the same photo after histogram equalization, brighter with more contrast, and its histogram spread across the full range; Czech captions; flat clean style."

## Barevné kanály

Barevný obrázek se skládá ze tří kanálů (B, G, R). Ty lze oddělit funkcí `cv2.split`, upravit samostatně a opět spojit funkcí `cv2.merge`. Případně se využije indexování pole NumPy.

```python
b, g, r = cv2.split(img)

# zdůraznění červené složky
r = cv2.convertScaleAbs(r, alpha=1.3, beta=0)
upraveny = cv2.merge([b, g, r])

# totéž pomocí NumPy: vynulování zeleného kanálu
bez_zelene = img.copy()
bez_zelene[:, :, 1] = 0

# průměrná hodnota každého kanálu
print(img.mean(axis=(0, 1)))      # [B, G, R]
```

Oddělení kanálů pomáhá při analýze, například v kterém kanálu je kontrast objektu nejvyšší. Funkce `convertScaleAbs(obr, alpha, beta)` provádí lineární úpravu `alpha·pixel + beta`, tedy změnu kontrastu (`alpha`) a jasu (`beta`), s ořezáním na rozsah 0 až 255.

## Rotace a zrcadlení obrázků

Zrcadlení provádí funkce **`cv2.flip`** a otočení o násobky 90° funkce **`cv2.rotate`**. Jsou rychlé, protože pouze přeuspořádají pixely, a nevznikají žádné okraje.

```python
horizontalne = cv2.flip(img, 1)       # zrcadlení kolem svislé osy (vlevo ↔ vpravo)
vertikalne = cv2.flip(img, 0)         # kolem vodorovné osy (nahoře ↔ dole)
oboje = cv2.flip(img, -1)

o90 = cv2.rotate(img, cv2.ROTATE_90_CLOCKWISE)
o180 = cv2.rotate(img, cv2.ROTATE_180)
```

Otočení o libovolný úhel jsme viděli v základní části pomocí `getRotationMatrix2D` a `warpAffine`. Při otáčení o jiný úhel vznikají prázdné rohy, které se vyplní konstantou (`borderValue`), případně se zvětší výsledné plátno. Zrcadlení se často používá k **augmentaci dat**, tedy k uměle rozšířenému trénovacímu setu pro neuronové sítě.

## Adaptivní prahování

Klasické prahování používá jeden práh pro celý obrázek, což selže při nerovnoměrném osvětlení (například stín na fotografii dokumentu). **Adaptivní prahování** počítá práh zvlášť pro každý pixel z jeho okolí.

```python
adaptivni = cv2.adaptiveThreshold(
    sedy,
    255,
    cv2.ADAPTIVE_THRESH_GAUSSIAN_C,   # práh = vážený průměr okolí − C
    cv2.THRESH_BINARY,
    blockSize=11,                     # velikost okolí (liché číslo)
    C=2,                              # konstanta odečtená od průměru
)
```

Parametry `blockSize` a `C` je třeba vyladit podle obrázku. Adaptivní prahování se používá například při skenování textu nebo hledání čar na nerovnoměrně osvětleném pozadí.

TODO: obrázek – Prompt: "Three-panel figure of a photographed page of text with an uneven shadow on one side: original, global threshold (half the page black), adaptive threshold (clean text everywhere); Czech captions; flat clean style."

## Gaussovský a mediánový filtr

S oběma filtry jsme se setkali v základní části. Zde se podíváme na to, kdy který použít. **Gaussovský filtr** nahrazuje pixel váženým průměrem okolí a je vhodný pro obecné vyhlazení a potlačení jemného šumu. Rozostří ale i hrany. **Mediánový filtr** nahradí pixel mediánem okolí, a protože medián není ovlivněn extrémy, dokonale odstraní impulsní šum (náhodné bílé a černé tečky), přičemž hrany zachovává lépe.

Ještě šetrnější k hranám je **bilaterální filtr**, který vyhlazuje oblasti s podobnou barvou, ale přes výrazné hrany nepřetahuje.

```python
gauss = cv2.GaussianBlur(img, (7, 7), 1.5)
median = cv2.medianBlur(img, 5)
bilateral = cv2.bilateralFilter(img, d=9, sigmaColor=75, sigmaSpace=75)

# vlastní jádro (dolní propust) pomocí obecné konvoluce
jadro = np.ones((5, 5), np.float32) / 25
vlastni = cv2.filter2D(img, -1, jadro)

# zaostření (ostřicí jádro)
ostreni = np.array([[0, -1, 0],
                    [-1, 5, -1],
                    [0, -1, 0]], np.float32)
zaostreny = cv2.filter2D(img, -1, ostreni)
```

Obecná funkce `filter2D` provede **konvoluci** libovolného jádra s obrazem. Jádra tak lze navrhovat podle potřeby, od rozostření přes zaostření až po detekci hran.

## Detekce kontur (findContours)

**Kontura** je křivka spojující body na hranici objektu o stejné hodnotě. Funkce **`cv2.findContours`** hledá kontury v binárním obrázku. Je to základ pro měření a analýzu tvaru objektů.

```python
_, binarni = cv2.threshold(sedy, 0, 255, cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU)

kontury, hierarchie = cv2.findContours(
    binarni, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE
)
print("Nalezeno objektů:", len(kontury))

for k in kontury:
    plocha = cv2.contourArea(k)
    obvod = cv2.arcLength(k, True)
    x, y, w, h = cv2.boundingRect(k)       # ohraničující obdélník
    print(plocha, obvod, (x, y, w, h))

cv2.drawContours(img, kontury, -1, (0, 255, 0), 2)    # vykreslení všech kontur zeleně
```

Parametr `RETR_EXTERNAL` vrací jen vnější obrysy, `RETR_TREE` kompletní hierarchii včetně děr. Typické použití je **počítání objektů** (mince, buňky, součástky), měření jejich velikosti a filtrování podle plochy, aby se zahodily drobné skvrny.

## Detekce geometrických tvarů

Z kontury lze odvodit, o jaký tvar jde. Funkce **`cv2.approxPolyDP`** zjednoduší křivku na polygon s menším počtem vrcholů. Podle počtu vrcholů pak tvar rozpoznáme.

```python
for k in kontury:
    if cv2.contourArea(k) < 500:
        continue                                   # ignorovat drobnosti

    epsilon = 0.02 * cv2.arcLength(k, True)
    polygon = cv2.approxPolyDP(k, epsilon, True)
    vrcholu = len(polygon)

    if vrcholu == 3:
        tvar = "trojúhelník"
    elif vrcholu == 4:
        tvar = "čtyřúhelník"
    elif vrcholu > 6:
        tvar = "kruh"
    else:
        tvar = f"{vrcholu}-úhelník"

    x, y, w, h = cv2.boundingRect(k)
    cv2.putText(img, tvar, (x, y - 5), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 255), 1)
```

Pro kruhy existuje i specializovaná Houghova transformace `cv2.HoughCircles` a pro úsečky `cv2.HoughLinesP`. Tento postup je jednoduchý a spoléhá na čistě nasnímané obrazy. Pro složitější scény se dnes používají neuronové sítě.

TODO: obrázek – Prompt: "A white background with several simple coloured shapes (triangle, square, pentagon, circle) each outlined with a green contour and labelled with a small Czech text 'trojúhelník', 'čtyřúhelník', 'pětiúhelník', 'kruh' next to it; flat clean style."

## Maskování obrázků

**Maska** je černobílý obrázek stejné velikosti, který určuje, na které pixely se má operace použít. Bílé pixely masky (255) se zpracují a černé (0) se vynechají. Jak maska vzniká, jsme viděli u HSV (`cv2.inRange`). Bitové operace pak umožňují výsledek použít:

```python
# kruhová maska ve středu obrázku
maska = np.zeros(img.shape[:2], dtype=np.uint8)
vyska, sirka = maska.shape
cv2.circle(maska, (sirka // 2, vyska // 2), min(vyska, sirka) // 3, 255, thickness=-1)

jen_vyrez = cv2.bitwise_and(img, img, mask=maska)       # mimo kruh zčerná

# rozmazání pozadí, ostrý střed
rozmazany = cv2.GaussianBlur(img, (31, 31), 0)
vysledek = np.where(maska[:, :, None] == 255, img, rozmazany)
```

Masky se kombinují operacemi `bitwise_and`, `bitwise_or` a `bitwise_not`. Využívají se při výběru objektu podle barvy, při odstraňování pozadí nebo při omezení detekce na zajímavou oblast.

## Kreslení geometrických objektů

OpenCV umí do obrázku kreslit čáry, obdélníky, kruhy, elipsy, polygony i text. Funkce **modifikují zadaný obrázek přímo**, proto se na kopii pracuje s `img.copy()`. Barvy se zadávají v pořadí BGR a `thickness=-1` znamená vyplněný útvar.

```python
platno = np.full((300, 400, 3), 255, dtype=np.uint8)     # bílé plátno

cv2.line(platno, (20, 20), (380, 20), (0, 0, 0), 2)
cv2.rectangle(platno, (50, 60), (200, 160), (255, 0, 0), 3)         # modrý rámeček
cv2.circle(platno, (300, 110), 40, (0, 0, 255), -1)                 # červený vyplněný kruh
cv2.ellipse(platno, (120, 230), (60, 30), 0, 0, 360, (0, 150, 0), 2)
cv2.putText(platno, "OpenCV", (220, 250), cv2.FONT_HERSHEY_SIMPLEX, 1.2, (0, 0, 0), 2)

cv2.imwrite("kresba.png", platno)
```

Funkce `putText` neumí české znaky s diakritikou (výchozí písma jsou jen ASCII). Pro ně je potřeba použít knihovnu Pillow nebo vykreslit text jinak. Kreslení se běžně používá k označování výsledků detekce: ohraničující obdélníky, popisky a body.

## Zpracování videa (VideoCapture, VideoWriter)

S třídou `VideoCapture` pro čtení videa jsme se už seznámili. Pro **zápis** videa slouží `cv2.VideoWriter`, kterému zadáme název souboru, kodek, počet snímků za sekundu a rozměry.

```python
vstup = cv2.VideoCapture("video.mp4")

fps = vstup.get(cv2.CAP_PROP_FPS)
sirka = int(vstup.get(cv2.CAP_PROP_FRAME_WIDTH))
vyska = int(vstup.get(cv2.CAP_PROP_FRAME_HEIGHT))
pocet = int(vstup.get(cv2.CAP_PROP_FRAME_COUNT))
print(fps, sirka, vyska, pocet)

kodek = cv2.VideoWriter_fourcc(*"mp4v")
vystup = cv2.VideoWriter("vystup.mp4", kodek, fps, (sirka, vyska))

while True:
    ok, snimek = vstup.read()
    if not ok:
        break
    snimek = cv2.GaussianBlur(snimek, (15, 15), 0)     # libovolné zpracování
    vystup.write(snimek)

vstup.release()
vystup.release()
```

Rozměr předaný `VideoWriter` musí přesně odpovídat rozměru zapisovaných snímků, jinak se video nezapíše. Pomocí `vstup.set(cv2.CAP_PROP_POS_FRAMES, n)` lze přeskočit na zadaný snímek.

## Detekce pohybu

Nejjednodušší detekce pohybu porovnává po sobě jdoucí snímky. Pokud se pixel mezi nimi výrazně změnil, v daném místě se něco pohnulo. Funkce `cv2.absdiff` spočítá absolutní rozdíl a prahováním z ní vznikne maska pohybu.

```python
kamera = cv2.VideoCapture(0)
predchozi = None

while True:
    ok, snimek = kamera.read()
    if not ok:
        break

    sedy = cv2.GaussianBlur(cv2.cvtColor(snimek, cv2.COLOR_BGR2GRAY), (21, 21), 0)

    if predchozi is None:
        predchozi = sedy
        continue

    rozdil = cv2.absdiff(predchozi, sedy)
    _, pohyb = cv2.threshold(rozdil, 25, 255, cv2.THRESH_BINARY)
    pohyb = cv2.dilate(pohyb, None, iterations=2)          # zacelení děr

    kontury, _ = cv2.findContours(pohyb, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    for k in kontury:
        if cv2.contourArea(k) > 800:
            x, y, w, h = cv2.boundingRect(k)
            cv2.rectangle(snimek, (x, y), (x + w, y + h), (0, 255, 0), 2)

    cv2.imshow("Pohyb", snimek)
    predchozi = sedy
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

kamera.release()
cv2.destroyAllWindows()
```

Program kombinuje většinu dříve probraných kroků: šedotón, rozostření, prahování, dilataci a kontury. Pro pevnou kameru se často používá robustnější odečítání pozadí, například `cv2.createBackgroundSubtractorMOG2()`, které si pozadí „pamatuje“ a toleruje postupné změny osvětlení.

## Detekce objektů

OpenCV obsahuje i hotové metody pro nalezení objektů. Nejjednodušší je **šablonová shoda** (`cv2.matchTemplate`), která hledá ve velkém obrázku výskyt malého vzoru:

```python
sablona = cv2.imread("logo.png", cv2.IMREAD_GRAYSCALE)
vysledek = cv2.matchTemplate(sedy, sablona, cv2.TM_CCOEFF_NORMED)

_, nejlepsi, _, poloha = cv2.minMaxLoc(vysledek)
if nejlepsi > 0.8:
    h, w = sablona.shape
    cv2.rectangle(img, poloha, (poloha[0] + w, poloha[1] + h), (0, 255, 0), 2)
```

Metoda funguje jen pro objekty stejné velikosti a orientace. Univerzálnější jsou detektory příznaků (ORB, SIFT) nebo modely založené na neuronových sítích. OpenCV obsahuje modul **`cv2.dnn`**, který umí načíst a spustit předtrénované sítě (například YOLO nebo SSD) a použít je k detekci a klasifikaci objektů.

## Haar Cascade Classifier

**Haar Cascade** je klasická metoda detekce objektů, hlavně obličejů. Je založena na kaskádě jednoduchých klasifikátorů, které hledají typické světlé a tmavé oblasti (například tmavé oči nad světlejšími tvářemi). Je velmi rychlá, ale méně přesná než moderní neuronové sítě. OpenCV obsahuje předtrénované modely, uložené jako soubory XML.

```python
cesta = cv2.data.haarcascades + "haarcascade_frontalface_default.xml"
detektor = cv2.CascadeClassifier(cesta)

tvare = detektor.detectMultiScale(
    sedy,
    scaleFactor=1.1,      # o kolik se mění měřítko okna při každém kroku
    minNeighbors=5,       # kolik sousedních detekcí je potřeba pro potvrzení
    minSize=(30, 30),     # minimální velikost obličeje
)

for (x, y, w, h) in tvare:
    cv2.rectangle(img, (x, y), (x + w, y + h), (255, 0, 0), 2)

print("Nalezeno obličejů:", len(tvare))
```

Parametry `scaleFactor` a `minNeighbors` ovlivňují citlivost: nižší hodnoty najdou víc obličejů, ale i více falešných nálezů. Modely nejlépe fungují na čelních obličejích a dobrém osvětlení.

TODO: obrázek – Prompt: "A group photo-style illustration of three cartoon faces with blue rectangles drawn around each detected face and a caption in Czech 'Haar cascade: nalezeno 3 obličejů'; flat clean style, no real people."

{% hint style="warning" %}
Detekce a rozpoznávání obličejů se týká osobních údajů. Před nasazením v reálné aplikaci je třeba zvážit právní a etická omezení, zejména ochranu soukromí a souhlas fotografovaných osob.
{% endhint %}

## Shrnutí

Rozšiřující část OpenCV rozvíjí základní operace v praktické úlohy. Histogram a jeho vyrovnání zlepšují kontrast, barevné kanály se dají upravovat samostatně, adaptivní prahování zvládá nerovnoměrné osvětlení a různé filtry vyhlazují nebo zaostřují obraz. Kontury umožňují počítat a měřit objekty, a po zjednodušení na polygon i rozpoznat geometrické tvary. Masky vybírají oblasti zájmu, kreslicí funkce označují výsledky a `VideoWriter` ukládá zpracované video. Porovnáním po sobě jdoucích snímků lze detekovat pohyb a pro nalezení objektů slouží šablonová shoda, Haar Cascade nebo předtrénované neuronové sítě.
