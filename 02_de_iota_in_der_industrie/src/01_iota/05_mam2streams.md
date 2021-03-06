# Masked Authenticated Messaging

[TODO]()

<div class="head_text">
Eine Einführung
</div>

Text Christian Oosting

## Einleitung
Bei Masked Authenticated Messaging (MAM) handelt es sich um ein Protokoll zur Datenübertragung, welches  als zusätzlicher Layer auf dem Tangle abgebildet wird. Dies ermöglicht es beispielsweise Sensoren ihre Messwerte an den Tangle zu senden und für gewünschte Parteien verfügbar zu machen. Die Funktionsweise ähnelt einem sozialen Netzwerk (z.B. YouTube), welches im Fall von MAM jedoch hauptsächlich für IoT-Geräte anstatt für Menschen gedacht ist. Es existieren Sender, die Kanäle erstellen und Empfänger, welche diese Kanäle abonnieren. Die drei wichtigsten Eigenschaften ergeben sich direkt aus dem Namen.

Verschlüsselt (Masked)
Nachrichten können nur von solchen Empfängern gelesen werden, die vom Sender dazu berechtigt wurden.

Authentifiziert (Authenticated)
Es kann sichergestellt werden, dass Nachrichten von einem bestimmten Sender kommen. Dazu nutzt der Sender seinen Seed.

Nachrichtenstream (Messaging)
Ein Gerät kann einen kontinuierlichen Stream von Nachrichten an den Tangle senden. Stream bedeutet in diesem Zusammenhang, dass jede Nachricht einen Verweis auf die folgende Nachricht enthält. Empfänger können den Stream von der ersten bekannten Nachricht an nur in Vorwärtsrichtung verfolgen. Auf vorherige Nachrichten besteht kein Zugriff.

<img MAM-Stream>
Vereinfachte Darstellung eines MAM-Streams

## Notwendigkeit
IOTA ermöglicht bereits ohne zusätzlichen Layer das Versenden wertfreier Transaktionen, also einfacher Nachrichten, an den Tangle. Es können also beispielsweise verschlüsselte Sensordaten in festgelegten Intervallen an eine vordefinierte Adresse gesendet werden. Aber wozu wird MAM dann überhaupt benötigt? Da der komplette Tangle öffentlich einsehbar ist, wäre es für Angreifer ein Leichtes Adressen zu finden, auf denen in regelmäßigen Abständen Transaktionen eingehen und diese mit falschen Transaktionen zu überfluten. Um dies zu umgehen, sollte die Adresse mit jeder neuen Nachricht gewechselt werden. Das ist beispielsweise möglich, indem die verschiedenen Adressen in einer zentralen Datenbank gehalten werden, um den Überblick zu behalten. Dadurch würde jedoch auf viele Vorteile verzichtet werden, die ja der eigentliche Grund für die Nutzung eines DLT wie IOTA sind. Eine weitere Möglichkeit ist ein komplexeres Verfahren, welches durch Verschlüsselung und Signaturen die Möglichkeit bietet, innerhalb des Tangle auf weitere Adressen zu verweisen ohne diese für potentielle Angreifer sichtbar zu machen. Genau dies ermöglicht MAM.   

## Gossip
IOTA verwendet das Gossip-Protokoll, um Transaktionen im Netzwerk zu verteilen. Das bedeutet, dass jeder Netzwerkknoten alle neuen Transaktionen an sämtliche benachbarten Knoten weiter “tratscht”. Das sind entweder solche Transaktionen, die der Knoten von anderen erhalten hat oder solche, die von diesem Knoten selbst ausgestellt wurden. Um nun einem MAM-Stream zu folgen,  benötigt man die jeweilige Channel-ID und prüft den eingehenden Tratsch darauf, ob dieser eine Nachricht mit der gewünschten ID enthält.

## Root, Adresse und Channel-ID
Beschäftigt man sich genauer mit MAM, stolpert man immer wieder über die Begriffe Root, Adresse und Channel-ID. Während die Channel-ID eine IOTA-Adresse bezeichnet, die Teil eines MAM-Streams ist, muss man bei der Abgrenzung zum Root etwas genauer hinsehen.

Was genau ist also ein Root? MAM verwendet den sogenannten Merkle-Tree. Dabei handelt es sich um eine hash-basierte Datenstruktur zur effizienten Verifizierung von Daten. Jeder Merkle-Tree enthält einen Wurzelknoten, welcher mit allen anderen Knoten und Blättern verbunden ist.. Dieser Wurzelknoten wird auch als Root bezeichnet und dient in MAM als Schlüssel zur Dekodierung von Nachrichten. In öffentlichen MAM-Streams ist der Root zusätzlich gleich der Adresse/Channel-ID.

<img: Merkle-Tree>
Merkle-Tree


## Zugriffsberechtigung
MAM bietet verschiedene Modi zum Streamen von Nachrichten an:

Public
Eine reine Adressabfrage ermöglicht Zugriff auf die Nachricht. Die Adresse ist gleich dem Root, wodurch es jedem, der die jeweilige Adresse abfragt, möglich ist die enthaltene Nachricht zu entschlüsseln.

Private
Im privaten Modus wird aus dem Root ein Hash generiert, welcher als Adresse dient. Nur Empfänger, welche den Root kennen, können also die Nachricht entschlüsseln. Dies ist sinnvoll für Netze verschiedener Geräte eines einzelnen Eigentümers. Dieser Modus sollte nicht verwendet werden, um externen Parteien Zugriff zu gewähren, da ein solcher Zugriff nicht wieder entzogen werden kann. 

