---
title: Vägledning för personuppgifter som lagras i Azure Log Analytics| Microsoft-dokument
description: I den här artikeln beskrivs hur du hanterar personuppgifter som lagras i Azure Log Analytics och metoder för att identifiera och ta bort dem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: a720627e1783d2e29ef180b7855132ea59444cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248754"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Vägledning för personuppgifter som lagras i Logganalys- och application insights

Log Analytics är ett datalager där personuppgifter sannolikt kommer att hittas. Application Insights lagrar sina data i en Log Analytics-partition. I den här artikeln beskrivs var sådana data vanligtvis finns i Logganalys- och application insights, liksom de funktioner som är tillgängliga för dig för att hantera sådana data.

> [!NOTE]
> I den här artikeln refererar _loggdata_ till data som skickas till en Log Analytics-arbetsyta, medan _programdata_ refererar till data som samlas in av Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategi för hantering av personuppgifter

Även om det kommer att vara upp till dig och ditt företag att i slutändan bestämma den strategi som du kommer att hantera dina privata data (om alls), följande är några möjliga metoder. De är listade i prioritetsordning ur teknisk synvinkel från de flesta till minst att föredra:

* Om möjligt, stoppa insamling av, fördunkla, anonymisera eller på annat sätt justera de uppgifter som samlas in för att utesluta den från att betraktas som "privat". Detta är den _absolut_ föredragna metoden, vilket sparar behovet av att skapa en mycket kostsam och effektfull datahanteringsstrategi.
* Om det inte är möjligt, försök att normalisera data för att minska påverkan på dataplattformen och prestanda. I stället för att till exempel logga ett explicit användar-ID skapar du en uppslagsdata som korrelerar användarnamnet och deras information till ett internt ID som sedan kan loggas någon annanstans. På så sätt, om en av dina användare ber dig att ta bort deras personliga information, är det möjligt att endast ta bort raden i uppslagstabellen som motsvarar användaren kommer att vara tillräckligt. 
* Slutligen, om privata data måste samlas in, skapa en process runt rensning API-sökvägen och den befintliga frågan API-sökvägen för att uppfylla eventuella skyldigheter du kan ha runt exportera och ta bort privata data som är associerade med en användare. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Var letar du efter privata data i Log Analytics?

Log Analytics är ett flexibelt arkiv som, samtidigt som du skriver ett schema till dina data, gör att du kan åsidosätta varje fält med anpassade värden. Dessutom kan alla anpassade scheman intas. Därför är det omöjligt att säga exakt var privata data kommer att hittas på din specifika arbetsyta. Följande platser är dock bra utgångspunkter i ditt lager:

### <a name="log-data"></a>Loggdata

* *IP-adresser:* Log Analytics samlar in en mängd olika IP-information i många olika tabeller. Följande fråga visar till exempel alla tabeller där IPv4-adresser har samlats in under de senaste 24 timmarna:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Användar-ID:* Användar-ID finns i en mängd olika lösningar och tabeller. Du kan leta efter ett visst användarnamn i hela datauppsättningen med hjälp av sökkommandot:
    ```
    search "[username goes here]"
    ```
  Kom ihåg att leta inte bara efter människoläsbara användarnamn utan även GUIDs som direkt kan spåras tillbaka till en viss användare!
* *Enhets-ID:* Precis som användar-ID:er betraktas ibland enhets-ID:er som "privata". Använd samma metod som anges ovan för användar-ID:er för att identifiera tabeller där detta kan vara ett problem. 
* *Anpassade data:* Log Analytics tillåter insamling i en mängd olika metoder: anpassade loggar och anpassade fält, [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) och anpassade data som samlas in som en del av systemhändelseloggar. Alla dessa är mottagliga för att innehålla privata uppgifter och bör undersökas för att kontrollera om sådana uppgifter finns.
* *Lösningsinfångade data*: Eftersom lösningsmekanismen är öppen rekommenderar vi att du granskar alla tabeller som genereras av lösningar för att säkerställa efterlevnad.

### <a name="application-data"></a>Programdata

* *IP-adresser:* Medan Application Insights som standard fördunklar alla IP-adressfält till "0.0.0.0", är det ett ganska vanligt mönster att åsidosätta det här värdet med den faktiska användarens IP för att underhålla sessionsinformation. Analytics-frågan nedan kan användas för att hitta alla tabeller som innehåller värden i IP-adresskolumnen förutom "0.0.0.0" under de senaste 24 timmarna:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Användar-ID:* Som standard använder Application Insights slumpmässigt genererade ID:er för användar- och sessionsspårning. Det är dock vanligt att se dessa fält åsidosätts för att lagra ett ID som är mer relevant för programmet. Till exempel: användarnamn, AAD GUIDs, etc. Dessa ID anses ofta vara in-scope som personuppgifter, och bör därför hanteras på lämpligt sätt. Vår rekommendation är alltid att försöka fördunkla eller anonymisera dessa ID. Fält där dessa värden är vanligt förekommande inkluderar session_Id, user_Id, user_AuthenticatedId, user_AccountId samt customDimensions.
* *Anpassade data:* Med Application Insights kan du lägga till en uppsättning anpassade dimensioner till alla datatyper. Dessa dimensioner kan vara *alla* data. Använd följande fråga för att identifiera eventuella anpassade dimensioner som samlats in under de senaste 24 timmarna:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data i minnet och under överföring:* Application Insights spårar undantag, begäranden, beroendeanrop och spårningar. Privata data kan ofta samlas in på kod- och HTTP-samtalsnivå. Granska undantag, begäranden, beroenden och spårningstabeller för att identifiera sådana data. Använd [telemetriinitierare](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) där det är möjligt för att fördunkla dessa data.
* *Snapshot Debugger fångar:* Snapshot [Debugger-funktionen](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) i Application Insights kan du samla in felsökningsögonblicksbilder när ett undantag fångas på produktionsinstansen för ditt program. Ögonblicksbilder exponerar den fullständiga stackspårningen som leder till undantagen samt värdena för lokala variabler vid varje steg i stapeln. Tyvärr tillåter den här funktionen inte selektiv radering av fästpunkter eller programmatisk åtkomst till data i ögonblicksbilden. Om standardlagringsfrekvensen för ögonblicksbilder inte uppfyller dina efterlevnadskrav är rekommendationen därför att stänga av funktionen.

