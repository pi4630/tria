# Tria – Architektur

> **Status:** Entwurf / Proof of Concept  
> **Sprache:** Deutsch  
> **Lizenz:** GNU Affero General Public License v3.0 (AGPL-3.0)
> **Version dieses Dokuments:** 0.1

## 1. Zweck des Projekts

Tria ist ein Open-Source-Proof-of-Concept für die Digitalisierung der
Sprachgruppenzugehörigkeitserklärung in Südtirol.

Das Projekt untersucht, wie die Abgabe, Verwaltung, Änderung, der Widerruf
und die Bescheinigung einer Sprachgruppenzugehörigkeit als durchgängig
digitales Verfahren umgesetzt werden könnten.

Tria ist keine produktive Behördenanwendung und stellt keine rechtsgültigen
Bescheinigungen aus. Der POC soll wesentliche fachliche, datenschutzrechtliche,
sicherheitstechnische und kryptographische Eigenschaften einer möglichen
Zielarchitektur ausführbar und überprüfbar demonstrieren.

Ein wesentliches Projektziel ist die Nachvollziehbarkeit:

**Rechtsnorm → fachliche Anforderung → Architekturentscheidung → technische Umsetzung**

Die Architektur soll dadurch nicht nur beschreiben, *wie* das System
funktioniert, sondern auch, *warum* wesentliche Entscheidungen getroffen wurden.


## 2. Architekturprinzipien

### 2.1 Bürgerzentrierung

Das System verwaltet nicht lediglich Datensätze über Sprachgruppen, sondern
unterstützt Bürgerinnen und Bürger bei der Ausübung eines gesetzlich geregelten
Rechts.

Die Benutzeroberfläche muss deshalb jederzeit verständlich darstellen:

- welcher fachliche Zustand aktuell besteht,
- welche Erklärung derzeit wirksam ist,
- welche Verfahren aktuell zulässig sind,
- welche Verfahren bereits laufen,
- welche zukünftigen Rechtswirkungen eintreten,
- und zu welchem konkreten Zeitpunkt diese eintreten.

Technische Statuswerte dürfen rechtlich relevante Zustände nicht verschleiern.


### 2.2 State Transparency

Rechtliche Übergangszustände sind Teil des Domainmodells.

Insbesondere ist zwischen der Abgabe einer Erklärung und ihrer rechtlichen
Wirksamkeit zu unterscheiden.

Beispiel:

```text
Änderung abgegeben
       │
       │ bisherige Erklärung bleibt wirksam
       │
       ▼
zukünftiger Wirksamkeitszeitpunkt
       │
       ▼
neue Erklärung wird wirksam
```

Die Anwendung muss solche Übergänge mit konkreten Daten verständlich darstellen.


### 2.3 Purpose-bound Access

Der Zugriff auf besonders schützenswerte Fachdaten wird nicht allein durch
eine Benutzerrolle legitimiert.

Jeder Zugriff muss an einen zulässigen Zweck und ein autorisiertes Verfahren
gebunden sein.

Das grundlegende Modell lautet:

```text
Purpose
   │
   ▼
Procedure
   │
   ▼
Context
   │
   ▼
Authorization / Data Access
   │
   ▼
Protected Domain Data
```

**Purpose** beschreibt, warum eine Verarbeitung stattfinden darf.

**Procedure** beschreibt das fachliche Verfahren, mit dem dieser Zweck
umgesetzt wird.

**Context** bezeichnet die konkrete Instanz dieses Verfahrens, beispielsweise
einen bestimmten Antrag einer bestimmten authentifizierten Person zu einem
bestimmten Zeitpunkt.

Eine technische Rolle allein begründet keinen Zugriff auf Fachdaten.


### 2.4 Procedure-bound State Changes

Fachliche Daten werden nicht durch beliebige CRUD-Operationen verändert.

> **Eine fachliche Zustandsänderung ist immer das Ergebnis eines autorisierten
> Verfahrens.**

Dies gilt insbesondere für:

- erstmalige Erklärung,
- Änderung,
- Widerruf,
- Ausstellung einer Bescheinigung.

Direkte Änderungen fachlicher Zustände unter Umgehung der zugehörigen
Domain Rules sind nicht vorgesehen.


### 2.5 Context Propagation

