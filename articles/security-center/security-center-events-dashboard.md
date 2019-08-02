---
title: Övervaka och bearbeta säkerhets händelser i Azure Security Center | Microsoft Docs
description: Lär dig hur du kan använda Security Centers instrument panel för händelser för att se säkerhets händelser från dina virtuella Azure-datorer och datorer som inte är Azure-datorer.
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
ms.openlocfilehash: 389aaee621251890cd3f75744a94b9c9b29c5695
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662386"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Övervakning och bearbetning av säkerhets händelser i Azure Security Center
Instrument panelen händelser ger en översikt över antalet säkerhets händelser som samlats in över tid och en lista över viktiga händelser som kan kräva din uppmärksamhet.  

> [!NOTE]
> Instrument panelen för säkerhets händelser har dragits tillbaka den 31 juli 2019. Mer information och alternativa tjänster finns i [dra tillbaka Security Center funktioner (2019 juli)](security-center-features-retirement-july2019.md#menu_events).

## <a name="what-is-a-security-event"></a>Vad är en säkerhets händelse?
Security Center använder Microsoft Monitoring Agent för att samla in olika säkerhetsrelaterade konfigurationer och händelser från dina datorer och lagrar dessa händelser på dina arbets ytor. Exempel på sådana data är: operativ system loggar (Windows-händelseloggar), processer som körs och händelser från säkerhetslösningar som är integrerade med Security Center. Microsoft Monitoring Agent kopierar också kraschdumpfiler till dina arbetsytor.

## <a name="requirements"></a>Krav
Om du vill använda den här funktionen måste arbets ytan köra Log Analytics version 2 och vara på Security Center standard nivå. Se sidan Security Center [prissättning](security-center-pricing.md) för mer information om standard nivån.

## <a name="events-processed-dashboard"></a>Instrument panel för händelser som bearbetas
Du kommer åt instrument panelen **händelser** från bladet Security Center huvud meny eller Security Center **Översikt** .  

![Instrument panel för händelser som bearbetas][1]

Panelen **händelser** under **Security Center** visar antalet händelser som flödar till Security Center från dina virtuella Azure-datorer och datorer som inte är Azure-datorer.

**Instrument panelen händelser** ger en översikt över antalet händelser som har bearbetats övertid och en lista över händelser.

 ![Instrumentpanel][2]

 Den övre halvan av instrument panelen trender alla händelser som bearbetas under den senaste veckan. Den nedre halvan av instrument panelen listar viktiga händelser och alla händelser efter typ:

 - **Viktiga händelser** innehåller händelse frågor som Security Center tillhandahåller och händelse frågor som du skapar och lägger till. Instrument panelen ger också en snabb överblick över antalet viktiga händelser.
 - **Alla händelser efter typ** visar de händelse typer som tas emot och ett antal för varje typ. Exempel på händelse typ är SecurityEvent, CommonSecurityLog, WindowsFirewall och W3CIISLog.

> [!NOTE]
> Viktiga händelser är [utvärdering av webb bas linje](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). Målet med utvärderingen av webbaslinjen är att hitta potentiellt sårbara webbserverinställningar.

## <a name="view-processed-event-details"></a>Visa information om bearbetade händelser
1. Välj **händelser**under **Security Center** huvud menyn.
2. **Instrument panels** väljaren för händelser kan öppnas. Om du bara har en arbets yta visas inte den här arbets ytans väljare. Om du har fler än en arbets yta måste du välja en arbets yta för att visa information om bearbetade händelser. Välj en arbets yta i listan om du har fler än en arbets yta.

   ![Lista över arbets ytor][3]

3. **Instrument panelen händelser** öppnas och visar händelse information för den valda arbets ytan. Du kan visa viktiga händelser och alla händelser efter typ.  I det här exemplet valde vi **viktiga händelser**.

   ![Viktig händelse][4]

4. Du kan fråga efter mer data under arbets ytan genom att välja en händelse typ. I det här exemplet valde vi **SecurityEvent**.

   ![Välja en händelse typ][5]

5. **Loggs ökningen** öppnas med ytterligare information om händelse typen.

   ![Loggsökning][6]

## <a name="add-a-notable-event"></a>Lägg till en viktig händelse
Security Center ger välkomst händelser som är viktiga. Du kan lägga till viktiga händelser baserat på din egen fråga med hjälp av [Kusto-frågespråket](../log-analytics/log-analytics-search-reference.md). Vi går tillbaka till **instrument panelen händelser** för att lägga till en viktig händelse.

1. Välj **Lägg till viktiga händelse**.

   ![Lägg till en viktig händelse][7]

2. **Lägg till anpassad viktiga händelse** öppnas.  Under **visnings namn**anger du ett namn för din viktiga händelse. Ange din fråga för händelsen under **Sök fråga**.

   ![Ange din fråga][8]

4. Välj **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Uppdatera din arbets yta för händelse bearbetning
Arbets ytan måste köra Log Analytics version 2 och vara på Security Center standard nivå för att använda händelse bearbetning i Security Center. På **instrument panelen för händelser instrument panels** väljare identifieras arbets ytor som inte uppfyller dessa krav.

![Arbets ytan uppfyller inte kraven][9]

Om raden för arbets ytan:

- Innehåller **uppdateringen kräver uppdatering** – du måste uppdatera arbets ytan till Log Analytics version 2
- Innehåller **uppgraderings plan** – du måste uppgradera arbets ytan till Security Center standard nivån
- Är tomt – din arbets yta uppfyller kraven och du kan välja en arbets yta som tar dig till instrument panelen

> [!NOTE]
> Under **instrument panel för händelser**anger kolumnen **händelser** mängden händelser i varje arbets yta.  Den här kolumnen är tom för vissa arbets ytor eftersom Security Center kostnads fri nivå tillämpas på den arbets ytan. Under den kostnads fria nivån kommer Security Center att samla in händelser, men händelserna sparas inte i Azure Monitor loggar och är inte tillgängliga på instrument panelen.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Uppdatera arbets ytan till Log Analytics version 2
1. Välj en arbets yta som **kräver uppdatering**.
2. **Sök uppgraderingen** öppnas. Välj **Uppgradera nu**.

   ![Uppgradera nu][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Uppgradera till Security Center standard nivån
1. Välj en arbets yta med **uppgraderings plan**.
2. **Instrument panelen händelser** öppnas. Välj **instrument panelen prova händelser**.

   ![Prova instrument panelen][11]

3. Under **registrering till avancerad säkerhet**väljer du den arbets yta som du uppgraderar.
4. Under **prissättning**väljer du **standard**.
5. Välj **Spara**.

   ![Uppgradera till standard nivån][12]

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Security Centers händelse instrument panel. Mer information om hur instrument panelen fungerar och hur du skriver egna händelse frågor finns i:

- [Vad är Azure Monitor loggar?](../log-analytics/log-analytics-overview.md) – Översikt över Azure Monitor loggar
- [Förstå loggs ökningar i Kusto](../log-analytics/log-analytics-log-search-new.md) – beskriver hur loggs ökningar används i Azure Monitor loggar och ger begrepp som bör förstås innan en loggs ökning skapas
- [Sök referens för Kusto](../log-analytics/log-analytics-search-reference.md) – lär dig hur du skriver egna händelse frågor med hjälp av frågespråket i loggen

I följande avsnitt kan du lära dig mer om Security Center:

- [Översikt över Security Center](security-center-intro.md) – beskriver viktiga funktioner i Security Center

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
