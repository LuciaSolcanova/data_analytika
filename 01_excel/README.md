# Lekce 1.1 – Převod reportu na čistá data

**Zadání:** Report nákladů středisek za Q1 2026 (export z účetnictví) převést do podoby
vhodné pro analýzu a ověřit správnost celkového součtu.

**Soubor:** `lekce_1_1_naklady.xlsx`
- list `Report_z_ucetnictvi` – původní report
- list `Data` – čistá tabulka `tbl_Naklady` (27 záznamů)

**Nástroje:** Excel – Excel Table, strukturované odkazy, SUM, SUBTOTAL, filtry

## 1. Nalezené problémy v reportu

| # | Problém | Kde | Dopad na analýzu | Řešení |
| --- | --- | --- | --- | --- |
| 1 | Číslo uložené jako text („34 000 Kč“) | C12 (HR, účet 518, leden) | SUM hodnotu tiše přeskočí, mezisoučet HR je o 34 000 Kč nižší | Převést na číslo 34000, „Kč“ řešit formátem buňky |
| 2 | Celkový součet zapsaný ručně, ne vzorcem | F21 | Hodnota se neaktualizuje při změně dat (např. po dodatečné úpravě) | Součet vždy počítat vzorcem a ověřit kontrolním součtem |
| 3 | Sloučené buňky | Středisko A6:A8, A11:A13, A16:A18; hlavičky A4:F5 | Hodnotu má jen první buňka, filtr a vzorce vidí zbylé řádky jako prázdné | Vyplnit středisko v každém řádku, hlavičku v jednom řádku |
| 4 | Skrytá mezera před názvem („ Obchod“) | A16 | SUMIFS ani XLOOKUP hodnotu nenajdou („ Obchod“ ≠ „Obchod“) | Odstranit funkcí TRIM |
| 5 | Měsíce ve sloupcích vedle sebe (široký formát) | C5:E5 | Nelze filtrovat ani seskupovat podle období, nelze načíst do kontingenční tabulky ani Power BI | Převést do dlouhého formátu se sloupcem `Datum` |
| 6 | Číslo a název účtu v jedné buňce | sloupec B | Nelze filtrovat ani propojovat podle čísla účtu | Rozdělit na `Ucet` a `NazevUctu` |
| 7 | Mezisoučty a prázdné řádky uvnitř dat | řádky 9–10, 14–15, 19–20 | Při agregaci by se náklady započítaly dvakrát | Data bez mezisoučtů, součty počítat až v reportu |
| 8 | Nadpis a poznámky nad tabulkou | řádky 1–2 | Hlavička není v prvním řádku, nástroje data nenačtou správně | Popis oddělit od dat |
| 9 | Vzorce s pevnými oblastmi | F6:F19 | Nový řádek se do součtu nezapočítá | Excel Table a strukturované odkazy |

## 2. Postup

1. Report jsem převedla do dlouhého formátu: jeden řádek = středisko × účet × měsíc.
2. Sloupce: `Datum | Stredisko | Ucet | NazevUctu | Castka` (bez diakritiky a mezer kvůli
   kompatibilitě s SQL, Pythonem a Power BI). Datum = poslední den měsíce.
3. Data jsem převedla na tabulku `tbl_Naklady` (Ctrl+T), celkový součet počítám
   strukturovaným odkazem `=SUM(tbl_Naklady[Castka])`.
4. Výsledek jsem odsouhlasila na zdrojový report (viz kontrolní součet).

## 3. Kontrolní součet

| Zdroj | Částka (Kč) |
| --- | --- |
| Součet čistých dat `tbl_Naklady` | 3 813 450 |
| Celkem dle reportu (F21) | 3 809 150 |
| **Rozdíl** | **4 300** |

**Vysvětlení rozdílů:**
1. Mezisoučet HR v reportu je o 34 000 Kč nižší, protože hodnota v C12 je uložena jako text
   a funkce SUM ji nezapočítá. Součet mezisoučtů reportu (3 779 450) + 34 000 = 3 813 450,
   čistá data tedy odpovídají zdrojovým hodnotám.
2. Celkový součet F21 je zapsán ručně a od správné hodnoty se liší o 4 300 Kč. Pravděpodobně
   byl vyčíslen před dodatečnou úpravou března u střediska Obchod. Doporučení: ověřit
   v účtárně a celkový součet v reportu počítat vzorcem.

**Kontrola po střediscích:** po vyfiltrování střediska IT ukazuje řádek souhrnů tabulky
1 572 450 Kč, což odpovídá mezisoučtu IT v reportu (F9).

## 4. Co jsem se naučila

- Report pro čtení a data pro analýzu jsou dvě různé věci.
- Před analýzou vždy ověřím kvalitu dat a odsouhlasím součty na zdroj.
- Nejnebezpečnější chyby jsou ty, které Excel nehlásí (text místo čísla, skryté mezery,
  ručně psané součty).
- Chybu opravuji u zdroje (převod textu na číslo), ne ručním dopočtem ve vzorci.
- Součty nad filtrovanými daty počítám funkcí SUBTOTAL (ignoruje skryté řádky), ne SUM.
- Rozdíl, jehož příčinu nelze z dat jednoznačně určit, ověřím u zdroje, neodhaduji ho.

## 5. Příště

Ruční převod jsem dělala záměrně. V lekci 1.6 stejný převod zautomatizuji v Power Query.