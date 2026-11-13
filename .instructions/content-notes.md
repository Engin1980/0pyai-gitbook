Pro tříhodinovou kapitolu bych volil přibližně **8–12 základních pojmů** a zbytek zařadil do rozšiřující části. Základní část by měla studentům dát představu, k čemu knihovna slouží, jak se používá a co s ní dokážou udělat. Rozšiřující část může sloužit jako přehled možností, inspirace pro další studium nebo samostatnou práci.

Níže jsem původní seznamy proškrtal a rozdělil.

## **7. NumPy**

**Základní**

- Účel a využití knihovny NumPy
- NumPy array (`ndarray`)
- Dimenze a tvar pole (`ndim`, `shape`)
- Datové typy (`dtype`)
- Vytváření polí (`array`, `zeros`, `ones`, `arange`, `linspace`)
- Indexování a slicing
- Základní operace s poli
- Aritmetické a relační operace
- Agregační funkce (`sum`, `mean`, `min`, `max`)
- Změna tvaru pole (`reshape`)
- Broadcasting
- Generování náhodných čísel
- Transpozice polí
- Spojování a rozdělování polí

**Rozšiřující**

- Kopie a pohledy na pole (Copy vs. View)
- Boolean Masking
- Podmíněný výběr (`where`)
- Univerzální funkce (`ufuncs`)
- Matice a lineární algebra (`numpy.linalg`)
- Standardní odchylka a rozptyl
- Práce s `NaN` a `Inf`

## **8. Pandas**

**Základní**

- Účel a využití knihovny Pandas
- Datové struktury `Series` a `DataFrame`
- Vytváření DataFrame
- Načítání dat ze souborů (`read_csv`, `read_excel`)
- Základní prohlížení dat (`head`, `tail`, `info`, `describe`)
- Výběr sloupců a řádků (`loc`, `iloc`)
- Filtrování dat
- Řazení dat (`sort_values`)
- Přidávání a odebírání sloupců
- Práce s chybějícími hodnotami (`NaN`, `dropna`, `fillna`)
- Základní statistické funkce
- Export dat (`to_csv`, `to_excel`)

**Rozšiřující**

- Odstraňování duplicit
- Změna datových typů (`astype`)
- Aplikace funkcí (`apply`, `map`)
- Seskupování dat (`groupby`)
- Agregace dat (`agg`)
- Spojování DataFrame (`merge`, `concat`)
- Kontingenční tabulky (`pivot_table`)
- Práce s textovými daty (`str`)
- Práce s datumem a časem (`datetime`)
- Klouzavá okna (`rolling`)

## **9. Matplotlib**

**Základní**

- Účel a využití knihovny Matplotlib
- Modul `pyplot`
- Základní struktura grafu (`Figure`, `Axes`)
- Vytvoření grafu (`plot`)
- Spojnicový graf
- Bodový graf (`scatter`)
- Sloupcový graf (`bar`)
- Histogram (`hist`)
- Název grafu a popisky os
- Legenda (`legend`)
- Barvy a styly grafů
- Více grafů v jednom obrázku (`subplots`)
- Ukládání grafů (`savefig`)

**Rozšiřující**

- Výsečový graf (`pie`)
- Krabicový graf (`boxplot`)
- Formátování os a jejich rozsah
- Mřížka (`grid`)
- Značky datových bodů (Markers)
- Anotace a text v grafu
- Chybové úsečky (`errorbar`)
- Více datových řad v jednom grafu
- 3D grafy
- Interaktivní grafy

## **10. SciPy**

**Základní**

- Účel a využití knihovny SciPy
- Vazba na NumPy
- Struktura knihovny a její moduly
- Numerická integrace (`scipy.integrate`)
- Optimalizace (`scipy.optimize`)
- Hledání kořenů rovnic
- Minimalizace funkcí
- Statistika (`scipy.stats`)
- Pravděpodobnostní rozdělení
- Statistické testování
- Interpolace dat (`scipy.interpolate`)
- Lineární algebra (`scipy.linalg`)
- Řešení soustav lineárních rovnic


