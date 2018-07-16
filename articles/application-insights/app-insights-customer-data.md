---
title: Vägledning för personliga data som lagras i Azure Application Insights | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar personliga data som lagras i Azure Application Insights och metoderna för att identifiera och ta bort den.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044721"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Vägledning för personliga data som lagras i Application Insights

Application Insights är ett datalager där personliga data är sannolikt kommer att finnas. Den här artikeln beskriver där i Application Insights dessa data ofta finns, samt funktioner som är tillgängliga för dig att hantera dessa data.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategi för hantering av personuppgifter

När det kommer att vara upp till dig och ditt företag slutänden att avgöra vilken strategi som du hanterar dina privata data (om alls), följer några strategier som möjligt. De visas i prioritetsordning från en teknisk synvinkel från mest till minst bättre:
* Om möjligt, stoppa insamling av, Förvräng, maskera eller justera de data som samlas in för att undanta den från ses som _privata_. Den här metoden är att föredra, sparar du att behöva skapa en hantera strategi kostsam och påverkar data.
* Om möjligt, försöka att normalisera data för att minska påverkan på dataplattform och prestanda. Till exempel i stället för att logga en explicit användar-ID kan du skapa en sökning av data som kommer att korrelera användarnamnet och deras information till ett internt ID som sedan kan loggas någon annanstans. På så sätt kan om en av dina användare att be dig att ta bort sin personliga information, är det möjligt att endast borttagning av raden i uppslagstabellen för användaren är tillräckligt. 
* Slutligen privata data måste samlas in, skapar du en process runt Rensa API sökvägen och befintlig fråga API sökväg för att uppfylla alla skyldigheter som du kan ha runt exportera och ta bort alla privata data som hör till en användare.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Var ska se ut för privata data i Application Insights?

Application Insights är en helt flexibla store, som vid datavetenskapsmetod som fastställer ett schema till dina data, kan du åsidosätta varje fält med anpassade värden. Därför är det omöjligt att säga exakt där personliga data finns i ett visst program. Följande platser, men är bra startpunkter i inventeringen:

* *IP-adresser*: medan Application Insights kommer som standard Förvräng alla fält för IP-adress att ”0.0.0.0”, är det ett relativt vanligt mönster för att åsidosätta det här värdet med faktiska användar-IP-adress för att upprätthålla sessionsinformation. Analytics-frågan nedan kan användas för att hitta någon tabell som innehåller värdena i kolumnen IP-adress än ”0.0.0.0” under de senaste 24 timmarna:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Användar-ID*: som standard, Application Insights använder slumpmässigt genererat ID: N för användaren och sessionsspårning. Det är dock vanligt att de här fälten åsidosätts för att lagra ett ID som är mer relevant för programmet. Till exempel: användarnamn, AAD-GUID, osv. Dessa ID: N ofta anses omfattade som personliga data, och därför bör hanteras på rätt sätt. Vår rekommendation är alltid att försöka Förvräng eller maskera dessa ID: N. Fält där dessa värden finns vanligtvis är session_Id, user_Id, user_AuthenticatedId, user_AccountId samt customDimensions.
* *Anpassade data*: Application Insights kan du lägga till en uppsättning av anpassade dimensioner till någon datatyp av. Dessa dimensioner kan vara *alla* data. Använd följande fråga för att identifiera eventuella anpassade dimensioner som samlas in under de senaste 24 timmarna:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Data i minnet och under överföring*: Application Insights spårar undantag, begäranden, beroendeanrop och spårningar. Privata data kan ofta samlas på koden och HTTP-anrop nivå. Granska undantag, begäranden, beroenden och spårningar tabeller för att identifiera sådana data. Använd [telemetri-initierare](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) där det är möjligt att Förvräng dessa data.
* *Snapshot Debugger insamlingar*: den [Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funktion i Application Insights kan du samla in ögonblicksbilder för felsökning när ett undantag har inträffat på instansen av ditt program. Ögonblicksbilder utsätter den fullständiga stackspårningen som leder till undantagen som värden för lokala variabler vid varje steg i stacken. Tyvärr tillåter inte den här funktionen för selektiv radering av fästpunkter eller programmatisk åtkomst till data i ögonblicksbilden. Om frekvensen för standard ögonblicksbilder kvarhållning inte uppfyller efterlevnadskraven, därför rekommenderar vi att stänga av funktionen.

## <a name="how-to-export-and-delete-private-data"></a>Exportera och ta bort privata data

Det är __starkt__ rekommenderas, om möjligt, för att omstrukturera din princip för datainsamling för att inaktivera insamling av personliga data, dölja eller maskera den, eller på annat sätt modifiera frågan om du vill ta bort den från ses som ”privat”. Hantering av data när den har tagits ut, kommer resultera i kostnader för dig och ditt team att definiera och automatisera en strategi, skapa ett gränssnitt för kunderna att interagera med sina data via och löpande underhållskostnader. Dessutom är det beräkningsmässigt dyra för Application Insights och ett stort antal samtidiga fråga eller rensa API-anrop har möjlighet att påverka alla andra interaktion med Application Insights-funktioner. Som SA, det finns faktiskt fanns några giltiga scenarier där privata data måste samlas in. De här fallen, bör data hanteras enligt beskrivningen i det här avsnittet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Visa och exportera

För både visa och exportera databegäranden, den [fråge-API](https://dev.applicationinsights.io/quickstart) ska användas. Logik för att omvandla formen av data till ett lämpligt alternativ för att förse dina användare kommer att upp till dig att implementera. [Azure Functions](https://azure.microsoft.com/services/functions/) är ett bra ställe att vara värd för sådana logik.

### <a name="delete"></a>Ta bort

> [!WARNING]
> Tar bort i Application Insights är destruktiva och icke-reversibelt! Du vara mycket försiktig i sina körning.

Vi har gjort tillgänglig som en del av en sekretess hantering artikel en ”Rensa” API-sökväg. Den här sökvägen bör användas sparsamt på grund av riskerna med att göra det, potentiell prestandapåverkan och kan ge skeva upp alla aggregeringar, mått och andra aspekter av din Application Insights-data. Se den [strategi för hantering av personuppgifter](#strategy-for-personal-data-handling) ovan för alternativa metoder för att hantera privata data.

Rensa är en mycket Privilegierade åtgärd att ingen app eller användare i Azure (inklusive även resursägaren) har behörighet att köra utan att uttryckligen beviljas en roll i Azure Resource Manager. Den här rollen är _Data Purger_ och noggrant bör delegeras på grund av risken för dataförlust.

När Azure Resource Manager-rollen har tilldelats, två nya API-sökvägar är tillgängliga, fullständig utvecklardokumentation och anropa länkad form:

* [Rensa efter](https://docs.microsoft.com/rest/api/application-insights/components/purge) – tar ett objekt som att ange parametrar för att ta bort och returnerar en referens-GUID
* Rensa status för GET - INLÄGG Rensa anropet returnerar en x-ms-status-location-rubriken som innehåller en URL som du kan anropa för att fastställa statusen för din Rensa API. Exempel:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Även om merparten av Rensa åtgärder kan slutföra mycket snabbare än serviceavtalet, på grund av deras stor inverkan på dataplattform som används av Application Insights **en formell serviceavtal (SLA) för att slutföra åtgärder för rensning har ställts in till 30 dagar**.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur data som samlas in, bearbetas och skyddas i [Application Insights datasäkerhet](app-insights-data-retention-privacy.md).