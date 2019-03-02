---
title: Övervakning och bearbetning av säkerhetshändelser i Azure Security Center | Microsoft Docs
description: Lär dig hur du kan använda instrumentpanelen för Security Center-händelser för att se säkerhetshändelser från dina virtuella Azure-datorer och Azure-datorer.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 2b9426043619887d99003677fd6e8488cb56cce2
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241076"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Övervakning och bearbetning av säkerhetshändelser i Azure Security Center
Instrumentpanelen för händelser innehåller en översikt av antalet säkerhetshändelser som samlas in över tid och en lista över viktiga händelser som kan kräva din uppmärksamhet.  

> [!NOTE]
> Om du vill använda den här funktionen, måste din arbetsyta köra Log Analytics version 2 och vara på Security Center Standard-nivån. Se de Säkerhetscenter [prissättningssidan](security-center-pricing.md) för mer information om Standard-nivån.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="what-is-a-security-event"></a>Vad är en säkerhetshändelse?
Security Center använder Microsoft Monitoring Agent att samla in olika säkerhetsrelaterade konfigurationer och händelser från dina virtuella datorer och lagrar dessa händelser i dina arbetsytor. Exempel på sådana data är: operativsystemloggar (Windows-händelseloggar), kör bearbetar och händelser från säkerhetslösningar som är integrerade med Security Center. Microsoft Monitoring Agent kopierar också kraschdumpfiler till dina arbetsytor.

## <a name="events-processed-dashboard"></a>Instrumentpanelen för händelser som bearbetas
Du har åtkomst till den **händelser** instrumentpanelen från huvudmenyn i Security Center eller Security Center **översikt** bladet.  

![Instrumentpanelen för händelser som bearbetas][1]

Den **händelser** panelen **Security Center** visar antalet händelser som flödar till Security Center från dina virtuella Azure-datorer och Azure-datorer.

Den **instrumentpanelen för händelser** innehåller en översikt över hur många händelser som bearbetas övertid och en lista över händelser.

 ![Instrumentpanel][2]

 Den övre delen av instrumentpanelen trender alla händelser som bearbetas under den senaste veckan. Nederkant hälften av instrumentpanelen visar viktiga händelser och alla händelser enligt typ:

 - **Viktiga händelser** omfattar händelsefrågor som Security Center tillhandahåller och händelsefrågor som du skapar och lägga till. Instrumentpanelen innehåller också en snabb överblick över antal varje viktig händelse.
 - **Alla händelser enligt typ** visar vilka händelsetyper av som tas emot och ett antal för varje typ av. Exempel på händelsetyp är SecurityEvent, CommonSecurityLog, WindowsFirewall och W3CIISLog.

> [!NOTE]
> Viktiga händelser inkluderar [web säkerhetsbaslinje](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Målet med utvärderingen av webbaslinjen är att hitta potentiellt sårbara webbserverinställningar.

## <a name="view-processed-event-details"></a>Visa bearbetade händelseinformation
1. Under den **Security Center** Huvudmeny väljer **händelser**.
2. Den **instrumentpanelen för händelser** Väljaren kan öppna. Om du har endast en arbetsyta, visas inte den här Väljaren. Om du har fler än en arbetsyta måste välja en arbetsyta för att visa information om den bearbetade händelse. Välj en arbetsyta i listan om du har fler än en arbetsyta.

  ![Arbetsytans lista][3]

3. Den **instrumentpanelen för händelser** öppnas där du kan se händelsedetaljerna för den valda arbetsytan. Du kan visa viktiga händelser och alla händelser enligt typ.  I det här exemplet har vi valt **viktiga händelser**.

  ![Viktig händelse][4]

4. Du kan fråga efter mer data under arbetsytan genom att välja en händelsetyp. I det här exemplet har vi valt **SecurityEvent**.

  ![Att välja en händelsetyp][5]

5. **Loggsöknings-** öppnas med mer information om hur händelsetyp.

  ![Loggsökning][6]

## <a name="add-a-notable-event"></a>Lägg till en viktig händelse
Security Center tillhandahåller out-of the box viktiga händelser. Du kan lägga till viktiga händelser baserat på din egen fråga med hjälp av den [Kusto-frågespråket](../log-analytics/log-analytics-search-reference.md). Vi återkommer till den **instrumentpanelen för händelser** att lägga till en viktig händelse.

1. Välj **Lägg till viktig händelse**.

  ![Lägg till en viktig händelse][7]

2. **Lägg till anpassad viktig händelse** öppnas.  Under **visningsnamn**, ange ett namn för din viktig händelse. Under **sökfråga**, ange din fråga för händelsen.

  ![Ange din fråga][8]

4. Välj **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Uppdatera din arbetsyta för bearbetning av händelser
Din arbetsyta måste köra Log Analytics version 2 och på standardnivån i Security Center använder händelsebearbetning i Security Center. Den **instrumentpanelen för händelser** Väljaren identifierar arbetsytor som inte uppfyller dessa krav.

![Arbetsytan uppfyller inte kraven][9]

Om arbetsytan raden:

- Innehåller **kräver uppdatera** -du behöver uppdatera din arbetsyta till Log Analytics version 2
- Innehåller **uppgradera planera** – du måste uppgradera arbetsytan till standardnivån i Security Center
- Har inte angetts - uppfyller din arbetsyta och att välja en arbetsyta tar dig till instrumentpanelen

> [!NOTE]
> Under **instrumentpanelen för händelser**, **händelser** kolumn visar antalet händelser i varje arbetsyta.  Den här kolumnen är tom i vissa arbetsytors eftersom Security Centers kostnadsfria nivån tillämpas på arbetsytan. Security Center samlar in händelser under den kostnadsfria nivån, men händelserna sparas inte i Azure Monitor-loggar och är inte tillgängliga i instrumentpanelen.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Uppdatera arbetsytan till Log Analytics version 2
1. Välj en arbetsyta som **kräver uppdatering**.
2. **Sök efter uppgraderingen** öppnas. Välj **uppgradera nu**.

  ![Uppgradera nu][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Uppgradera till standardnivån i Security Center
1. Välj en arbetsyta med **uppgradera planera**.
2. **Instrumentpanelen för händelser** öppnas. Välj **försök händelser instrumentpanelen**.

  ![Testa instrumentpanelen][11]

3. Under **registrering för avancerad säkerhet**, Välj den arbetsyta som du uppgraderar.
4. Under **priser**väljer **Standard**.
5. Välj **Spara**.

  ![Uppgradera till Standard-nivån][12]

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder instrumentpanelen för Security Center-händelse. Mer information om hur instrumentpanelen fungerar och för att skriva din egen händelsefrågor, se:

- [Vad är Azure Monitor-loggar?](../log-analytics/log-analytics-overview.md) – Översikt över Azure Monitor-loggar
- [Förstå log söker i Kusto](../log-analytics/log-analytics-log-search-new.md) – beskriver hur du använder loggsökningar i Azure Monitor-loggar och tillhandahåller begrepp som du bör känna till innan du skapar en loggsökning
- [Kusto-sökreferens](../log-analytics/log-analytics-search-reference.md) – Lär dig att skriva egna händelsefrågor med hjälp av frågespråket i loggen

I följande avsnitt kan du lära dig mer om Security Center:

- [Översikt över Security Center](security-center-intro.md) – beskriver Security Center viktiga funktioner

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
