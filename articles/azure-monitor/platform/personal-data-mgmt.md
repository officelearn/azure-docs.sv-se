---
title: Vägledning för personliga data som lagras i Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar personliga data som lagras i Azure Log Analytics och metoderna för att identifiera och ta bort den.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 6254a32349a0f7c743c3fb4993080ca3437c8276
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810278"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Vägledning för personliga data som lagras i Log Analytics och Application Insights

Log Analytics är ett datalager där personliga data är sannolikt kommer att finnas. Application Insights lagras data i en Log Analytics-partition. Den här artikeln diskuterar där i Log Analytics och Application Insights sådana data ofta finns, samt funktioner som är tillgängliga för dig att hantera sådana data.

> [!NOTE]
> För den här artikeln _logga data över_ refererar till data som skickas till en Log Analytics-arbetsyta, medan _programdata_ refererar till data som samlas in av Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategi för hantering av personuppgifter

När det kommer att vara upp till dig och ditt företag slutänden att avgöra vilken strategi som du hanterar dina privata data (om alls), följer några strategier som möjligt. De visas i prioritetsordning från en teknisk synvinkel från mest till minst bättre:

* Om möjligt, stoppa insamling av, Förvräng, maskera eller justera de data som samlas in för att undanta den från ses som ”privat”. Det här är _överlägset_ önskad metod som gör att du sparar behovet av att skapa en hantera strategi dyrt och påverkar data.
* Om möjligt, försöka att normalisera data för att minska påverkan på dataplattform och prestanda. I stället för att logga en explicit användar-ID kan du till exempel skapa en lookup-data som kommer korrelera användarnamnet och deras information till ett internt ID som sedan kan loggas någon annanstans. På så sätt kan bör en av dina användare att be dig att ta bort sin personliga information, är det möjligt att endast borttagning av raden i uppslagstabellen för användaren är tillräckligt. 
* Slutligen privata data måste samlas in, skapar du en process runt Rensa API sökvägen och befintlig fråga API sökväg för att uppfylla alla skyldigheter som du kan ha runt exportera och ta bort alla privata data som hör till en användare. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Var ska se ut för privata data i Log Analytics?

Log Analytics är en flexibel store, som vid datavetenskapsmetod som fastställer ett schema till dina data, kan du åsidosätta varje fält med anpassade värden. Dessutom kan alla anpassade schemaobjekt matas in. Därför är det omöjligt att säga exakt där personliga data finns i din specifika arbetsyta. Följande platser, men är bra startpunkter i inventeringen:

### <a name="log-data"></a>Loggdata

* *IP-adresser*: Log Analytics samlar in en mängd olika IP-information i många olika tabeller. Följande fråga visar till exempel alla tabeller där IPv4-adresser har samlats in under de senaste 24 timmarna:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Användar-ID*: Användar-ID finns i en mängd olika lösningar och tabeller. Du kan söka efter ett visst användarnamn över hela datauppsättningen med hjälp av kommandot search:
    ```
    search "[username goes here]"
    ```
Kom ihåg att se ut för läsbara användarnamn men GUID som kan spåras tillbaka direkt till en viss användare!
* *Enhets-ID*: Som användar-ID anses ibland enhets-ID ”privat”. Använd samma metod som anges ovan för användar-ID för att identifiera tabeller där det kan vara ett problem. 
* *Anpassade data*: Log Analytics möjliggör insamling i flera olika sätt: anpassade loggar och anpassade fält, de [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) , och anpassade data som samlas in som en del av systemets händelseloggar. Alla dessa kan vara utsatta för som innehåller privata data och bör undersökas för att kontrollera om några sådana data finns.
* *Lösningen insamlade data*: Eftersom mekanismen för lösningen är en kunskapsuppsättning, rekommenderar vi att granska alla tabeller som genererats av lösningar för att säkerställa att.

### <a name="application-data"></a>Programdata

