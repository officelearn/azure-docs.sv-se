---
title: 'Självstudiekurs: Övervaka Azure Spring Cloud-resurser med aviseringar och åtgärdsgrupper | Microsoft-dokument'
description: Läs om hur du använder Spring Cloud-aviseringar.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.openlocfilehash: cf7e075244a83190b83e629ef300a4903b114a6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77920084"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Självstudiekurs: Övervaka Spring Cloud-resurser med aviseringar och åtgärdsgrupper

Azure Spring Cloud-aviseringar stöder övervakningsresurser baserat på villkor som tillgänglig lagring, antal begäranden eller dataanvändning. En avisering skickar meddelande när priser eller villkor uppfyller de definierade specifikationerna.

Det finns två steg för att ställa in en aviseringspipeline: 
1. Konfigurera en åtgärdsgrupp med de åtgärder som ska vidtas när en avisering utlöses, till exempel e-post, SMS, Runbook eller Webhook. Åtgärdsgrupper kan återanvändas bland olika aviseringar.
2. Ställ in varningsregler. Reglerna binder måttmönster med åtgärdsgrupperna baserat på målresurs, mått, villkor, tidsaggregering osv.

## <a name="prerequisites"></a>Krav
Förutom Azure Spring-kraven beror den här självstudien på följande resurser.

* En distribuerad Azure Spring Cloud-instans.  Följ vår [snabbstart](spring-cloud-quickstart-launch-app-cli.md) för att komma igång.

* En Azure-resurs att övervaka. I det här exemplet övervakas en Spring Cloud-instans.
 
Följande procedurer initierar både **åtgärdsgrupp** och **avisering** från alternativet Aviseringar i den vänstra **navigeringsfönstret** i en Spring Cloud-instans. (Proceduren kan också starta från sidan **Översikt över bildskärmen** i Azure-portalen.) 

Navigera från en resursgrupp till din Spring Cloud-instans. Välj **Aviseringar** i den vänstra rutan och välj sedan **Hantera åtgärder:**

![Sidan För att visa portalresursgrupp](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Ställ in åtgärdsgrupp

Om du vill påbörja proceduren för att initiera en ny **åtgärdsgrupp**väljer du **+ Lägg till åtgärdsgrupp**.

![Skärmdump portal Lägg till åtgärdsgrupp](media/alerts-action-groups/action-1.png)

På sidan **Lägg till åtgärdsgrupp:**

 1. Ange ett **åtgärdsgruppnamn** och **Kortnamn**.

 1. Ange **gruppen Prenumeration** och **Resurs**.

 1. Ange **åtgärdsnamn**.

 1. Välj **åtgärdstyp**.  Detta öppnar en annan ruta till höger för att definiera den åtgärd som ska vidtas vid aktiveringen.

 1. Definiera åtgärden med alternativen i den högra rutan.  Det här fallet använder e-postmeddelande.

 1. Klicka på **OK** i det högra åtgärdsfönstret.

 1. Klicka på **OK** i dialogrutan **Lägg till åtgärdsgrupp.** 

  ![Åtgärd för att definiera skärmbildsportal](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Ställ in avisering 

De föregående stegen skapade en **åtgärdsgrupp** som använder e-post. Du kan också använda telefonavisering, webhooks, Azure-funktioner, etc.  

Om du vill konfigurera en **avisering** **navigerar** du tillbaka till sidan Aviseringar och klickar på **Hantera varningsregler**.

  ![Avisering för skärmbildsportal](media/alerts-action-groups/alerts-2.png)

1. Välj **resurs** för aviseringen.

1. Klicka på **+ Ny varningsregel**.

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-3.png)

1. På sidan **Skapa regel** anger du **RESURSEN**.

1. **CONDITION-inställningen** innehåller många alternativ för att övervaka dina **Spring Cloud-resurser.**  Klicka på **Lägg till** för att öppna fönstret **Konfigurera signallogik.**

1. Välj ett villkor. I det här exemplet används **procent av system-CPU-användning**.

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-3-1.png)

1. Bläddra nedåt i fönstret **Konfigurera signallogik** för att ange **tröskelvärdet** för övervakning.

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-3-2.png)

1. Klicka på **Klar**.

Mer information om de villkor som är tillgängliga för övervakning finns i [Alternativ för användarportalmått](spring-cloud-concept-metrics.md#user-metrics-options).

 Klicka på **Markera åtgärdsgrupp**under **ÅTGÄRDER.** Välj den tidigare definierade **åtgärdsgruppen**i fönstret **ÅTGÄRDER** .

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-3-3.png) 

1. Bläddra nedåt och namnge varningsregeln under **VARNINGSINFORMATION.**

1. Ange **allvarlighetsgrad**.

1. Klicka på **Skapa aviseringsregel**.

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-3-4.png)

Kontrollera att den nya varningsregeln är aktiverad.

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-4.png)

En regel kan också skapas med hjälp av sidan **Mått:**

   ![Skärmbild portal ny varningsregel](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Nästa steg
* [Alternativ för användarportalmått](spring-cloud-concept-metrics.md#user-metrics-options)
* [Skapa och hantera åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [SMS-varningsbeteende i åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Självstudiekurs: Använda distribuerad spårning med Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