Kann ein externes Verfahren ein internes Verfahren auslösen, muss der
ursprüngliche fachliche und sicherheitsrelevante Kontext nachvollziehbar
propagiert werden.

Ein nachgelagerter Dienst darf einen Datenzugriff nicht allein deshalb
durchführen, weil der Aufruf technisch von einem vertrauenswürdigen Dienst
stammt.


### 2.6 Privacy by Design und Datenminimierung

Die technische Verfügbarkeit eines personenbezogenen Attributs begründet
weder dessen fachliche Erforderlichkeit noch dessen Speicherung.

Insbesondere gilt:

> **Verfügbar ≠ erforderlich ≠ zu speichern.**

Attribute eines Identity Providers, eines Ausweisdokuments oder eines
öffentlichen Registers werden nur verarbeitet, wenn ein konkreter Purpose
dies erfordert.

Wo möglich, werden personenbezogene Ausgangsdaten frühzeitig auf weniger
identifizierende Informationen reduziert.


### 2.7 Write Once, Run Everywhere

Der POC soll ohne Bindung an ein bestimmtes Desktop-Betriebssystem entwickelt
und ausgeführt werden können.

Unterstützte Entwicklungsplattformen sollen mindestens sein:

- Linux,
- macOS,
- Windows.

Plattformspezifische Abhängigkeiten werden möglichst vermieden.

Die lokale Infrastruktur wird soweit sinnvoll containerisiert. Abhängigkeiten
der Java-Anwendung werden über Maven verwaltet.


### 2.8 Open Source und Reproduzierbarkeit

Ein Informatiker soll Tria aus dem öffentlichen Repository beziehen und mit
wenigen nachvollziehbaren Schritten lokal starten können.

Der Bootstrap soll insbesondere:

1. technische Voraussetzungen prüfen,
2. fehlende Voraussetzungen verständlich benennen,
3. lokale Konfiguration erzeugen,
4. kryptographisches Development-Schlüsselmaterial erzeugen,
5. PostgreSQL starten,
6. Datenbankschema und Migrationen anwenden,
7. die Anwendung starten,
8. den Zustand der Anwendung überprüfen.

Das Setup darf sicherheitsrelevante Änderungen am Hostsystem nicht ungefragt
durchführen.


## 3. Rechtlicher Kontext

### 3.1 Zentrale Rechtsgrundlage

Zentrale fachliche Rechtsgrundlage ist Art. 20-ter des
D.P.R. 26. Juli 1976, Nr. 752 in der jeweils geltenden Fassung.

Die Norm regelt insbesondere:

- die individuelle namentliche Erklärung der Zugehörigkeit bzw. Angliederung
  zu einer der drei Sprachgruppen,
- die Abgabe und Aufbewahrung der Erklärung,
- die Ausstellung einer Bescheinigung,
- die Änderung einer Erklärung,
- den Widerruf,
- die zeitliche Wirksamkeit,
- Sonderregelungen für bestimmte Personengruppen.

Die Architektur darf gesetzlich bestimmte Fristen nicht als technische
Eigenschaften implementieren, sondern muss sie als fachliche Regeln behandeln.


### 3.2 Aktuelle zeitliche Domain Rules

Nach der derzeitigen Rechtslage sind insbesondere folgende Zeitregeln relevant:

#### Reguläre erstmalige Erklärung

Grundsätzlich tritt die Wirkung 18 Monate nach Abgabe der Erklärung ein.

#### Erklärung nach Verständigung durch die Gemeinde

Wird die Erklärung innerhalb der gesetzlich vorgesehenen Jahresfrist nach der
entsprechenden Verständigung abgegeben, tritt die Wirkung unmittelbar ein.

#### Minderjährige

Erklärungen von Personen zwischen 14 und 18 Jahren sind unmittelbar wirksam.

#### Änderung

Eine bestehende Erklärung kann grundsätzlich frühestens fünf Jahre nach ihrer
Abgabe geändert werden.

Die Änderung wird zwei Jahre nach ihrer Abgabe wirksam.

Bis zu diesem Zeitpunkt bleibt die bisherige Erklärung wirksam.

#### Widerruf

Eine Erklärung kann jederzeit widerrufen werden.