* *IP-adresser*: Application Insights kommer som standard Förvräng alla fält för IP-adress att ”0.0.0.0”, men det är ett relativt vanligt mönster för att åsidosätta det här värdet med faktiska användar-IP-adress för att upprätthålla sessionsinformation. Analytics-frågan nedan kan användas för att hitta någon tabell som innehåller värdena i kolumnen IP-adress än ”0.0.0.0” under de senaste 24 timmarna:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Användar-ID*: Som standard använder Application Insights slumpmässigt genererat ID: N för användar- och sessionsspårning. Det är dock vanligt att de här fälten åsidosätts för att lagra ett ID som är mer relevant för programmet. Till exempel: användarnamn, AAD-GUID, osv. Dessa ID: N ofta anses omfattade som personliga data, och därför bör hanteras på rätt sätt. Vår rekommendation är alltid att försöka Förvräng eller maskera dessa ID: N. Fält där dessa värden finns vanligtvis är session_Id, user_Id, user_AuthenticatedId, user_AccountId samt customDimensions.
* *Anpassade data*: Application Insights kan du lägga till en uppsättning av anpassade dimensioner till någon datatyp av. Dessa dimensioner kan vara *alla* data. Använd följande fråga för att identifiera eventuella anpassade dimensioner som samlas in under de senaste 24 timmarna:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data i minnet och under överföring*: Application Insights spårar undantag, begäranden, beroendeanrop och spårningar. Privata data kan ofta samlas på koden och HTTP-anrop nivå. Granska undantag, begäranden, beroenden och spårningar tabeller för att identifiera sådana data. Använd [telemetri-initierare](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) där det är möjligt att Förvräng dessa data.
* *Snapshot Debugger insamlingar*: Den [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funktion i Application Insights kan du samla in ögonblicksbilder för felsökning när ett undantag har inträffat på instansen av ditt program. Ögonblicksbilder utsätter den fullständiga stackspårningen som leder till undantagen som värden för lokala variabler vid varje steg i stacken. Tyvärr tillåter inte den här funktionen för selektiv radering av fästpunkter eller programmatisk åtkomst till data i ögonblicksbilden. Om frekvensen för standard ögonblicksbilder kvarhållning inte uppfyller efterlevnadskraven, därför rekommenderar vi att stänga av funktionen.

## <a name="how-to-export-and-delete-private-data"></a>Exportera och ta bort privata data

Som vi nämnde i den [strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) ovan, är det __starkt__ rekommenderas om den allt möjligt, för att omstrukturera din princip för insamling av data om du vill inaktivera insamling av privata data, dölja eller maskera det eller på annat sätt modifiera den för att ta bort den från ses som ”privat”. Hantering av data kommer främsta resultera i kostnader för dig och ditt team att definiera och automatisera en strategi, skapa ett gränssnitt för kunderna att interagera med sina data via och löpande underhållskostnader. Dessutom är det beräkningsmässigt dyra för Log Analytics och Application Insights och ett stort antal samtidiga fråga eller rensa API-anrop har möjlighet att påverka all interaktion med Log Analytics-funktioner. Som SA, det finns faktiskt fanns några giltiga scenarier där privata data måste samlas in. De här fallen, bör data hanteras enligt beskrivningen i det här avsnittet.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visa och exportera

För både visa och exportera databegäranden, den [Log Analytics-fråga API: N](https://dev.loganalytics.io/) eller [Application Insights fråga API: N](https://dev.applicationinsights.io/quickstart) ska användas. Logik för att omvandla formen av data till ett lämpligt alternativ för att förse dina användare kommer att upp till dig att implementera. [Azure Functions](https://azure.microsoft.com/services/functions/) gör ett bra ställe att vara värd för sådana logik.

### <a name="delete"></a>Ta bort

> [!WARNING]
> Tar bort i Log Analytics är destruktiva och icke-reversibelt! Du vara mycket försiktig i sina körning.

Vi har gjort tillgänglig som en del av en sekretess hantering av en *Rensa* API-sökväg. Den här sökvägen bör användas sparsamt på grund av riskerna med att göra det, potentiell prestandapåverkan och kan ge skeva upp alla aggregeringar, mått och andra aspekter av din Log Analytics-data. Se den [strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) för alternativa metoder för att hantera privata data.

Rensa är en mycket Privilegierade åtgärd att ingen app eller användare i Azure (inklusive även resursägaren) har behörighet att köra utan att uttryckligen beviljas en roll i Azure Resource Manager. Den här rollen är _Data Purger_ och bör delegeras försiktighet på grund av risken för dataförlust. 

När de har tilldelats rollen Azure Resource Manager finns två nya API-sökvägar: 

#### <a name="log-data"></a>Loggdata

* [Rensa efter](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) – tar ett objekt som att ange parametrar för att ta bort och returnerar en referens-GUID 
* Rensa status för GET - INLÄGG Rensa anropet returnerar en x-ms-status-location-rubriken som innehåller en URL som du kan anropa för att fastställa statusen för din Rensa API. Exempel:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Medan vi förväntar oss merparten av Rensa åtgärder för att slutföra mycket snabbare än serviceavtal på grund av deras stor inverkan på dataplattform som används av Log Analytics, **en formell serviceavtal (SLA) för att slutföra åtgärder för rensning har ställts in till 30 dagar**. 

#### <a name="application-data"></a>Programdata

* [Rensa efter](https://docs.microsoft.com/rest/api/application-insights/components/purge) – tar ett objekt som att ange parametrar för att ta bort och returnerar en referens-GUID
* Rensa status för GET - INLÄGG Rensa anropet returnerar en x-ms-status-location-rubriken som innehåller en URL som du kan anropa för att fastställa statusen för din Rensa API. Exempel:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Även om merparten av Rensa åtgärder kan slutföra mycket snabbare än serviceavtalet, på grund av deras stor inverkan på dataplattform som används av Application Insights **en formell serviceavtal (SLA) för att slutföra åtgärder för rensning har ställts in till 30 dagar**.

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur Log Analytics-data som samlas in, bearbetas och skyddas i [Log Analytics-datasäkerhet](../../azure-monitor/platform/data-security.md).
- Läs mer om hur Application Insights-data som samlas in, bearbetas och skyddas i [Application Insights datasäkerhet](../../azure-monitor/app/data-retention-privacy.md).