Restricted
Zum Entschlüsseln der Nachrichten wird neben dem Root ein zusätzlicher Schlüssel (side key) benötigt. Somit können auch externe Parteien Zugriff auf die Daten erhalten. Durch Änderung des Schlüssels besteht die Möglichkeit den Zugriff 

## Snapshots
Um die Größe des  Tangle auf einem angemessenen Level zu halten, verwendet das IOTA-Protokoll sogenannte Snapshots. Dabei werden sämtliche vor dem Zeitpunkt des Snapshots getätigten Transaktionen gelöscht. Die Adressen behalten lediglich ihre Balance. Dies muss bei der Verwendung von MAM beachtet werden. Sollte es notwendig sein Nachrichten über einen langen Zeitraum im Tangle zu halten, wird die Nutzung einer Permanode-Lösung wie Chronicle empfohlen. Diese kann entweder selbst gehostet oder von externen Anbietern als kostenpflichtiger “query as a service” genutzt  werden. 

## Data Marketplace
Der Data Marketplace ist ein dezentraler Marktplatz für Daten wie beispielsweise Sensordaten. Diese werden in Echtzeit erfasst und mithilfe von MAM wird es anderen Teilnehmern im Marketplace ermöglicht Zugriff auf die Nachrichtenstreams zu erhalten. Als Benutzer des Data Marketplace haben Sie somit die Möglichkeit, mit MAM verschlüsselte Daten sowohl zu kaufen als auch  zu verkaufen.

Bisher wird ein Großteil der Daten in Datensilos gesammelt und selten über Unternehmensgrenzen hinweg genutzt. Der Data Marketplace soll die Verschwendung dieser Daten stoppen, Silogrenzen sprengen und die befreiten Daten in einem offenen und dezentralen Marktplatz sammeln.

## MAM in der Industrie
Es existieren unzählige Anwendungsbereiche für MAM in der Industrie. Wo immer die Authentizität von Daten eine Rolle spielt, kann der Einsatz von MAM Sinn ergeben. Dies ist in der Industrie vor allem der Fall, wenn gemeinsame Datensätze verschiedener Hersteller oder Zulieferer verwaltet werden müssen. 


<img Fujitsu Audit-Trail mit IOTA und MAM>
Fujitsu Audit-Trail mit IOTA und MAM

Fujitsu stellte bereits auf der Hannover Messe 2018 das Proof of Concept eines verteilten Audit-Trails auf Basis von IOTA vor, welches die Vorteile der Nutzung eines DLT wie IOTA gegenüber einer klassischen Datenbank verdeutlicht. Dabei wird ein zwischen zwei Unternehmen verteilter Produktionsprozess simuliert, indem zwei Roboter gemeinsam an einem Förderband arbeiten und Komponenten platzieren. Die Roboter sind in der Lage diese Komponenten eindeutig zu identifizieren sowie alle relevanten Daten wie ID, Standort und Zeitpunkt der Ausführung eines Arbeitsschrittes mithilfe von MAM manipulationssicher und für alle beteiligten Parteien abrufbar im Tangle zu speichern. Zusätzlich können die Roboter durch Überwachung des MAM-Streams erkennen, ob eine Komponente alle vorgeschriebene Produktionsschritte durchlaufen hat und Komponenten aussortieren, falls notwendig. Die beteiligten Unternehmen können somit auf eine gemeinsame Infrastruktur verzichten.


<img MAM FLOW>

## XDK2MAM
Bosch hat mit dem XDK (Cross Domain Development Kit) eine Prototyping-Plattform für das Internet der Dinge entwickelt. Das XDK lässt sich als leistungsstarke Lösung zur Entwicklung von Sensorknoten einsetzen. Es enthält ein Gyroskop, einen Beschleunigungssensor, einen Temperatursensor, einen Lichtsensor und vieles mehr. Zudem ist das Gerät mit WLAN, Bluetooth Low Energy, einem Steckplatz für SD-Karten und  gebrauchsfertigen Softwarepaketen ausgestattet.

XDK2MAM stellt die passende Software bereit, um mit dem XDK Sensordaten an den Tangle zu senden und diese auf dem IOTA Data Marketplace zu verkaufen. Es handelt sich um ein Open-Source-Projekt von Daniele de Michele und Alejandro Elustondo in Zusammenarbeit mit Daniel Trauth von der RWTH Aachen. XDK2MAM stellt Schnittstellen bereit, um Daten sowohl über HTTP als auch über Bluetooth Low Energy, MQTT oder USB an den Tangle zu streamen.  Dies gewährleistet eine einfach programmierbare, effiziente und kostengünstige Entwicklung von IoT-Prototypen in Verbindung mit MAM und dem Data Marketplace.   


## Fazit
Masked Authenticated Messaging ist bereits heute einsatzbereit und bietet die Möglichkeit Daten verschlüsselt und manipulationssicher auf dem Tangle zu hinterlegen sowie diese Daten mit ausgewählten Nutzern zu teilen. Neben Bosch und Fujitsu entdecken mehr und mehr Unternehmen die sich bietenden Möglichkeiten und entwickeln Lösungen auf Basis von MAM. Momentan wird an der Folgeversion MAM 1.1 gearbeitet, welche laut Roadmap der IOTA Foundation Mitte dieses Jahres fertiggestellt werden soll. 