Nach einem Widerruf kann eine neue Erklärung grundsätzlich frühestens drei
Jahre nach der gesetzlich maßgeblichen Rückgabe der widerrufenen Erklärung
abgegeben werden.

Die neue Erklärung wird weitere zwei Jahre später wirksam.

#### Bestimmte nicht in Südtirol ansässige Personen

Art. 20-ter Abs. 7-bis sieht auch Erklärungen bestimmter EU-Bürger und ihrer
Familienangehörigen sowie bestimmter Drittstaatsangehöriger vor.

Für die erste Erklärung dieser Personengruppen sieht die Norm grundsätzlich
unmittelbare Wirksamkeit vor.

### 3.3 Architekturkonsequenz zeitlicher Regeln

Zeitliche Rechtswirkungen werden durch fachliche, versionierbare Regeln
bestimmt und nicht aus technischen Prozessabläufen abgeleitet.

Das Domainmodell muss daher mindestens zwischen folgenden Konzepten
unterscheiden können:

```text
submittedAt
effectiveAt
revokedAt
supersededAt
```

Die konkrete Modellierung wird im Zuge des Domain Designs festgelegt.

Gesetzliche Fristen dürfen nicht verteilt als technische Konstanten in
Controller-, UI- oder Persistenzlogik implementiert werden.


## 4. Rechtliche Prämisse der Zielarchitektur

Die derzeitige Rechtslage enthält weiterhin Elemente des papiergebundenen
Verfahrens. Insbesondere sieht Art. 20-ter die Aufbewahrung der Erklärung in
einer verschlossenen gelben Namenshülle vor und untersagt beim zuständigen
Amt bestimmte Aufzeichnungen bzw. Registrierungen des Inhalts, auch in
informatischer Form.

Gleichzeitig sieht Art. 20-ter telematische Verfahren vor. Der italienische
Datenschutzgarant hat 2024 zu einem Entwurf einer Durchführungsverordnung für
die telematische Erklärung und Zertifizierung Stellung genommen. Dieser
Entwurf sah weiterhin eine Überführung elektronisch abgegebener Erklärungen
in eine analoge Aufbewahrungsform vor.

### Architekturprämisse für Tria

Tria bildet **nicht** die gelbe Papierhülle technisch nach.

Für die Zielarchitektur wird angenommen, dass die einschlägigen
Rechtsgrundlagen so angepasst werden, dass eine vollständig elektronische,
datenschutzkonforme Verarbeitung und Aufbewahrung zulässig ist.

Diese Annahme ist ausdrücklich eine **SOLL-Prämisse des POC** und keine
Beschreibung der derzeit uneingeschränkt zulässigen produktiven Umsetzung.

Die Unterschiede zwischen geltendem Recht und den Annahmen der Zielarchitektur
sind nachvollziehbar zu dokumentieren.


## 5. Akteure

### 5.1 Bürger

Der Bürger ist der zentrale externe Akteur.

Für produktive externe Verfahren wird eine starke elektronische
Authentifizierung über einen staatlich anerkannten Identity Provider
vorausgesetzt, insbesondere über SPID oder CIE.

Das konkret erforderliche Authentifizierungs- bzw. Assurance-Niveau ist vor
einer produktiven Umsetzung rechtlich und technisch zu bestimmen.

Der Identity Provider beantwortet die Frage:

> **Wer ist diese Person?**

Er entscheidet nicht, ob ein fachliches Verfahren zulässig ist.

Diese Entscheidung trifft das zuständige Fachsystem auf Grundlage von:

- authentifizierter Identität,
- bestehendem fachlichem Zustand,
- Purpose,
- Procedure Context,
- gesetzlichen Voraussetzungen,
- zeitlichen Domain Rules.

### 5.2 Zuständige Behörde

Das zuständige System des Tribunale di Bolzano / Landesgerichts Bozen wird in
der Zielarchitektur als autoritative fachliche Instanz betrachtet.

Die genaue organisatorische, datenschutzrechtliche und technische
Verantwortungsverteilung ist für eine produktive Umsetzung gesondert zu
bestimmen.

### 5.3 Interner Benutzer / internes Verfahren

Interne Akteure erhalten keinen generellen Zugriff auf sämtliche Fachdaten.

Auch interne Verarbeitung erfolgt innerhalb eines definierten Purpose und
Procedure Context.


