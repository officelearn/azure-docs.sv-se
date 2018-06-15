---
title: Felsöka för felaktig inkommande händelser i Azure Stream Analytics | Microsoft Docs
description: Hur vet jag vilka händelser i min inkommande data som orsakar problem i ett Stream Analytics-jobb
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766400"
---
# <a name="troubleshoot-for-malformed-input-events"></a>Felsöka för felaktig inkommande händelser

När Indataströmmen för Stream Analytics-jobbet innehåller felaktigt utformade meddelanden, gör serialisering problem. Felaktigt utformade meddelanden innehåller felaktig serialisering, till exempel parentes saknas i en JSON-objekt eller felaktig Tidsstämpelformat. När en Stream Analytics-jobbet tar emot ett felaktigt meddelande, släpper meddelandet och meddelar användare med en varning. Symbolen varning visas på den **indata** för Stream Analytics-jobbet:

![Indata sida vid sida](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Felsökningssteg

1. Gå till panelen inkommande och klicka om du vill visa varningar.
2. Panelen inkommande information visar en uppsättning varningar med information om problemet. Följande är ett exempel varning, varningsmeddelandet visas Partition, förskjutning och sekvensnummer där det finns felaktiga JSON-data. 

   ![Varningsmeddelande visas med förskjutning](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Kör följande kodfragment för att hämta JSON-data som har fel format. Den här kodblock läser partitions-Id, förskjutning och skriver ut data. Du kan hämta i det fullständiga exemplet från den [GitHub exempel databasen](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). När du läser data kan du analysera och korrigera serialiseringsformat.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Nästa steg

* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
