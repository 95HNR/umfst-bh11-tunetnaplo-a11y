# Tünetnapló, ami csak egérrel működik – hibariport

Repó: https://github.com/Hunor/umfst-bh11-tunetnaplo-a11y
Készítette: Sandor Hunor, INFO 3.EV

## 1. hiba

* **Hiba leírása és reprodukálása:** Telefonos nézetben (vagy böngészős emulátorban) kétujjas mozdulattal nem lehet belenagyítani az oldalba. A remegő kezű vagy gyengénlátó felhasználók nem tudják felnagyítani a tartalmat.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** A meta viewport tag tartalmazza a nagyítást tiltó paramétereket. Megsértett kritérium: WCAG 1.4.4 Szöveg átméretezése.
* **Javítás:** A letiltó attribútumok törlése.
* Előtte:  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no"> 
* Utána:  <meta name="viewport" content="width=device-width, initial-scale=1"> 


* **Hogyan tesztelted:** axe DevTools (hibát dob a meta tagre), Lighthouse, mobilos emulátor (pinch-to-zoom).
* **Tanulság / érintett alapelv:** Soha ne vedd el a felhasználótól a nagyítás és a böngésző alapértelmezett funkcióinak irányítását.

## 2. hiba

* **Hiba leírása és reprodukálása:** A "Fejfájás", "Hányinger", "Szédülés" opciókra Tab billentyűvel nem lehet rálépni. NVDA képernyőolvasóval navigálva csak egyszerű szövegként olvassa fel őket, a kiválasztott állapot nem derül ki.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** Interaktív űrlapelemek (checkbox) helyett egyszerű  <div>  tagek lettek használva. Megsértett kritériumok: WCAG 2.1.1 Billentyűzet, WCAG 4.1.2 Név, szerep, érték.
* **Javítás:** A div-ek cseréje vizuálisan rejtett natív  <input type="checkbox">  elemekre és az azokat körbeölelő  <label> -ekre, egy  <fieldset> -en belül.
* Előtte:  <div class="chk" data-v="fejfajas">Fejfájás</div> 
* Utána:  <label class="chk-label"><input type="checkbox" name="tunet" value="fejfajas"><span class="chk-text">Fejfájás</span></label> 


* **Hogyan tesztelted:** Csak billentyűzet (Tab és Szóköz), NVDA képernyőolvasó.
* **Tanulság / érintett alapelv:** Az ARIA első szabálya: ha van natív HTML elem (pl. checkbox), mindig azt használd egyedi megoldások (div + JS) helyett.

## 3. hiba

* **Hiba leírása és reprodukálása:** A képernyőolvasó a mezőkre ugorva (Erősség, Megjegyzés) nem mondja be, hogy mit kell oda írni, csak azt, hogy "szerkesztés". A címsor-navigáció h1-ről egyből h3-ra ugrik.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** A mezők címeit  <h3>  taggel oldották meg összekapcsolt  <label>  helyett. Megsértett kritériumok: WCAG 1.3.1 Információ és kapcsolatok, WCAG 3.3.2 Címkék vagy utasítások.
* **Javítás:** A címsorok lecserélése  <label>  elemekre, a  for  attribútummal összekötve a megfelelő  id -jű mezővel.
* Előtte:  <h3>Erősség (0–10)</h3> <input id="eros" ...> 
* Utána:  <label for="eros">Erősség (0–10)</label> <input id="eros" ...> 


* **Hogyan tesztelted:** NVDA/VoiceOver (a mezőre ugorva már felolvassa a címkét), axe DevTools.
* **Tanulság / érintett alapelv:** A vizuális közelség nem elég: a kapcsolatokat (címke és mező között) programozottan is biztosítani kell.

## 4. hiba

* **Hiba leírása és reprodukálása:** A Tab billentyűt nyomogatva a fókusz először a legalsó (Megjegyzés) mezőre ugrik, onnan visszafelé az Erősségre, ami ellentmond a vizuális sorrendnek.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** A mezőkön pozitív  tabindex  érték szerepel ( tabindex="1"  és  "2" ). Megsértett kritérium: WCAG 2.4.3 Fókuszsorrend.
* **Javítás:** A  tabindex  attribútumok teljes eltávolítása, hogy a böngésző a DOM természetes sorrendjét követhesse.
* Előtte:  <textarea id="megj" tabindex="1"></textarea> 
* Utána:  <textarea id="megj"></textarea> 


* **Hogyan tesztelted:** Csak billentyűzet (Tabulátorral történő végignavigálás az űrlapon).
* **Tanulság / érintett alapelv:** Soha ne használj nullánál nagyobb tabindex értéket, mert szétveri a logikus navigációt.