## 6. Externe Verfahren

Der POC soll mindestens folgende Bürgerverfahren fachlich berücksichtigen.

### 6.1 Erstmalige Erklärung

Das System stellt fest:

- ob bereits eine Erklärung existiert,
- ob die Person die Voraussetzungen erfüllt,
- welche zeitlichen Regeln gelten,
- ab welchem Zeitpunkt die Erklärung wirksam wird.

Vor der endgültigen Abgabe werden die Auswirkungen verständlich dargestellt.

### 6.2 Bescheinigung anfordern

Eine berechtigte Person kann eine Bescheinigung über den fachlich maßgeblichen
Zustand anfordern.

Die Bescheinigung wird als PDF erzeugt und kryptographisch abgesichert.

### 6.3 Erklärung ändern

Das System prüft insbesondere:

- ob eine wirksame bzw. relevante bestehende Erklärung vorliegt,
- ob die gesetzliche Mindestfrist abgelaufen ist,
- wann eine Änderung abgegeben werden darf,
- wann die Änderung wirksam wird.

Die Anwendung zeigt sowohl die aktuell wirksame als auch die zukünftige
Erklärung und den konkreten Wirksamkeitszeitpunkt.

### 6.4 Erklärung widerrufen

Der Widerruf ist als eigenständiges Verfahren zu behandeln.

Vor Abschluss des Verfahrens muss die betroffene Person verständlich über die
Folgen informiert werden, insbesondere über Wartezeiten für eine spätere neue
Erklärung und deren Wirksamkeit.


## 7. Internes Verfahren: Altersstatistik

Der POC implementiert bewusst mindestens ein internes Verfahren.

### 7.1 Purpose

Der Zweck besteht in der Erstellung einer aggregierten Statistik über
Sprachgruppen nach Altersklassen.

Dieser Purpose legitimiert **keinen allgemeinen Zugriff auf Bürgerdaten**.

### 7.2 Datenminimierung

Für die Statistik werden beispielsweise benötigt:

```text
Geburtsdatum
     │
     ▼
Ableitung
     │
     ▼
Altersklasse
     │
     ├──────────────┐
     │              │
Sprachgruppe        │
     │              │
     └──────► Aggregation
                    │
                    ▼
             Statistik
```

Name, Steuernummer und Anschrift werden für diesen Purpose nicht benötigt.

Das Geburtsdatum soll innerhalb des geschützten Verarbeitungskontexts soweit
möglich lediglich zur Ableitung einer Altersklasse verwendet werden.

Das statistische Ergebnis enthält keine Individualdatensätze.

### 7.3 Gemeindestatistik

Eine Statistik nach Gemeinde ist **nicht automatisch** Bestandteil desselben
Purpose.

Sie würde die Verarbeitung eines zusätzlichen personenbezogenen Merkmals
erfordern.

Bevor eine solche Statistik zulässig oder implementiert werden kann, sind
insbesondere zu klären:

- Rechtsgrundlage und konkreter Zweck,
- autoritative Quelle der Wohnsitzinformation,
- Notwendigkeit einer dauerhaften Speicherung,
- Möglichkeit einer zweckgebundenen temporären Ableitung,
- Umgang mit Personen ohne Wohnsitz in Südtirol,
- Re-Identifikationsrisiken bei kleinen Gemeinden und kleinen Gruppen,
- Mindestgrößen bzw. Unterdrückung kleiner statistischer Zellen.

Tria speichert personenbezogene Attribute nicht vorsorglich allein aufgrund
eines möglichen zukünftigen statistischen Interesses.


## 8. Datenschutz und GDPR/DSGVO

Datenschutz wird als Architekturmerkmal und nicht als nachgelagerte
Compliance-Aufgabe behandelt.

### 8.1 Zweckbindung

Jede Verarbeitung benötigt einen definierten fachlichen Purpose.

### 8.2 Datenminimierung

Es werden nur diejenigen personenbezogenen Attribute verarbeitet, die für den
konkreten Zweck erforderlich sind.

### 8.3 Privacy by Design und Privacy by Default

Datenschutzanforderungen werden bereits beim Domain-, Daten- und
Sicherheitsdesign berücksichtigt.

### 8.4 Identifikation ist nicht Fachdatenhaltung

