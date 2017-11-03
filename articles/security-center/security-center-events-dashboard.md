---
title: "Övervakning och bearbetning av säkerhetshändelser i Azure Security Center | Microsoft Docs"
description: "Lär dig hur du kan använda instrumentpanelen för Security Center-händelser för att se säkerhetshändelser från dina virtuella Azure-datorer och Azure-datorer."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Övervakning och bearbetning av säkerhetshändelser i Azure Security Center
Händelser instrumentpanelen innehåller en översikt över antalet säkerhetshändelser samlas in med tiden och en lista över viktiga händelser som kan kräva din uppmärksamhet.  

> [!NOTE]
> Om du vill använda den här funktionen måste din arbetsyta kör logganalys version 2 och vara på standardnivån för Security Center. Se Security Center [sida med priser](security-center-pricing.md) mer information om standardnivån.
>
>

## <a name="what-is-a-security-event"></a>Vad är en säkerhetshändelse?
Security Center använder Microsoft Monitoring Agent att samla in olika säkerhet relaterade konfigurationer och händelser från dina datorer och lagrar dessa händelser i dina arbetsytor. Exempel på sådana data är: operativsystemet systemloggar (Windows-händelseloggar), kör bearbetar och händelser från säkerhetslösningar integrerad med Security Center. Microsoft Monitoring Agent kopierar också kraschdumpfiler till dina arbetsytor.

## <a name="events-processed-dashboard"></a>Händelser som har bearbetats instrumentpanelen
Du har åtkomst till den **händelser** instrumentpanelen från Security Center huvudmenyn eller Security Center **översikt** bladet.  

![Händelser som har bearbetats instrumentpanelen][1]

Den **händelser** panelen **Security Center** visar antalet händelser som flödar till Security Center från dina virtuella Azure-datorer och Azure-datorer.

Den **händelser instrumentpanelen** ger en översikt över antalet händelser som har bearbetats övertid och en lista över händelser.

 ![Instrumentpanel][2]

 Den övre delen av instrumentpanelen trender alla händelser som har bearbetats under den senaste veckan. Nederkant hälften av instrumentpanelen visar en lista över viktiga händelser och alla händelser efter typ:

 - **Viktiga händelser** inkluderar händelsefrågor som tillhandahåller Security Center och händelsefrågor som du skapar och lägga till. Instrumentpanelen innehåller också en snabb överblick över antalet varje viktiga händelse.
 - **Alla händelser efter typ** visar vilka händelsetyper av som tas emot och ett antal för varje typ av. Exempel på händelsetyp är SecurityEvent, CommonSecurityLog, WindowsFirewall och W3CIISLog.

> [!NOTE]
> Viktiga händelser inkluderar [web baslinjen assessment](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Målet med utvärderingen av webbaslinjen är att hitta potentiellt sårbara webbserverinställningar.

## <a name="view-processed-event-details"></a>Visa bearbetade händelseinformation
1. Under den **Security Center** Huvudmeny, Välj **händelser**.
2. Den **händelser instrumentpanelen** arbetsytan selector öppnas. Om du har en enda arbetsyta, visas inte den här arbetsytan väljare. Om du har mer än en arbetsyta, måste du välja en arbetsyta för att visa händelseinformation om bearbetade. Välj en arbetsyta i listan om du har mer än en arbetsyta.

  ![Arbetsytelista][3]

3. Den **händelser instrumentpanelen** öppnas med händelseinformationen för den valda arbetsytan. Du kan visa viktiga händelser och alla händelser som är av typen.  Vi valde i det här exemplet **viktiga händelser**.

  ![Viktiga händelse][4]

4. Du kan fråga efter mer data under arbetsytan genom att välja en händelsetyp. Vi valde i det här exemplet **SecurityEvent**.

  ![Att välja en händelsetyp][5]

5. **Logga Sök** öppnas med ytterligare information om vilken typ av händelse.

  ![Loggsökning][6]

## <a name="add-a-notable-event"></a>Lägg till en viktiga händelse
Security Center ger out box viktiga händelser. Du kan lägga till viktiga händelser baserat på egna frågan med de [Log Analytics-frågespråket](../log-analytics/log-analytics-search-reference.md). Vi återkommer till den **händelser instrumentpanelen** att lägga till en händelse som viktiga.

1. Välj **lägga till viktiga händelsen**.

  ![Lägg till en viktiga händelse][7]

2. **Lägg till anpassade viktiga händelsen** öppnas.  Under **visningsnamn**, ange ett namn för din viktiga händelse. Under **sökfråga**, ange din fråga för händelsen.

  ![Ange din fråga][8]

4. Välj **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Uppdatera din arbetsyta för bearbetning av händelser
Ditt arbetsområde måste vara igång logganalys version 2 och vara i Security Center standardnivån att använda händelsebearbetning i Security Center. Den **händelser instrumentpanelen** arbetsytan selector identifierar arbetsytor som inte uppfyller kraven.

![Arbetsytan uppfyller inte kraven][9]

Om arbetsytan rad:

- Innehåller **kräver uppdatera** -du behöver uppdatera din arbetsyta till logganalys version 2
- Innehåller **uppgradera planera** – du behöver uppgradera din arbetsyta till standardnivån om Security Center
- Tomt - arbetsytan uppfyller kraven och välja en arbetsyta går till instrumentpanelen

> [!NOTE]
> Under **händelser instrumentpanelen**, **händelser** visar mängden händelser i varje arbetsyta.  Den här kolumnen är tomt i vissa arbetsytors eftersom Security Center kostnadsfria nivån tillämpas på arbetsytan. Under den kostnadsfria nivån Security Center samlar in händelser men händelserna som sparas inte i logganalys och är inte tillgängliga i instrumentpanelen.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Uppdatera arbetsytan till logganalys version 2
1. Välj en arbetsyta som **kräver uppdatering**.
2. **Söka efter uppgraderingen** öppnas. Välj **uppgradera nu**.

  ![Uppgradera nu][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Uppgradera till standardnivån om Security Center
1. Välj en arbetsyta med **uppgradera planera**.
2. **Händelser instrumentpanelen** öppnas. Välj **försök händelser instrumentpanelen**.

  ![Försök instrumentpanelen][11]

3. Under **Onboarding till avancerad säkerhet**, väljer arbetsytan som du uppgraderar.
4. Under **priser**väljer **Standard**.
5. Välj **Spara**.

  ![Uppgradera till standardnivån][12]

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder instrumentpanelen för Security Center-händelse. Mer information om hur instrumentpanelen fungerar och för att skriva egna händelsefrågor finns:

- [Vad är Log Analytics?](../log-analytics/log-analytics-overview.md) – Översikt på logganalys
- [Förstå loggen söker i logganalys](../log-analytics/log-analytics-log-search-new.md) – beskriver hur loggen sökningar används i logganalys och begrepp som du bör känna till innan du skapar en logg-sökning
- [Logganalys söka referens](../log-analytics/log-analytics-search-reference.md) – Lär dig att skriva egna händelsefrågor med frågespråket i loggen

Mer information om Security Center finns:

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
