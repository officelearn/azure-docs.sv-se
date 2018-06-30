---
title: Riktlinjer för personliga data som lagras i Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar personliga data som lagras i Azure Log Analytics och metoder för att identifiera och ta bort den.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3692c83a4991fc67ec176687bd076ab14e4c640d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129378"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Riktlinjer för personliga data som lagras i logganalys

Log Analytics är ett datalager där personliga data är sannolikt kommer att finnas. Den här artikeln handlar om där logganalys sådana data är vanligtvis finns, samt funktionerna som är tillgängliga för dig att hantera dessa data.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategi för hantering av personliga data

När det kommer att vara upp till du och ditt företag slutänden att avgöra strategin som du hanterar din privata data (om någon), följer några möjliga metoder. De visas i prioritetsordning ur en teknisk synvinkel från mest till minst bättre:

* Om möjligt stoppa samling, obfuscate anonymisera eller annars Justera data som samlas in om du vill exkludera från betraktas som ”privat”. Detta är _allra_ att föredra sparar behovet av att skapa en mycket kostsam och effektfulla datahantering strategi.
* Om inte möjligt försök att normalisera data för att minska påverkan på dataplattform och prestanda. Till exempel i stället för att logga en explicit användar-ID, skapa en sökning-data som ska matcha användarnamnet och deras information till ett internt ID som sedan kan loggas på en annan plats. På så sätt kan bör en av dina användare att be dig att ta bort deras egen personliga information, är det möjligt att endast borttagning av raden i uppslagstabellen motsvarar användaren kommer att tillräckligt. 
* Slutligen privata måste samlas in, skapar du en process runt Rensa API sökvägen och befintlig fråga API sökväg att uppfylla eventuella skyldigheter som du kan ha runt exportera och ta bort alla privata data som är kopplade till en användare. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Var ska leta efter privata data i Log Analytics?

Log Analytics är en flexibel store, som vid att ett schema till din data, kan du åsidosätta alla fält med anpassade värden. Dessutom kan ett anpassat schema inhämtas. Därför är det omöjligt att säga exakt där privata data kommer att hittas i din specifika arbetsyta. Följande platser, men är bra startpunkter i lagret:

* *IP-adresser*: logganalys samlar in en mängd olika IP-information mellan många olika tabeller. Följande fråga visar till exempel alla tabeller där IPv4-adresser har samlats in under de senaste 24 timmarna:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Användar-ID*: användar-ID finns i en mängd olika lösningar och tabeller. Du kan söka efter ett visst användarnamn över hela datamängden med hjälp av kommandot sökning:
    ```
    search "[username goes here]"
    ```
Kom ihåg att se ut för läsbart användarnamn men GUID som kan spåras tillbaka direkt till en viss användare!
* *Enhets-ID*: som användar-ID, enhets-ID ibland betraktas som ”privat”. Använd samma metod som anges ovan för användar-ID för att identifiera tabeller där detta kan bero på ett problem. 
* *Anpassade data*: logganalys möjliggör insamling i flera olika metoder: anpassade loggar och anpassade fält i [HTTP Data Collector API: et](log-analytics-data-collector-api.md) , och anpassade data samlas in som en del av systemhändelseloggarna. Alla dessa är sårbara för som innehåller privata data och bör granskas för att kontrollera om det finns sådana data.
* *Lösning insamlade data*: eftersom mekanismen för lösningen är en öppen, rekommenderar vi att granska alla tabeller som genererats av lösningar för att säkerställa kompatibilitet.

## <a name="how-to-export-and-delete-private-data"></a>Exportera och ta bort personliga data

Som anges i den [strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) ovan, är det __starkt__ rekommenderade som om alla möjligt att omstrukturera din princip för datainsamling för att inaktivera insamling av personliga data, obfuscating anonymizing det eller på annat sätt att ändra det om du vill ta bort den från betraktas som ”privat”. Hanterar data kommer främsta resultera i att du och ditt team att definiera och automatisera en strategi, skapa ett gränssnitt för kunderna att interagera med sina data via, och löpande underhållskostnader. Dessutom är det beräkningsmässigt kostsamma för Log Analytics och ett stort antal samtidiga frågan eller rensa API-anrop har möjlighet att påverka alla andra interaktion med logganalys-funktioner. Som rapporterade att verkligen vissa giltig scenarier där privata måste samlas in. I dessa fall bör data hanteras enligt beskrivningen i det här avsnittet.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visa och exportera

För både visa och exportera data begär den [frågan API](https://dev.loganalytics.io/) ska användas. Logik för att konvertera formen av data till ett lämpligt alternativ för att leverera till användarna kommer att vara upp till du implementera. [Azure Functions](https://azure.microsoft.com/services/functions/) gör det bra att vara värd för denna logik.

### <a name="delete"></a>Ta bort

> [!WARNING]
> Tar bort i logganalys är skadliga och icke-reversibelt! Kontrollera mycket försiktig sina körning.

Vi har gjort tillgängliga som en del av en sekretess hantering en *Rensa* API-sökväg. Den här sökvägen bör användas sparsamt på grund av riskerna i samband med att göra det, potentiell prestandapåverkan och kan ge skeva upp alla aggregeringar, mått och andra aspekter av Log Analytics-data. Finns det [strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) avsnittet alternativa metoder att hantera privata data.

Rensa är mycket Privilegierade att inga app eller användare i Azure (inklusive även resursägaren) har behörighet att köra utan att uttryckligen beviljas en roll i Azure Resource Manager. Den här rollen är _Data Purger_ och bör delegeras försiktigt på grund av risken för dataförluster. 

När rollen Azure Resource Manager har tilldelats två nya API-sökvägar som är tillgängliga: 

* [Efter Rensa] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – tar ett objekt som anger parametrarna för data att ta bort och returnerar en referens GUID 
* Rensa status för GET - POST Rensa anropet returneras ett 'x-ms-status-location-huvud som innehåller en URL som du kan anropa för att avgöra status för Rensa-API. Exempel:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Medan vi räknar om majoriteten av Rensa åtgärder att slutföra mycket snabbare än våra SLA på grund av deras tunga inverkan på dataplattform som används av Log Analytics, en formell SLA för att slutföra Rensa operations är inställt på 30 dagar. 

## <a name="next-steps"></a>Nästa steg
Läs mer om hur data som samlas in, bearbetas och skyddas i [logganalys datasäkerhet](log-analytics-data-security.md).