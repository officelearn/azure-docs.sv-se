---
title: "Kör bakgrundsaktiviteter med WebJobs i Azure App Service"
description: "Lär dig hur du använder WebJobs för att köra bakgrundsaktiviteter i Azure App Service web apps, API apps och mobilappar."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 661bfadfe1094271ad2ab672ad3f755c0fd24f02
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Kör bakgrundsaktiviteter med WebJobs i Azure App Service

## <a name="overview"></a>Översikt
WebJobs är en funktion i [Azure App Service](https://docs.microsoft.com/azure/app-service/) som gör att du kan köra ett program eller skript i samma kontext som webbprogrammet, API-app eller mobila appar. Det finns utan extra kostnad för att använda WebJobs.

Den här artikeln visar hur du distribuerar WebJobs med hjälp av den [Azure-portalen](https://portal.azure.com) att överföra en körbar fil eller skript. Information om hur du utvecklar och distribuerar WebJobs med hjälp av Visual Studio finns i [distribuera WebJobs med Visual Studio](websites-dotnet-deploy-webjobs.md).

Azure WebJobs SDK kan användas med WebJobs till att förenkla många programmeringsspråk. Mer information finns i [vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions erbjuder ett annat sätt att köra program och skript. En jämförelse mellan WebJobs och funktioner finns [Välj mellan flödet, Logic Apps, funktioner och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webbjobbet typer

I följande tabell beskrivs skillnaderna mellan *kontinuerlig* och *utlöses* WebJobs.


|Kontinuerlig  |Utlöst  |
|---------|---------|
| Startar omedelbart när Webbjobbet har skapats. Om du vill förhindra jobbet avslutas, fungerar programmet eller skriptet vanligtvis på arbetet i en oändlig loop. Om jobbet avslutas kan du starta om den. | Startar bara om den utlöses manuellt eller enligt ett schema. |
| Körs på alla instanser som webbappen körs på. Alternativt kan du begränsa Webbjobbet till en enda instans. |Körs på en enda instans som Azure väljer för belastningsutjämning.|
| Har stöd för fjärrfelsökning. | Stöder inte fjärrfelsökning.|

> [!NOTE]
> Ett webbprogram kan timeout efter 20 minuter av inaktivitet. Endast begäranden till webbplatsen scm (distribution) eller till webbappens sidor i portalen återställer timern. Begäranden till den faktiska platsen återställa inte timern. Om din app körs kontinuerligt eller aktivera schemalagda Webbjobb **alltid på** så att WebJobs kör på ett tillförlitligt sätt. Den här funktionen är endast tillgänglig i Basic, Standard och Premium [prisnivåer](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>Filtyper som stöds för skript eller program

Följande filtyper stöds:

* .cmd, .bat, .exe (med Windows cmd)
* .ps1 (med PowerShell)
* .SH (med Bash)
* .php (med PHP)
* .PY (med Python)
* .js (med Node.js)
* .JAR (med Java)

## <a name="CreateContinuous"></a> Skapa ett kontinuerligt Webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I den [Azure-portalen](https://portal.azure.com), gå till den **Apptjänst** sidor i din App Service webbapp, API-app eller mobila appar.

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. I den **WebJobs** väljer **Lägg till**.

    ![Webbjobbet sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd den **lägger till Webbjobb** inställningar som anges i tabellen.

   ![Lägg till Webbjobb på sidan](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myContinuousWebJob | Ett namn som är unikt i en Apptjänst-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken än ”-” och ”_”. |
   | **Ladda upp filen** | ConsoleApp.zip | En *.zip* -fil som innehåller filen körbara filer eller skript samt alla stödfiler som behövs för att köra program eller skript. De körbara filen eller skriptet filen typerna som stöds finns i den [filtyper som stöds](#acceptablefiles) avsnitt. |
   | **Typ** | Kontinuerlig | Den [Webbjobb typer](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Skalning** | Multi-instans | Endast tillgängligt för kontinuerliga Webbjobb. Anger om program eller skript som körs på alla instanser eller endast en instans. Alternativet för att köras på flera instanser gäller inte för kostnadsfri eller delad [prisnivåer](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Klicka på **OK**.

   Det nya Webbjobbet visas på den **WebJobs** sidan.

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. För att stoppa eller starta om ett kontinuerligt Webbjobb, högerklicka på Webbjobb i listan och klickar på **stoppa** eller **starta**.

    ![Stoppa ett kontinuerligt Webbjobb](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Skapa ett manuellt utlösta Webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I den [Azure-portalen](https://portal.azure.com), gå till den **Apptjänst** sidor i din App Service webbapp, API-app eller mobila appar.

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. I den **WebJobs** väljer **Lägg till**.

    ![Webbjobbet sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd den **lägger till Webbjobb** inställningar som anges i tabellen.

   ![Lägg till Webbjobb på sidan](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myTriggeredWebJob | Ett namn som är unikt i en Apptjänst-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken än ”-” och ”_”.|
   | **Ladda upp filen** | ConsoleApp.zip | En *.zip* -fil som innehåller filen körbara filer eller skript samt alla stödfiler som behövs för att köra program eller skript. De körbara filen eller skriptet filen typerna som stöds finns i den [filtyper som stöds](#acceptablefiles) avsnitt. |
   | **Typ** | Utlöst | Den [Webbjobb typer](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Utlösare** | Manuell | |

4. Klicka på **OK**.

   Det nya Webbjobbet visas på den **WebJobs** sidan.

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Om du vill köra Webbjobbet högerklickar du på namnet i listan och klickar på **kör**.
   
    ![Kör webbjobb](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Skapa ett schemalagda Webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I den [Azure-portalen](https://portal.azure.com), gå till den **Apptjänst** sidor i din App Service webbapp, API-app eller mobila appar.

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. I den **WebJobs** väljer **Lägg till**.

   ![Webbjobbet sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd den **lägger till Webbjobb** inställningar som anges i tabellen.

   ![Lägg till Webbjobb på sidan](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myScheduledWebJob | Ett namn som är unikt i en Apptjänst-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken än ”-” och ”_”. |
   | **Ladda upp filen** | ConsoleApp.zip | En *.zip* -fil som innehåller filen körbara filer eller skript samt alla stödfiler som behövs för att köra program eller skript. De körbara filen eller skriptet filen typerna som stöds finns i den [filtyper som stöds](#acceptablefiles) avsnitt. |
   | **Typ** | Utlöst | Den [Webbjobb typer](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Utlösare** | Har schemalagts | Aktivera funktionen alltid på för att schemalägga att fungera på ett tillförlitligt sätt. Always On är endast tillgänglig i Basic, Standard och Premium prisnivåer.|
   | **CRON-uttryck** | 0 0/20 * * * * | [CRON-uttryck](#cron-expressions) beskrivs i följande avsnitt. |

4. Klicka på **OK**.

   Det nya Webbjobbet visas på den **WebJobs** sidan.

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>CRON-uttryck

En [CRON-uttryck](https://en.wikipedia.org/wiki/Cron) består av sex fält: `{second} {minute} {hour} {day} {month} {day of the week}`.  Här följer några exempel:

* Var 15: e minut: `0 */15 * * * *`
* Varje timme (det vill säga när antalet minuter är 0): `0 0 * * * *` 
* Varje timme från 9: 00 och 17: 00: `0 0 9-17 * * *` 
* På 9:30:00 varje dag: `0 30 9 * * *`
* På 9:30:00 varje vardag: `0 30 9 * * 1-5`

Du kan ange CRON-uttryck i portalen eller inkluderar en `settings.job` filen i roten på din Webbjobb *.zip* filen, som i följande exempel:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> När du distribuerar ett Webbjobb från Visual Studio, markera din `settings.job` filegenskaper som **kopiera om nyare**.

## <a name="ViewJobHistory"></a> Visa jobbets historik

1. Välj WebJob som du vill se historiken för och välj sedan den **loggar** knappen.
   
   ![Loggar knappen](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. I den **Webbjobb information** väljer du en tid att visa detaljer för en körning.
   
   ![Webbjobbet information](./media/web-sites-create-web-jobs/webjobdetails.png)

3. I den **Webbjobb kör information** väljer **växla utdata** att se texten i logginnehållet.
   
    ![Web-jobbet körs information](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Om du vill visa den resulterande texten i ett nytt webbläsarfönster, Välj **hämta**. Om du vill hämta själva texten, högerklicka på **hämta** och använder webbläsaren för att spara filen innehållet.
   
5. Välj den **WebJobs** dynamiska längst upp på sidan för att gå till en lista över WebJobs.

    ![Dynamiska länkar på Webbjobbet](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista över WebJobs i instrumentpanelen för historik](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a>Nästa steg

Azure WebJobs SDK kan användas med WebJobs till att förenkla många programmeringsspråk. Mer information finns i [vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) och [WebJobs SDK Snabbreferens](./media/web-sites-create-web-jobs/webjobs-sdk-quick-reference.png).
