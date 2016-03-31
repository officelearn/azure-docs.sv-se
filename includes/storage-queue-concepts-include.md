## Was ist der Warteschlangenspeicher?

Der Azure-Warteschlangenspeicher ist ein Dienst zur Speicherung großer Mengen von
Nachrichten, auf die von überall auf der Welt aus
über authentifizierte Aufrufe über HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann
bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten – bis zur
Beschränkung der Gesamtkapazität des Speicherkontos. Ein Speicherkonto kann bis zu 500 TB Blob-, Warteschlangen- und Tabellendaten enthalten. Finden Sie unter [Azure Storage-Skalierbarkeit und Leistungsziele](http://msdn.microsoft.com/library/azure/dn249410.aspx) Details zur speicherkontokapazität.

Warteschlangenspeicherungen werden hauptsächlich für folgende Zwecke verwendet:

-   Erstellung eines Arbeits-Backlogs zur asynchronen Verarbeitung
-   Weiterleitung von Nachrichten von einer Azure-Webrolle an eine Azure-Workerrolle

## Konzepte des Warteschlangendiensts

Der Warteschlangendienst umfasst die folgenden Komponenten:

![Queue1](./media/storage-queue-concepts-include/queue1.png)


- **URL-Format:** Warteschlangen sind über die folgende URL-Format adressierbar:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
    Mit der folgenden URL kann eine der Warteschlangen im Diagramm adressiert werden:  
        
        http://myaccount.queue.core.windows.net/imagesToDownload

- **Speicherkonto:** alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Finden Sie unter [Azure Storage-Skalierbarkeit und Leistungsziele](../articles/storage/storage-scalability-targets.md) Details zur speicherkontokapazität.

- **Warteschlange:** eine Warteschlange enthält einen Satz von Nachrichten. Alle Nachrichten müssen sich in Warteschlangen befinden.

- **Nachricht:** eine Nachricht in einem beliebigen Format und von bis zu 64KB.


