Die folgende Tabelle listet die Grenzwerte der verschiedenen Dienstebenen (S1, S2, F1) auf. Informationen zu den Kosten der einzelnen *Einheit* auf jeder Ebene finden Sie unter [IoT Hub Preise](http://azure.microsoft.com/pricing/details/iot-hub/).

| Ressource | S1 Standard | S2 Standard | F1 Free |
| -------- | ----------- | ----------- | ------- |
| Geräte/Einheit | 500 | 500 | 10 |
| Nachrichten/Tag | 50.000 | 1,500,000 | 3,000 |
| Maximale Anzahl der Einheiten | 200 | 200 | 1 |
| Updates für das Gerät (erstellen, aktualisieren <br/> Löschen Sie) pro Einheit pro Tag | 1100 | 1100 | 1100 |

> [AZURE.NOTE] Wenn Sie einen S1 oder S2-Tier-Hub mit mehr als 200 Einheiten erwarten, wenden Sie sich an Microsoft Support Services.

Die folgende Tabelle enthält die für IoT Hub-Ressourcen geltenden Grenzwerte:

| Ressource | Begrenzung |
| -------- | ----- |
| Maximale Anzahl von IoT Hubs pro Azure-Abonnement | 10 |
| Maximale Anzahl der Geräte-Identitäten<br/>  in einem einzigen Aufruf zurückgegeben wird. | 1.000 |
| Maximale Beibehaltungsdauer von IoT Hub-Nachrichten | 7 Tage |
| Maximale Größe einer Nachricht von einem Gerät an die Cloud | 256 KB |
| Maximale Größe eines Batches, das vom Gerät an die Cloud gesendet wird | 256 KB |
| Maximale Anzahl von Nachrichten im Batch, das vom Gerät an die Cloud gesendet wird | 500 |
| Maximale Größe einer Nachricht von der Cloud an das Gerät | 64 KB |
| Maximale Gültigkeitsdauer von Nachrichten von der Cloud an das Gerät | 2 Tage |
| Maximale Übermittlungsanzahl für Cloud-zu-Gerät <br/> messages | 100 |
| Maximale Übermittlungsanzahl für Feedback-Nachrichten <br/> in der Antwort auf eine Cloud-zu-Gerät | 100 |
| Maximale Gültigkeitsdauer für Nachrichten feedback <br/> Antwort auf eine Cloud-zu-Gerät | 2 Tage |

Der IoT Hub-Dienst drosselt Anforderungen, wenn die folgenden Kontingente überschritten werden:

| Drosselung | Wert pro Hub |
| -------- | ------------- |
| Identitätsregistrierungsvorgänge <br/> (erstellen, abrufen, auflisten, aktualisieren und löschen), <br/> einzelne oder Bulk Import/export | 100/Minute/Einheit, bis zu 5.000/Minute |
| Geräteverbindungen | 100/Sekunde/Einheit |
| Senden von Nachrichten von Geräten an die Cloud | 2000/min/Einheit (S2), 60/min/Einheit (S1) <br/> Mindestens 100/Sek. |
| Cloud-zu-Gerät-Vorgänge <br/> (sendet, empfängt, Feedback) | 100/Minute/Einheit |


