# Rajvonja-naloga-2025
Izvajanje nadkontrole pri robotski molži in analiza vplivov na vsebnosti v mleku


# Korekcijski faktorji za vsebnost mlečne maščobe pri robotski molži (AMS)

Repozitorij vsebuje analitični cevovod (R skripto) za izračun aditivnih korekcijskih faktorjev za oceno dnevne vsebnosti mlečne maščobe na podlagi enega samega vzorca pri kravah molznicah v sistemih avtomatizirane molže (AMS). 

Skripta je nastala v okviru razvojne naloge *Kmetijskega inštituta Slovenije (KIS)* in *Kmetijsko gozdarske zbornice Slovenije (KGZS)* za izboljšanje natančnosti nadkontrole prireje mleka v slovenskih rejah z molznimi roboti.

## 📖 Opis projekta in biološko ozadje

Z uvajanjem robotske molže krave same izbirajo ritem obiskov, kar vodi do visoke variabilnosti v intervalih molže in količini pomolzenega mleka. To neposredno vpliva na vsebnost mlečne maščobe v posameznem vzorcu. Skripta rešuje ta problem z uporabo **linearnih mešanih modelov (LMM)**, pri čemer upošteva ključne biološke mehanizme:
* **Učinek redčenja (Dilution effect):** Vpliv količine trenutno pomolzenega mleka na koncentracijo maščobnih kapljic.
* **"Carry-over" učinek (Spomin vimena):** Vpliv predhodnega intervala in predhodne količine mleka na preostalo mleko (residual milk) v vimenu in s tem na vsebnost maščobe v tekočem vzorcu.
* **Vpliv faze laktacije:** Upoštevanje linearnega in kvadratnega vpliva dni v laktaciji (DIM).

S pomočjo modela se "umetna" variabilnost, ki nastane zaradi različnih intervalov, odstrani, vrednosti pa se standardizirajo na fiktiven 12-urni referenčni interval.

## 🛠 Kaj skripta naredi?

Skripta je razdeljena v več logičnih sklopov:
1. **Uvoz in čiščenje podatkov:** Ekstrakcija podatkov, odstranjevanje biološko nerealnih meritev in omejevanje na intervale med 180 in 1200 minutami.
2. **Eksploratorna analiza:** Izračun korelacijskih matrik in vizualizacija odnosov (GAM in linearni trendi) med parametri molže in maščobo.
3. **Statistično modeliranje (LMM):** Gradnja regresijskega modela s knjižnico `lme4`, ki izolira genetski vpliv krave ter vpliv črede (naključni učinki) od sistemskih vplivov molže (fiksni učinki). 
4. **Diagnostika modela in Cookova razdalja:** Preverjanje normalnosti ostankov (Q-Q graf, histogram) in homoskedastičnosti ter odstranjevanje najbolj vplivnih odklonov (1 % podatkov) za povečanje robustnosti.
5. **Generiranje korekcijskih faktorjev:** Skaliranje spremenljivk in izračun predikcij za različne scenarije molže. Izris končne "Heatmap" tabele z aditivnimi popravki.
6. **Validacija:** Aplikacija faktorjev na podatke in dokazovanje padca standardnega odklona (zmanjšanje variabilnosti) in znižanja korelacije med časom molže in vsebnostjo maščobe.

## 📦 Zahteve in odvisnosti

Za poganjanje skripte potrebuješ R okolje (priporočljiv RStudio) in naslednje knjižnice:
* `tidyverse` (za manipulacijo podatkov in izrise - `dplyr`, `ggplot2`, `tidyr`)
* `readxl` (za uvoz .xlsx datotek)
* `lubridate` (za obdelavo datumov)
* `lme4` (za linearne mešane modele)
* `performance` (za evaluacijo modelov, R2, RMSE)

Za namestitev manjkajočih knjižnic zaženi:
```R
install.packages(c("tidyverse", "readxl", "lubridate", "lme4", "performance"))
```

## 🚀 Navodila za uporabo

1. Kloniraj repozitorij ali prenesi datoteko `.Rmd` na svoj računalnik.
2. V skripti ustrezno **prilagodi pot do vhodne datoteke** na tvojem računalniku (korak 2: Uvoz podatkov):
   ```R
   file_path <- "pot/do/tvoje/datoteke/nadkontrola.xlsx"
   ```
3. Zagotovi, da ima tvoja vhodna baza podatkov (shema testnih dni) ustrezno poimenovane stolpce (vsaj: `F_PERCENT_SAMPLE`, `IM_0`, `MY_0`, `IM_1`, `MY_1`, `DIM`, `ZAP_TELITEV`, `KM_ZIV_ID_SEQ`, `SIFRA_LOKACIJE`).
4. Skripto poženite po kosih (chunks) ali v celoti (`Knit`).

## 📊 Rezultati in izhodi

Pričakovani glavni izhodi po uspešnem zagonu skripte vključujejo:
* Diagnostične grafe modela (Residuals vs Fitted, Q-Q plots, histogram napak).
* Oceno modela (Marginalni in kondicionalni $R^2$, povprečna napaka - RMSE).
* **Heatmap matrika korekcijskih faktorjev**, ki glede na količino pomolzenega mleka in časovni interval svetuje, koliko odstotne točke maščobe je potrebno dodati/odvzeti za standardizacijo ocene na 12-urni interval.

## ✍️ Avtorji
* **Urban Vodopivc** ter sodelavci (Žan Pečnik, Tomaž Perpar, Janez Jeretina in ostali)
* **Kmetijski inštitut Slovenije (KIS)** in **Kmetijsko gozdarska zbornica Slovenije (KGZS)**, 2026.