Daten, die zur Identifikation einer Person sichtbar oder verfügbar sind,
werden nicht automatisch Bestandteil des fachlichen Datenbestands.

Dies entspricht auch dem Grundprinzip des bestehenden analogen Verfahrens:
Ein zur Identifikation vorgelegtes Dokument kann Informationen enthalten, die
für das konkrete Fachverfahren nicht übernommen werden müssen.

### 8.5 Statistik

Statistische Verfahren sollen möglichst mit abgeleiteten und reduzierten
Merkmalen arbeiten.

Wo möglich, verlassen ausschließlich aggregierte Ergebnisse den geschützten
Verarbeitungskontext.

### 8.6 Audit und Datenschutz

Audit-Logs dürfen nicht zu einer zweiten, schwächer geschützten Ablage
sensibler Fachdaten werden.

Audit-Ereignisse sollen insbesondere Kontext und Nachvollziehbarkeit
dokumentieren, ohne unnötig fachliche Klartextdaten zu protokollieren.

Beispiel:

```text
procedureId
procedureType
purpose
initiator
subjectReference
startedAt
completedAt
result
```

Die endgültige Struktur wird im Security Design festgelegt.


## 9. Sicherheitsarchitektur

### 9.1 Grundsatz

Der POC soll zentrale Sicherheitsmechanismen tatsächlich implementieren und
prüfbar machen.

Sicherheit soll nicht ausschließlich durch Infrastrukturdiagramme beschrieben
werden.

### 9.2 Defense in Depth

Vorgesehen sind mehrere voneinander unabhängige Schutzschichten, insbesondere:

- Transportverschlüsselung,
- Schutz gespeicherter Daten,
- Application-/Field-Level Encryption für besonders sensible Fachdaten,
- getrennte Datenbankrollen,
- Least Privilege,
- Purpose-bound Access,
- kontrollierte Schlüsselverwaltung,
- Audit Logging,
- Secret Management.

### 9.3 Schutz vor privilegiertem Datenbankzugriff

Ein Datenbankadministrator soll durch direkten Zugriff auf PostgreSQL oder
durch einen vollständigen Datenbank-Dump besonders geschützte Fachdaten nicht
automatisch im Klartext lesen können.

Dies soll im POC als überprüfbare Sicherheitseigenschaft demonstriert werden.

### 9.4 Schlüsselverwaltung

Der POC verwendet lokale kryptographische Schlüssel.

Private Schlüssel werden niemals im Git-Repository gespeichert.

Die Architektur trennt die fachliche Signaturfunktion von der konkreten
Schlüsselbereitstellung.

Konzeptionell:

```text
SigningService
     │
     ├── LocalDevelopmentKeyProvider    # POC
     │
     └── ExternalKeyProvider            # Zielarchitektur
```

Eine produktive Architektur soll die Verwendung geeigneter externer
Key-Management- bzw. HSM-Infrastruktur ermöglichen.


## 10. Bescheinigung und QR-Code

### 10.1 Grundprinzip

Eine erzeugte Bescheinigung enthält:

1. einen menschenlesbaren Dokumentinhalt,
2. eine maschinenlesbare Repräsentation der relevanten Inhalte,
3. eine kryptographische Signatur bzw. ein geeignetes elektronisches Vertrauensmerkmal,
4. einen QR-Code als Transportcontainer für die maschinenlesbaren Daten.

Der QR-Code selbst stellt keine kryptographische Sicherheit her.

### 10.2 Verarbeitung

Konzeptionell:

```text
Certificate Data
       │
       ▼
Canonical Representation
       │
       ▼
Signing Service
       │
       ▼
Signed Payload
       │
       ▼
QR Encoder
       │
       ▼
QR Code
       │
       ▼
PDF Certificate
```

Für die QR-Code-Erzeugung soll eine geeignete Java-Bibliothek über Maven
eingebunden werden. Eine separate Installation eines QR-Code-Generators auf
dem Entwicklungsrechner ist nicht vorgesehen.

### 10.3 Bindung an den sichtbaren Dokumentinhalt

Der signierte maschinenlesbare Inhalt muss so gestaltet werden, dass eine
Verifier-Anwendung den sichtbaren Inhalt der Bescheinigung mit dem signierten
Inhalt vergleichen kann.

