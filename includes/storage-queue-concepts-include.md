## Vad är Queue Storage?
Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

Vanliga användningsområden för Queue Storage är:

* Skapa en lista med kvarvarande uppgifter att bearbeta asynkront
* Skicka meddelanden från en Azure-webbroll till en Azure-arbetsroll

## Kötjänst-koncept
Kötjänsten består av följande komponenter:

![Kö1](./media/storage-queue-concepts-include/queue1.png)

* **URL-format:** köer är adresserbara via följande URL-format:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Följande URL adresserar en kö i diagrammet:  
  
        http://myaccount.queue.core.windows.net/images-to-download
* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/storage-scalability-targets.md) för information om kapacitet för lagringskonton.
* **Kö:** en kö innehåller en uppsättning meddelanden. Alla meddelanden måste vara i en kö. Observera att könamnet måste vara helt i gemener. Mer information om namngivning av köer finns i [namngivning av köer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Meddelande:** ett meddelande i valfritt format, som är upp till 64 KB. Den maximala tid som ett meddelande kan finnas i kön är 7 dagar.

<!--HONumber=Jun16_HO2-->


