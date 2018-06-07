---
title: Riktlinjer för personliga data som lagras i Azure Application Insights | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar personliga data som lagras i Azure Application Insights och metoder för att identifiera och ta bort den.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: Evgeny.Ternovsky;mbullwin
ms.openlocfilehash: 1f9f7d53fc09111e0060c934e3869326fcaadb7e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655362"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Riktlinjer för personliga data som lagras i Application Insights

Application Insights är ett datalager där personliga data är sannolikt kommer att finnas. Den här artikeln beskrivs där i Application Insights dessa data är vanligtvis finns, samt funktionerna som är tillgängliga för dig att hantera dessa data.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategi för hantering av personliga data

När det kommer att vara upp till du och ditt företag slutänden att avgöra strategin som du hanterar din privata data (om någon), följer några möjliga metoder. De visas i prioritetsordning ur en teknisk synvinkel från mest till minst bättre:
* Om möjligt stoppa samling obfuscate, anonymisera eller annars Justera data som samlas in om du vill exkludera från anses _privata_. Den här metoden är att föredra sparar behovet av att skapa en kostsam och effektfulla datahantering strategi.
* Om inte möjligt försök att normalisera data för att minska påverkan på dataplattform och prestanda. Till exempel skapa en sökning att data som kommer att korrelera användarnamnet och deras information till ett internt ID som sedan kan loggas på en annan plats i stället för att logga en explicit användar-ID. På så sätt kan om en av dina användare att be dig att ta bort deras egen personliga information, är det möjligt att endast borttagning av raden i uppslagstabellen motsvarar användaren kommer att tillräckligt. 
* Slutligen privata måste samlas in, skapar du en process runt Rensa API sökvägen och befintlig fråga API sökväg att uppfylla eventuella skyldigheter som du kan ha runt exportera och ta bort alla privata data som är kopplade till en användare.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Var ska leta efter privata data i Application Insights?

Application Insights är en helt flexibla store, som vid att ett schema till din data, kan du åsidosätta alla fält med anpassade värden. Därför är det omöjligt att säga exakt där privata data kommer att hittas i ett visst program. Följande platser, men är bra startpunkter i lagret:

* *IP-adresser*: medan Application Insights kommer som standard obfuscate alla fält för IP-adress till ”0.0.0.0”, är det ett relativt vanligt mönster att åsidosätta det här värdet med faktiska användar-IP-adress för att underhålla sessionsinformation. Analytics-fråga nedan kan användas för att hitta någon tabell som innehåller värdena i kolumnen IP-adress än ”0.0.0.0” under de senaste 24 timmarna:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Användar-ID*: som standard Application Insights använder slumpmässigt genererat ID: N för användare och sessionsspårning. Det är vanligt att fälten åsidosättas för att lagra ett ID som är mer relevant för programmet. Till exempel: användarnamn, AAD GUID, osv. Dessa ID: N ofta anses vara scope som personliga data, och därför ska hanteras på rätt sätt. Vår rekommendation är alltid att försöka obfuscate eller anonymisera dessa ID: N. Fält där värdena ofta hittades inkluderar session_Id, user_Id, user_AuthenticatedId, user_AccountId samt customDimensions.
* *Anpassade data*: Application Insights gör att du kan lägga till en uppsättning anpassade dimensioner till en datatyp. Dessa dimensioner kan vara *alla* data. Använd följande fråga för att identifiera eventuella anpassade dimensioner som samlas in under de senaste 24 timmarna:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data i minnet och under överföring*: Application Insights spårar undantag, begäranden, beroendeanrop och spår. Privata ofta samlas in på koden och HTTP-nivå. Granska de undantag, begäranden, beroenden och spårningar tabeller för att identifiera sådana data. Använd [telemetri initierare](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) där det är möjligt att obfuscate dessa data.
* *Ögonblicksbild felsökare insamlingar*: den [ögonblicksbild felsökare](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funktion i Application Insights kan du samla in debug-ögonblicksbilder när ett undantagsfel i produktion instans av programmet. Ögonblicksbilder ska visa fullständig stack-spårning som leder till undantagen som värden för lokala variabler i varje steg i stacken. Tyvärr tillåter inte den här funktionen för selektiv radering av snapin-punkter eller programmatisk åtkomst till data i ögonblicksbilden. Om standardintervallet för kvarhållning av ögonblicksbilder inte uppfyller dina krav på efterlevnad därför vi rekommenderar att inaktivera funktionen.

## <a name="how-to-export-and-delete-private-data"></a>Exportera och ta bort personliga data

Det är __starkt__ rekommenderas när det är möjligt att omstrukturera din princip för datainsamling för att inaktivera insamling av privata data, obfuscating eller anonymizing, eller på annat sätt att ändra den om du vill ta bort den från betraktas som ”privat”. Hanterar data när den har samlats in, leder till kostnader som du och ditt team att definiera och automatisera en strategi, skapa ett gränssnitt för kunderna att interagera med sina data via och löpande underhållskostnaderna. Dessutom är det beräkningsmässigt kostsamma för Application Insights och ett stort antal samtidiga frågan eller rensa API-anrop har möjlighet att påverka alla andra interaktion med Application Insights-funktioner. Som rapporterade att verkligen vissa giltig scenarier där privata måste samlas in. I dessa fall bör data hanteras enligt beskrivningen i det här avsnittet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visa och exportera

För både visa och exportera data begär den [frågan API](https://dev.applicationinsights.io/quickstart) ska användas. Logik för att konvertera formen av data till ett lämpligt alternativ för att leverera till användarna kommer att vara upp till du implementera. [Azure Functions](https://azure.microsoft.com/services/functions/) är det bra att vara värd för denna logik.

### <a name="delete"></a>Ta bort

> [!WARNING]
> Tar bort i Application Insights är skadliga och icke-reversibelt! Kontrollera mycket försiktig sina körning.

Vi har gjort tillgängliga som en del av en sekretess hantering artikel en ”Rensa” API-sökväg. Den här sökvägen bör användas sparsamt på grund av riskerna i samband med att göra det, potentiell prestandapåverkan och kan ge skeva upp alla aggregeringar, mått och andra aspekter av Application Insights-data. Finns det [strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) avsnittet ovan för alternativa metoder att hantera privata data.

Rensa är mycket Privilegierade att inga app eller användare i Azure (inklusive även resursägaren) har behörighet att köra utan att uttryckligen beviljas en roll i Azure Resource Manager. Den här rollen är _Data Purger_ och bör delegeras noggrant på grund av risken för dataförluster.

När rollen Azure Resource Manager har tilldelats två nya API-sökvägar är tillgängliga, fullständig utvecklardokumentationen och anropa form länkade:

* [Rensa POST](https://docs.microsoft.com/rest/api/application-insights/components/purge) – tar ett objekt som anger parametrarna för data att ta bort och returnerar en referens GUID
* Rensa status för GET - POST Rensa anropet returneras ett 'x-ms-status-location-huvud som innehåller en URL som du kan anropa för att avgöra status för Rensa-API. Exempel:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

När det stora flertalet av Rensa operations kan slutföra mycket snabbare än SLA på grund av deras tunga inverkan på dataplattform som används av Application Insights, en formell SLA för att slutföra Rensa operations är inställt på 30 dagar.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur data som samlas in, bearbetas och skyddas i [Application Insights datasäkerhet](app-insights-data-retention-privacy.md).