Dadurch soll insbesondere verhindert werden, dass ein gültiger QR-Code
unbemerkt auf ein manipuliertes Dokument übertragen wird.


## 11. Verifikation

Eine Smartphone- oder Desktop-Anwendung zur Prüfung einer Bescheinigung ist
nicht Bestandteil des POC.

Die Schnittstellen und Datenformate müssen eine spätere unabhängige
Verifier-Implementierung jedoch ermöglichen.

Eine solche Anwendung soll konzeptionell zwei unterschiedliche Aussagen
prüfen können:

### Kryptographische Echtheit

Offline prüfbar, soweit notwendige Vertrauensinformationen verfügbar sind:

- Integrität des Payloads,
- kryptographische Signatur,
- Aussteller,
- Bindung zwischen sichtbarem und signiertem Inhalt.

### Fachliche Aktualität

Eine kryptographisch gültige Bescheinigung muss nicht notwendigerweise den
heute aktuellen fachlichen Zustand darstellen.

Eine spätere Zielarchitektur kann daher zusätzlich eine Online-Prüfung des
aktuellen Status beim autoritativen System vorsehen.

```text
QR
 │
 ├──► Offline verification
 │       ├── signature
 │       ├── issuer
 │       └── document binding
 │
 └──► Optional online status
         └── current / superseded / revoked
```


## 12. Technische POC-Architektur

### 12.1 Zielplattform

Der POC soll lokal auf Windows, macOS und Linux ausführbar sein.

Für einen öffentlich erreichbaren Demonstrationsbetrieb ist ein Deployment auf
einem Raspberry Pi vorgesehen.

### 12.2 Anwendung

Vorgesehener Technologie-Stack:

- Java,
- Spring Boot,
- Maven,
- PostgreSQL,
- Docker / Docker Compose.

Weitere technische Komponenten werden erst nach fachlicher und
sicherheitstechnischer Bewertung festgelegt.

### 12.3 Datenbank

PostgreSQL wird für lokale Entwicklung und POC-Betrieb containerisiert
bereitgestellt.

Datenbankschemaänderungen sollen über versionierte Migrationen erfolgen.

### 12.4 Build

Der Maven Wrapper wird in das Repository aufgenommen.

Entwickler sollen keine lokale Maven-Installation benötigen.

### 12.5 Bootstrap

Für den Einstieg sind mindestens vorgesehen:

```text
setup.sh        Linux / macOS
setup.ps1       Windows PowerShell
```

Die Skripte sollen möglichst wenig eigene Geschäftslogik enthalten und
denselben plattformneutralen Bootstrap-Prozess orchestrieren.

Fehlende Systemvoraussetzungen werden erkannt und erklärt, jedoch nicht
ungefragt mit administrativen Rechten installiert.


## 13. Scope des POC

Der POC soll insbesondere demonstrieren:

- Bürgerverfahren für Erklärung, Bescheinigung, Änderung und Widerruf,
- zeitabhängiges Domainmodell,
- State Transparency,
- Purpose-bound Access,
- Procedure Context,
- mindestens ein internes Statistikverfahren,
- Datenminimierung,
- PostgreSQL-Persistenz,
- Schutz sensibler Daten,
- Field-/Application-Level Encryption,
- Least-Privilege-Datenbankzugriffe,
- Audit Logging,
- lokale Schlüsselverwaltung,
- kryptographisch geschützte Bescheinigung,
- QR-Code-Erzeugung,
- plattformneutralen Entwicklungs- und Startprozess.


## 14. Nicht-Scope des POC

Nicht Bestandteil der POC-Implementierung sind insbesondere:

### Produktive SPID-/CIE-Integration

Im POC wird eine lokale bzw. simulierte authentifizierte Identität verwendet.

Die Anwendungsarchitektur beginnt hinter der Identity-Trust-Boundary.

### Enterprise Privileged Access Management

Eine produktive Zielarchitektur benötigt geeignete Mechanismen für
privilegierte Zugriffe, insbesondere:

- Privileged Access Management (PAM),
- zeitlich begrenzte bzw. bedarfsgesteuerte Berechtigungen,
- Vaulting privilegierter Credentials,
- kontrollierte Break-Glass-Verfahren,
- nachvollziehbare administrative Sitzungen.

