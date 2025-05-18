# TodoApp

## Projektbeschreibung

Dieses Projekt ist eine einfache Kalender-Anwendung, die es Benutzern ermöglicht, Termine und die Verschiedenen Bereiche des Lebens zu ordnen. Es ist ein Tool, um Klarheit über die individuellen Aufgaben und Ziele des Nutzers zu schaffen. Die Anwendung bietet Funktionen wie das Hinzufügen von Aufgaben, das Verwalten von Zweigen (Branches) und das Verfolgen von Zielen. Sie wurde mit Vue.js entwickelt, um eine moderne und reaktive Benutzeroberfläche bereitzustellen.

## Motivation

Die Oberfläche der App spiegelt genau meine Planung wieder, die ich vor stressigen Monaten eine lange Zeit lang auf Papier aufgezeichnet habe. Daher ist die Struktur mittlerweile sehr ausgereift und zumindest für meine Anwendung perfekt. So konnte ich mit den einfachsten Mitteln einen Überblick behalten und meine Monate strukturieren.

## Warum ein Framework wie Vue.js?

Die Entscheidung, ein Framework wie Vue.js zu verwenden, basiert auf folgenden Vorteilen:

1. **Reaktivität und Datenbindung**: Vue.js bietet eine einfache Möglichkeit, Daten und die Benutzeroberfläche synchron zu halten. Änderungen an den Daten werden automatisch in der Benutzeroberfläche reflektiert.
2. **Komponentenbasierte Architektur**: Die Anwendung ist in wiederverwendbare Komponenten wie `BranchManager`, `GoalsManager` und `Calendar` unterteilt. Dies erleichtert die Wartung und Erweiterung des Codes.
3. **Einfachheit und Flexibilität**: Vue.js ist leichtgewichtig und einfach zu erlernen, weswegen es ideal für dieses Projekt wirkte.


### Nutzung der Reaktivität in Vue.js

Die Reaktivität von Vue.js wurde in diesem Projekt genutzt, um die Benutzeroberfläche dynamisch und interaktiv zu gestalten. Ein Beispiel dafür ist die Erstellung von Zielen (Goals) basierend auf den existierenden Zweigen (Branches).

- **Datenbindung**: Die Daten für Zweige und Ziele werden in der data-Option der Vue-Komponenten definiert. Änderungen an diesen Daten werden automatisch in der Benutzeroberfläche dargestellt.

- **Reaktive Abhängigkeiten**: Wenn ein Benutzer einen neuen Zweig erstellt oder einen bestehenden Zweig bearbeitet, werden die Ziele automatisch aktualisiert, um die Änderungen widerzuspiegeln. Vue.js stellt sicher, dass alle abhängigen Komponenten und Daten aktualisiert werden, wenn sich die zusammenhängenden liegenden Daten ändern.

```javascript
addTodo(branch) {
  if (branch.newTodo?.trim()) {
    branch.todos.push({
      text: branch.newTodo.trim(),
      completed: false
    });
    branch.newTodo = '';
    // Speichern der aktualisierten Daten in LocalStorage
    localStorage.setItem('branches', JSON.stringify(this.branches));
  }
}
```
### Erklärung:
- Wenn ein neues Todo hinzugefügt wird, wird es direkt in der todos-Liste des entsprechenden Branches gespeichert.
- Nach dem Hinzufügen wird die aktualisierte Liste der Branches in LocalStorage gespeichert.

## Verwendung von LocalStorage

### Implementierung

LocalStorage wird in diesem Projekt verwendet, um Benutzerdaten wie Zweige (Branches), Aufgaben (Todos) und Ziele (Goals) zu speichern. Dies wird durch die `localStorage`-API des Browsers erreicht. Beispiele für die Implementierung:

- **Speichern von Daten**:
  ```javascript
  localStorage.setItem('branches', JSON.stringify(this.branches));
  ```
  Hier werden die Zweige als JSON-String gespeichert. Die JSON.stringify() Methode konvertiert Werte in einen JSON-String.

- **Abrufen von Daten**:
  ```javascript
  this.branches = JSON.parse(localStorage.getItem('branches') || '[]');
  ```
  Die Methode JSON.parse() ruft eine JSON-Zeichenkette ab und erstellt daraus ein JavaScript-Objekt. Hier werden die gespeicherten Daten abgerufen und in ein JavaScript-Objekt umgewandelt.
  
### Nachteile von LocalStorage

1. **Größenbeschränkung**: Der LocalStorage des Browsers hat eine Speichergrenze von etwa 5 MB pro Domain, was für größere Datenmengen unzureichend sein kann, für dieses Projekt jedoch vollkommen ausreicht.
2. **Sicherheitsrisiken**: Der LocalStorage ist anfällig für XSS-Angriffe (Cross-Site Scripting), weil die Daten im Klartext gespeichert werden und von JavaScript gelesen werden können. Cookies könnten hingegen mit Flags wie 'HttpOnly' und 'Secure' geschützt werden. Dieses Projekt hat jedoch keinen so hohen Sicherheitsanspruch.


### Vorteile von LocalStorage

1. **Persistenz**: Daten bleiben auch nach dem Schließen des Browsers erhalten.
2. **Einfachheit**: Die `localStorage`-API erfordert keine zusätzlichen Bibliotheken.
3. **Keine Serverabhängigkeit**: Da die Daten lokal gespeichert werden ist kein Server erforderlich. Dies vereinfacht die Anwendung und macht sie sehr unabhängig.
4. **Datenschutz**: Im Gegensatz zu Cookies werden die Daten an keinen Server gesendet. Der Nutzer speichert seine Daten lokal.

   
## Verzicht auf Login

Ein Login-System wurde bewusst nicht implementiert, um die Komplexität des Projekts gering zu halten. Ziel war es, eine einfache und leicht verständliche Anwendung zu erstellen, die ohne Benutzerkonten auskommt. Dies macht die Anwendung ideal für den lokalen Gebrauch oder für Benutzer, die keine sensiblen Daten speichern möchten.
Ein weiterer Vorteil des Verzichts auf ein Login-System ist die Benutzerfreundlichkeit. Benutzer können die Anwendung sofort nutzen, ohne sich registrieren, was die Einstiegshürde senkt.
Zudem wird die Entwicklung und Wartung vereinfacht, da keine zusätzlichen Sicherheitsmaßnahmen wie Passwortverschlüsselung oder Authentifizierungsmechanismen implementiert werden müssen. Dies spart Entwicklungszeit und reduziert potenzielle Sicherheitsrisiken.
Da keine Authentifizierung über einen Server erforderlich ist, kann die Anwendung vollständig offline genutzt werden.


## Projekt Demo

Das Projelt wird aktuell unter folgender URL gehostet. https://etmt.phwt.de/~k.kusu/


