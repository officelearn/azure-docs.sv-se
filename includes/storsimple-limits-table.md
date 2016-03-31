<!--author=alkohli last changed: 12/15/15-->

| Grenzwertbezeichner | Begrenzung | Kommentare |
|----------------- | ------|--------- |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto | 64 | |
| Maximale Anzahl von Volumecontainern | 64 | |
| Maximale Anzahl von Volumes | 255 | |
| Maximale Anzahl von Zeitplänen pro Bandbreitenvorlage | 168 | Einen Zeitplan für jede Stunde, jeden Tag der Woche (24 * 7). |
| Maximale Größe eines mehrstufigen Volumes auf physischen Geräten | 64 TB für 8100 und 8600 | 8100 und 8600 sind physische Geräte. |
| Maximale Größe eines mehrstufigen Volumes auf virtuellen Geräten in Azure | 30 TB für 8010 <br></br> 64 TB für 8020 | 8010 und 8020 sind virtuelle Geräte in Azure, die Standardspeicher und Storage Premium verwenden. |
| Maximale Größe eines lokal fixierten Volumes auf physischen Geräten | 10 TB für 8100 <br></br> 25 TB für 8600 | 8100 und 8600 sind physische Geräte. |
| Maximale Anzahl von iSCSI-Verbindungen | 512 | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren | 512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät | 64 | |
| Maximale Anzahl von Volumes pro Sicherungsrichtlinie | 24 | |
| Maximale Anzahl von Sicherungen, die pro Sicherungsrichtlinie beibehalten werden | 64 | |
| Maximale Anzahl von Zeitplänen pro Sicherungsrichtlinie | 10 | |
| Maximale Anzahl von Momentaufnahmen beliebigen Typs, die pro Volume beibehalten werden können | 256 | Dies beinhaltet lokale Momentaufnahmen und Cloud-Momentaufnahmen. |
| Maximale Anzahl von Momentaufnahmen, die in einem Gerät vorhanden sein können | 10.000 | |
| Maximale Anzahl von Volumes, die für Sicherung, Wiederherstellung oder Klonen parallel verarbeitet werden können | 16 |<ul><li>Wenn mehr als 16 Volumes vorhanden sind, werden diese sequenziell verarbeitet werden, wenn verarbeitungsslots verfügbar werden.</li><li>Neue Sicherungen eines geklonten oder wiederhergestellten Volumes mehrstufigen kann nicht auftreten, bis der Vorgang abgeschlossen ist. Allerdings sind für ein lokales Volume, Sicherungen zulässig nachdem das Volume online ist.</li></ul>|
| Wiederherstellungszeit für das Wiederherstellen und Klonen mehrstufiger Volumes | < 2 Minuten | <ul><li>Das Volume wird innerhalb von zwei Minuten wiederherstellen oder Klonen Vorgang unabhängig von der Datenträgergröße zur Verfügung gestellt.</li><li>Die Volume-Leistung möglicherweise Anfangs langsamer als normal, da die meisten Daten und Metadaten noch in der Cloud befinden. Leistung kann steigen, wie Daten aus der Cloud zum StorSimple-Gerät übertragen.</li><li>Die Gesamtzeit zum Herunterladen der Metadaten hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite zur Cloud beeinflusst werden.</li><li>Die Wiederherstellung oder Klonvorgang ist abgeschlossen, wenn alle Metadaten auf dem Gerät.</li><li>Sicherungsvorgänge können nicht ausgeführt werden, bis die Wiederherstellung oder Klonen vollständig abgeschlossen ist.|
| Wiederherstellungszeit für das Wiederherstellen lokal fixierter Volumes | < 2 Minuten | <ul><li>Das Volume wird innerhalb von zwei Minuten der Restore-Vorgang unabhängig von der Datenträgergröße zur Verfügung gestellt.</li><li>Die Volume-Leistung möglicherweise Anfangs langsamer als normal, da die meisten Daten und Metadaten noch in der Cloud befinden. Leistung kann steigen, wie Daten aus der Cloud zum StorSimple-Gerät übertragen.</li><li>Die Gesamtzeit zum Herunterladen der Metadaten hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite zur Cloud beeinflusst werden.</li><li>Im Gegensatz zu Volumes in Ebenen, bei lokal angeheftete Volumes werden der Volume-Daten lokal auf dem Gerät heruntergeladen. Die Wiederherstellung ist abgeschlossen, wenn alle Daten des Datenträgers auf dem Gerät gebracht worden.</li><li>Die Wiederherstellungsvorgänge länger sein können, und die Gesamtzeit zum Abschließen des Wiederherstellungsvorgangs hängt von der Größe der bereitgestellten lokalen Volumes, die Internetbandbreite und die vorhandenen Daten auf dem Gerät. Sicherungsvorgänge auf lokal fixierten Volumes sind zulässig, während die Wiederherstellung ausgeführt wird.|
| Thin-Wiederherstellungsverfügbarkeit | Letztes Failover | |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von SSD-Ebene aus bereitgestellt)* | 920/720 MB/s mit einer einzelnen 10-GbE-Netzwerkschnittstelle | Bis zu 2x mit MPIO und zwei Netzwerkschnittstellen. |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von HDD-Ebene aus bereitgestellt)* | 120/250 MB/s |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von Cloud-Ebene aus bereitgestellt)* | 11/41 MB/s | Lesedurchsatz hängt von Clients ab, die genügend E/A-Warteschlangentiefe generieren und verwalten müssen. |

& #42; Der maximale Durchsatz pro E/A-Typ wurde mit 100 %-Lese- und 100 %-Schreibszenarien gemessen. Der tatsächliche Durchsatz kann geringer sein und hängt von der E/A-Mischung und den Netzwerkbedingungen ab.