Produkte wie CyberArk sind mögliche Implementierungen dieser Fähigkeit, aber
keine Architekturvorgabe.

### HSM / Enterprise Key Management

Der POC verwendet lokale Schlüsselverwaltung.

Hardware Security Modules bzw. institutionelle Key-Management-Systeme werden
als Bestandteil einer produktiven Zielarchitektur betrachtet.

### Verifier-App

Eine Smartphone- oder Desktop-App zum Scannen und Prüfen von QR-Codes wird
nicht implementiert.

### Produktionsbetrieb

Insbesondere nicht Gegenstand des POC sind:

- Hochverfügbarkeit,
- Disaster Recovery,
- Geo-Redundanz,
- produktive Betriebsorganisation,
- produktive SOC-/SIEM-Integration,
- vollständige behördliche IAM-/PAM-Infrastruktur.


## 15. Offene Architekturentscheidungen

Folgende Punkte werden bewusst noch nicht festgelegt:

- konkretes Domainmodell und State Machine,
- genaue Identifikatoren des Bürgers,
- erforderliches SPID-/CIE-Assurance-Level,
- Signatur versus elektronisches Siegel,
- Signaturalgorithmus,
- Canonical Serialization des QR-Payloads,
- QR-Payload-Format,
- Schlüsselrotation,
- Status-/Revocation-Modell ausgestellter Bescheinigungen,
- genaue Field-Level-Encryption-Strategie,
- Secret-Management-Lösung des POC,
- genaue PostgreSQL-Rollenstruktur,
- statistische Mindestzellgrößen,
- Umgang mit Wohnsitz- bzw. Gemeindedaten,
- technische Ausgestaltung der Purpose-/Procedure-/Context-Autorisierung.

Offene Entscheidungen werden erst getroffen, wenn fachliche, rechtliche und
sicherheitstechnische Anforderungen hinreichend verstanden sind.


## 16. Normative und fachliche Referenzen

### D.P.R. 26. Juli 1976, Nr. 752 – Art. 20-ter

Zentrale Rechtsgrundlage für Erklärung, Bescheinigung, Änderung, Widerruf und
zeitliche Rechtswirkungen der Sprachgruppenzugehörigkeit bzw. -angliederung.

Normattiva:
https://www.normattiva.it/

### D.Lgs. 7. Mai 2026, Nr. 97

Änderung der Durchführungsbestimmungen zum Autonomiestatut, unter anderem zu
Art. 20-ter D.P.R. 752/1976.

Normattiva:
https://www.normattiva.it/eli/id/2026/06/05/26G00114/CONSOLIDATED/

### Verordnung (EU) 2016/679 – GDPR/DSGVO

Insbesondere relevant:

- Art. 5 – Grundsätze der Verarbeitung, insbesondere Zweckbindung,
  Datenminimierung, Speicherbegrenzung sowie Integrität und Vertraulichkeit,
- Art. 25 – Datenschutz durch Technikgestaltung und durch
  datenschutzfreundliche Voreinstellungen,
- weitere Bestimmungen werden im Zuge der Datenschutzanalyse ergänzt.

EUR-Lex:
https://eur-lex.europa.eu/eli/reg/2016/679

### Garante per la protezione dei dati personali – Stellungnahme vom 18.07.2024

Stellungnahme Nr. 463 vom 18. Juli 2024 zum Entwurf einer
Durchführungsverordnung über die individuelle namentliche Erklärung der
Sprachgruppenzugehörigkeit bzw. -angliederung in telematischer Form.

[GDPD | Dokument Nr. 10039488](https://www.garanteprivacy.it/home/docweb/-/docweb-display/docweb/10039488)


## 17. Dokumentationsprinzip

Architekturentscheidungen sollen zukünftig möglichst nach folgendem Muster
dokumentiert werden:

```text
Quelle / Norm
      │
      ▼
fachliche oder nichtfunktionale Anforderung
      │
      ▼
Architecture Decision
      │
      ▼
technische Umsetzung
      │
      ▼
Test / Nachweis
```

Dadurch soll insbesondere nachvollziehbar bleiben, welche Teile des Systems
auf einer gesetzlichen Vorgabe, einer fachlichen Entscheidung, einer
Sicherheitsanforderung oder einer rein technischen Implementierungsentscheidung
beruhen.