**Rozšiřující**


- Řešení diferenciálních rovnic (`solve_ivp`)
- Vlastní čísla a vlastní vektory
- Regresní analýza
- Prokládání křivek (`curve_fit`)
- Zpracování signálů (`scipy.signal`)
- Fourierova transformace (`scipy.fft`)
- Filtrace signálů
- Prostorové algoritmy (`scipy.spatial`)
- Řídké matice (`scipy.sparse`)
- Zpracování obrazových dat (`scipy.ndimage`)

## **11. OpenCV**

**Základní**

- Účel a využití OpenCV
- Počítačové vidění (Computer Vision)
- Import knihovny (`cv2`)
- Reprezentace obrázku jako NumPy array
- Načítání a ukládání obrázků (`imread`, `imwrite`)
- Zobrazování obrázků (`imshow`)
- Barevné modely (RGB, BGR, HSV, Grayscale)
- Převod barevných prostorů (`cvtColor`)
- Změna velikosti a ořezávání obrázků (`resize`)
- Geometrické transformace
- Prahování obrázků (`threshold`)
- Rozostření a vyhlazování
- Detekce hran (Canny)
- Morfologické operace
- Eroze a dilatace
- Práce s kamerou

**Rozšiřující**

- Histogram obrázku
- Barevné kanály
- Rotace a zrcadlení obrázků
- Adaptivní prahování
- Gaussovský a mediánový filtr
- Detekce kontur (`findContours`)
- Detekce geometrických tvarů
- Maskování obrázků
- Kreslení geometrických objektů
- Zpracování videa (`VideoCapture`, `VideoWriter`)
- Detekce pohybu
- Detekce objektů
- Haar Cascade Classifier

## **12. PyTorch Lightning**

**Základní**

- Účel a využití PyTorch Lightning
- Rozdíl mezi PyTorch a PyTorch Lightning
- Základy Deep Learningu
- Tensor a jeho vlastnosti
- Základní operace s tensory
- GPU a akcelerace výpočtů
- Neuronová síť (`nn.Module`)
- Vrstvy neuronové sítě
- Aktivační funkce
- Ztrátová funkce (Loss Function)
- Optimalizátor (Optimizer)
- Dataset a DataLoader
- Trénovací, validační a testovací množina
- Epoch a Batch Size
- LightningModule
- Základní trénovací cyklus
- Lightning Trainer

**Rozšiřující**

- Automatická diferenciace (Autograd)
- Výpočet gradientů
- `training_step`
- `validation_step`
- `test_step`
- `configure_optimizers`
- Learning Rate
- Overfitting a Underfitting
- Regularizace
- Callback
- ModelCheckpoint
- EarlyStopping
- Logging metrik
- TensorBoard
- Ukládání a načítání modelů
- Transfer Learning
- Fine-tuning
- Reprodukovatelnost experimentů
- Mixed Precision
- Distributed Training

## **Doporučené rozložení tříhodinové výuky**

Pro všechny kapitoly bych držel podobný časový rámec:

| Část                     | Čas    | Obsah                                  |
| ------------------------ | ------ | -------------------------------------- |
| Motivace a představení   | 15 min | K čemu knihovna slouží, kde se používá |
| Základní pojmy           | 45 min | Vysvětlení hlavních konceptů           |
| Praktické ukázky         | 75 min | Práce v Pythonu, krátké příklady       |
| Samostatné cvičení       | 30 min | Jednoduchá úloha pro studenty          |
| Shrnutí a další možnosti | 15 min | Co knihovna nabízí a kam pokračovat    |

**Ještě jedno pedagogické doporučení:** U SciPy a PyTorch Lightning bych byl s rozsahem základní části obzvlášť opatrný. SciPy je rozsáhlý ekosystém a PyTorch Lightning stojí na řadě konceptů z neuronových sítí. V těchto kapitolách je podle mě důležitější, aby studenti pochopili *kdy a proč danou technologii použít*, než aby si zapamatovali velké množství funkcí a metod.