# Grenadier WG – Putzplan

Ein Putzrad als Telegram-Mini-App: Das grüne Fünfeck mit den Namen dreht sich jede Woche ein Feld weiter. Welcher Name neben welcher Aufgabe steht, der ist in dieser Woche dran.

**Live:** https://leubner84.github.io/Putzplan/

Die ganze App ist eine einzige Datei: [`index.html`](index.html). Es gibt keinen Build-Schritt, kein npm und keinen Server.

## Bedienung

| Aktion | Was passiert |
|---|---|
| Rad mit dem Finger drehen | Woche vor oder zurück (ein Feld = eine Woche) |
| Schieberegler | Schnell bis zu 10 Wochen vor oder zurück, z. B. um nachzusehen, wer vor drei Wochen Küche hatte |
| „Zurück zu heute“ | Springt zur aktuellen Woche |
| Aufgabe antippen (runder Icon-Knopf oder Text) | Checkliste der Aufgabe und wer in der gewählten Woche dran ist |
| Huhn antippen | Es lässt Federn und rennt kurz davon |

Unter der Kalenderwoche steht, wie weit die angezeigte Woche weg ist („Diese Woche“, „Vor 3 Wochen“ …).

**Das Zwergseidenhuhn:** Ein gekritzeltes Zwergseidenhuhn läuft ständig durchs Bild und pickt hier und da (Code: `startSilkie()` in `index.html`). Wer am Handy „Bewegung reduzieren“ eingeschaltet hat, sieht es nicht.

## Telegram-Funktionen

- **Sprache:** Ist Telegram auf Deutsch eingestellt, erscheint alles auf Deutsch, bei jeder anderen Sprache auf Englisch. Außerhalb von Telegram gilt die Browsersprache.
- **Eigener Name markiert:** Wer die App öffnet, sieht seinen Namen auf dem Rad pink umkringelt und seine Aufgabe pink markiert.
- **Vibrieren** beim Wochenwechsel. Wischen am Rad schließt die Mini-App nicht.

**Wichtig:** Telegram verrät der App nur dann, wer sie öffnet, wenn sie **als Mini-App über einen Bot** gestartet wird (Menü-Button des Bots oder Link `t.me/<bot>/<app>`). Als normaler Link im Chat geöffnet, funktioniert alles, nur die Markierung des eigenen Namens fehlt.

## Anpassen

Alles Anpassbare steht oben im `<script>`-Teil von `index.html` unter **KONFIGURATION**.

### Mitbewohner – `PEOPLE`

```js
const PEOPLE = ["Kamilla", "Alex", "Lea", "Leuby", "Micha"];
```

- **Jemand zieht aus und jemand anderes ein:** Einfach den Namen an derselben Stelle ersetzen (so wurde z. B. Yumi → Kamilla gemacht). Die Reihenfolge der anderen bleibt gleich.
- **Die Anzahl ändert sich:** Rad und Fünfeck passen sich automatisch an (z. B. Sechseck). Die Einteilung springt dann aber für alle Wochen um, auch rückwirkend. Außerdem braucht es genauso viele Aufgaben wie Personen.

### Telegram-Namen zuordnen – `TELEGRAM_USERS`

Die App nimmt das **erste Wort des Telegram-Vornamens** und sucht es in `PEOPLE` („Lea Bär“ → Lea, „Micha Wg Rybakov“ → Micha). Heißt jemand in Telegram anders als im Plan, trägt man ihn hier ein:

```js
const TELEGRAM_USERS = {
    "christian": "Leuby",
    // "camilla": "Kamilla",
};
```

Schlüssel klein geschrieben: erstes Wort des Telegram-Vornamens, Telegram-Username (ohne @) oder numerische User-ID.

Achtung: In der Mitgliederliste zeigt Telegram die Namen so an, wie *du* die Leute in deinen Kontakten gespeichert hast. Die App bekommt aber den Namen, den die Person *selbst* eingestellt hat. Wenn sich jemand nicht markiert sieht, ist das meist der Grund.

### Aufgaben – `TASKS`

Jede Aufgabe hat ein Icon und Texte auf Englisch (`en`) und Deutsch (`de`):

```js
{
    icon: "🍽️",
    en: { name: "Kitchen", shortLabel: "KITCHEN", details: ["Mop kitchen floor", ...] },
    de: { name: "Küche",   shortLabel: "KÜCHE",   details: ["Küchenboden wischen", ...] }
}
```

- `name` – Überschrift im Popup
- `shortLabel` – Beschriftung am Rad; `\n` erzeugt einen Zeilenumbruch. Kurz halten, sonst ragt sie über den gelben Rand.
- `details` – die Checkliste im Popup

Die Reihenfolge entspricht den Positionen am Rad im Uhrzeigersinn, beginnend oben. Beim Ändern immer beide Sprachen anpassen.

### Oberflächentexte – `UI_TEXT`

Buttons, Hinweise und „Vor X Wochen“ für beide Sprachen.

## Wie die Rotation berechnet wird

- Referenz ist **Montag, 29.12.2025** (KW 1/2026), `REFERENCE_MONDAY`. Ab dort wird gezählt, wie viele Wochen vergangen sind.
- Pro Woche dreht sich das Fünfeck um ein Feld (360° / Anzahl Personen).
- Person Nummer *i* in `PEOPLE` hat in einer Woche die Aufgabe Nummer *(i + Wochen seit Referenz) mod Anzahl*.

Wer `REFERENCE_MONDAY` ändert, verschiebt den gesamten Plan.

## Lokal ansehen

Es gibt kein `npm run dev`. Entweder die Datei direkt öffnen:

```
open index.html
```

oder einen kleinen lokalen Server starten (näher an GitHub Pages):

```
python3 -m http.server 8000
```

und http://localhost:8000 aufrufen.

## Veröffentlichen

Die Seite läuft über **GitHub Pages** direkt aus dem `main`-Branch. Ein Push genügt; nach 1–2 Minuten ist die Änderung live.

```
git add index.html
git commit -m "Kurze Beschreibung der Änderung"
git push
```

## Bekannte Grenzen

- Ob eine Aufgabe erledigt wurde, speichert die App nicht. Dafür bräuchte es einen Server oder eine Datenbank.
- Die Schrift (Gochi Hand) wird von Google Fonts geladen.
- Die Link-Vorschau (`og:image`) verweist auf `preview-image.jpg`, das nicht im Repo liegt. Das Vorschaubild ist stattdessen in Telegram hinterlegt.