## <a name="how-to-export-and-delete-private-data"></a>Så här exporterar och tar du bort privata data

Som nämnts i [avsnittet om hantering](#strategy-for-personal-data-handling) av personuppgifter tidigare rekommenderas det __starkt__ att om det är möjligt omstrukturera din policy för datainsamling för att inaktivera insamling av privata data, fördunkla eller anonymisera den, eller på annat sätt ändra den för att ta bort den från att betraktas som "privat". Hantering av data kommer främst att resultera i kostnader för dig och ditt team att definiera och automatisera en strategi, bygga ett gränssnitt för dina kunder att interagera med sina data genom, och löpande underhållskostnader. Vidare är det beräkningsmässigt kostsamt för Log Analytics och Application Insights, och en stor mängd samtidiga fråge- eller rensning api-anrop kan påverka all annan interaktion med Log Analytics-funktioner negativt. Med detta sagt finns det verkligen några giltiga scenarier där privata uppgifter måste samlas in. I dessa fall bör data hanteras enligt beskrivningen i detta avsnitt.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visa och exportera

För både vy- och exportdatabegäranden bör [Log Analytics-fråge-API:et](https://dev.loganalytics.io/) eller [application insights-fråge-API:et](https://dev.applicationinsights.io/quickstart) användas. Logik för att konvertera formen på data till en lämplig en att leverera till användarna kommer att vara upp till dig att genomföra. [Azure Functions](https://azure.microsoft.com/services/functions/) är ett bra ställe att vara värd för sådan logik.

> [!IMPORTANT]
>  Medan de allra flesta rensningsåtgärder kan slutföras mycket snabbare än serviceavtalet, **är det formella serviceavtalet för slutförandet av rensningsåtgärder inställda på 30 dagar** på grund av deras stora inverkan på den dataplattform som används. Detta är en automatiserad process; det finns inget sätt att begära att en åtgärd hanteras snabbare.

### <a name="delete"></a>Ta bort

> [!WARNING]
> Tar bort i Log Analytics är destruktiva och icke-reversibla! Var ytterst försiktig i deras utförande.

Vi har gjort tillgängliga som en del av en sekretess hantering av en *rensning* API-sökväg. Den här sökvägen bör användas sparsamt på grund av risken i samband med detta, den potentiella prestandapåverkan och möjligheten att skeva all-up-aggregeringar, mätningar och andra aspekter av dina Log Analytics-data. Se avsnittet [Strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) för alternativa metoder för att hantera privata data.

Rensa är en mycket privilegierad åtgärd som ingen app eller användare i Azure (inklusive även resursägaren) har behörighet att köra utan att uttryckligen beviljas en roll i Azure Resource Manager. Den här rollen är _Datarensare_ och bör delegeras försiktigt på grund av risken för dataförlust. 

> [!IMPORTANT]
> För att hantera systemresurser begränsas rensningsbegäranden med 50 begäranden per timme. Du bör batch körningen av rensningsbegäranden genom att skicka ett enda kommando vars predikat innehåller alla användaridentiteter som kräver rensning. Använd [operatorn i](/azure/kusto/query/inoperator) för att ange flera identiteter. Du bör köra frågan innan du kör rensningsbegäran för att kontrollera att resultaten förväntas. 



När Azure Resource Manager-rollen har tilldelats är två nya API-sökvägar tillgängliga: 

#### <a name="log-data"></a>Loggdata

* [POST-rensning](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - tar ett objekt som anger parametrar för data för att ta bort och returnerar en referens GUID 
* GET-rensningsstatus - POST-rensningsanropet returnerar ett "x-ms-status-location"-huvud som innehåller en URL som du kan anropa för att fastställa status för ditt rensnings-API. Ett exempel:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Även om vi förväntar oss att de allra flesta utrensningsåtgärder slutförs mycket snabbare än vårt serviceavtal, på grund av deras stora inverkan på den dataplattform som används av Log Analytics, **är det formella SLA för slutförandet av rensningsåtgärder satt till 30 dagar.** 

#### <a name="application-data"></a>Programdata

* [POST-rensning](https://docs.microsoft.com/rest/api/application-insights/components/purge) - tar ett objekt som anger parametrar för data för att ta bort och returnerar en referens GUID
* GET-rensningsstatus - POST-rensningsanropet returnerar ett "x-ms-status-location"-huvud som innehåller en URL som du kan anropa för att fastställa status för ditt rensnings-API. Ett exempel:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Även om de allra flesta rensningsåtgärder kan slutföras mycket snabbare än serviceavtalet, på grund av deras stora inverkan på den dataplattform som används av Application Insights, **är det formella SLA för slutförandet av rensningsåtgärder inställd på 30 dagar**.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur Logganalysdata samlas in, bearbetas och skyddas finns i [Logganalysdatasäkerhet](../../azure-monitor/platform/data-security.md).
- Mer information om hur Application Insights-data samlas in, bearbetas och skyddas finns i [Datasäkerhet för Application Insights](../../azure-monitor/app/data-retention-privacy.md).
