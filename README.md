# TodoApp

## Projektbeschreibung

Dieses Projekt ist eine einfache To-Do-Anwendung, die es Benutzern ermöglicht, Aufgaben zu erstellen, zu verwalten und zu organisieren. Die Anwendung bietet Funktionen wie das Hinzufügen von Aufgaben, das Verwalten von Zweigen (Branches) und das Verfolgen von Zielen. Sie wurde mit Vue.js entwickelt, um eine moderne und reaktive Benutzeroberfläche bereitzustellen.

## Warum ein Framework wie Vue.js?

Die Entscheidung, ein Framework wie Vue.js zu verwenden, basiert auf mehreren Vorteilen:

1. **Reaktivität und Datenbindung**: Vue.js bietet eine einfache Möglichkeit, Daten und die Benutzeroberfläche synchron zu halten. Änderungen an den Daten werden automatisch in der Benutzeroberfläche reflektiert.
2. **Komponentenbasierte Architektur**: Die Anwendung ist in wiederverwendbare Komponenten wie `BranchManager`, `GoalsManager` und `Calendar` unterteilt. Dies erleichtert die Wartung und Erweiterung des Codes.
3. **Einfachheit und Flexibilität**: Vue.js ist leichtgewichtig und einfach zu erlernen, was es ideal für kleinere Projekte wie dieses macht.
4. **Community und Ökosystem**: Vue.js verfügt über eine große Community und ein reichhaltiges Ökosystem, das die Entwicklung beschleunigt.

## Verwendung von LocalStorage

### Implementierung

LocalStorage wird in diesem Projekt verwendet, um Benutzerdaten wie Zweige (Branches), Aufgaben (Todos) und Ziele (Goals) zu speichern. Dies wird durch die `localStorage`-API des Browsers erreicht. Beispiele für die Implementierung:

- **Speichern von Daten**:
  ```javascript
  localStorage.setItem('branches', JSON.stringify(this.branches));
  ```
  Hier werden die Zweige als JSON-String gespeichert.

- **Abrufen von Daten**:
  ```javascript
  this.branches = JSON.parse(localStorage.getItem('branches') || '[]');
  ```
  Hier werden die gespeicherten Daten abgerufen und in ein JavaScript-Objekt umgewandelt.

### Vorteile von LocalStorage

1. **Persistenz**: Daten bleiben auch nach dem Schließen des Browsers erhalten.
2. **Einfachheit**: Die `localStorage`-API ist einfach zu verwenden und erfordert keine zusätzlichen Bibliotheken.
3. **Keine Serverabhängigkeit**: Da die Daten lokal gespeichert werden, ist kein Server erforderlich, was die Anwendung schneller und unabhängiger macht.
4. **Datenschutz**: Im Gegensatz zu Cookies werden die Daten nicht automatisch an den Server gesendet, was die Privatsphäre der Benutzer schützt.

## Verzicht auf Login

Ein Login-System wurde bewusst nicht implementiert, um die Komplexität des Projekts gering zu halten. Ziel war es, eine einfache und leicht verständliche Anwendung zu erstellen, die ohne Benutzerkonten auskommt. Dies macht die Anwendung ideal für den lokalen Gebrauch oder für Benutzer, die keine sensiblen Daten speichern möchten.

## Fazit

Dieses Projekt zeigt, wie man mit Vue.js eine moderne und reaktive Anwendung erstellen kann. Die Verwendung von LocalStorage bietet eine einfache und effektive Möglichkeit, Daten zu speichern, ohne auf externe Server angewiesen zu sein. Durch den Verzicht auf ein Login-System bleibt die Anwendung einfach und benutzerfreundlich.


## Projekt Demo

Das Projelt wird aktuell unter folgender URL gehostet. https://etmt.phwt.de/~k.kusu/