## 5. hiba

* **Hiba leírása és reprodukálása:** A Mentés gombot nem lehet Tabbal elérni, és Enterrel sem lehet aktiválni.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** A gomb egy egyszerű  <div> , űrlap ( <form> ) nélkül. Megsértett kritériumok: WCAG 2.1.1 Billentyűzet, WCAG 4.1.2 Név, szerep, érték.
* **Javítás:** Az elemek becsomagolása egy  <form> -ba, és a div lecserélése  <button type="submit">  elemre.
* Előtte:  <div class="btn" id="ment">Mentés</div>  (JS:  click  esemény)
* Utána:  <button type="submit" class="btn">Mentés</button>  (JS:  <form>   submit  esemény figyelve)


* **Hogyan tesztelted:** Csak billentyűzet (Tabulátorral rálépve, majd Enter / Szóköz lenyomásával).
* **Tanulság / érintett alapelv:** Az ARIA első szabálya: natív elem ( <button> ) használata div helyett az űrlapok beküldésére.

## 6. hiba

* **Hiba leírása és reprodukálása:** Hibás érték megadásakor csak a mező kerete lesz piros. Sem a képernyőolvasó nem jelzi a hibát (nem ugrik oda a fókusz), sem a színtévesztő nem látja biztosan a problémát szöveg hiányában.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** A hiba jelzése kizárólag színnel történt, hibaüzenet és programozott állapot (ARIA) nélkül. Megsértett kritériumok: WCAG 1.4.1 Szín használata, WCAG 3.3.1 Hiba azonosítása.
* **Javítás:** Szöveges hibaüzenet hozzáadása, fókuszálása, illetve  aria-invalid="true"  és  aria-describedby  beállítása a mezőn.
* Előtte:  eros.classList.add('hibas'); return; 
* Utána:  eros.setAttribute('aria-invalid', 'true'); eros.setAttribute('aria-describedby', 'eros-hiba'); hibaSzoveg.textContent = '❌ Hiba...'; eros.focus(); 


* **Hogyan tesztelted:** NVDA/VoiceOver, csak billentyűzet, vizuális ellenőrzés (a szöveg egyértelmű szín nélkül is).
* **Tanulság / érintett alapelv:** Ne csak színnel közölj információt, és programozottan is értesítsd a felhasználót a hibáról.

## 7. hiba

* **Hiba leírása és reprodukálása:** Sikeres mentéskor a "Mentve!" szöveg megjelenik, de a képernyőolvasó ezt nem mondja be. A szöveg élénkzöld (#2ecc71) színe fehér háttéren alig olvasható.
* **Gyökérok + megsértett WCAG 2.2 sikerkritérium:** Dinamikusan beszúrt állapotüzenet ARIA live region nélkül, plusz túl alacsony (kb. 2:1) kontrasztarány. Megsértett kritériumok: WCAG 4.1.3 Állapotüzenetek, WCAG 1.4.3 Kontraszt (minimum).
* **Javítás:** A  <p>  tag megkapta a  role="status"  attribútumot, a színét pedig sötétzöldre (#1f6b3a) módosítottuk.
* Előtte:  <p id="ok"></p>  (CSS-ben color: #2ecc71)
* Utána:  <p id="ok" role="status"></p>  (CSS-ben color: #1f6b3a)


* **Hogyan tesztelted:** Kontrasztmérő (Lighthouse, axe DevTools, WebAIM Contrast Checker - eredmény >= 4.5:1), NVDA képernyőolvasó (automatikusan felolvasta mentéskor).
* **Tanulság / érintett alapelv:** A vizuálisan megjelenő állapotváltozásokat az asszisztív technológiák számára is közvetíteni kell élő régiók segítségével, megfelelő kontraszt mellett.

## Összegzés

* **Mit tanultam:** A "szemre működik" nem egyenlő a használhatósággal. A webfejlesztés során a HTML natív szemantikáját ( <button> ,  <input> ,  <label> ) kell előnyben részesíteni, mert ezek "ingyen" biztosítják a billentyűzet-támogatást és az akadálymentességet.
* **Melyik alapelv sérült a leggyakrabban:** A **Működtethetőség (Operable)** alapelve sérült leginkább (billentyűzettel használhatatlan elemek, fókuszsorrend), valamint az **Érthetőség (Understandable)** és **Robusztusság (Robust)** (hiányzó címkék, nem szabványos űrlapelemek és ARIA hiányosságok).
