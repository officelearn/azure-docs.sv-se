---
title: "Kontinuerlig övervakning av din pipeline för versionen av DevOps med VSTS och Azure Application Insights | Microsoft Docs"
description: "Innehåller instruktioner för att snabbt konfigurera kontinuerlig övervakning med Application Insights"
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: article
manager: carmonm
ms.openlocfilehash: 5bfbdd0033f966422a84071a694845627827f016
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Lägg till kontinuerlig övervakning i din version pipeline

Visual Studio Team Services VSTS () integreras med Azure Application Insights för att tillåta kontinuerlig övervakning av din DevOps versionen pipeline genom utvecklingslivscykeln programvara. 

VSTS stöder nu kontinuerlig övervakning innebär versionen pipelines kan införliva övervakningsdata från Application Insights och andra Azure-resurser. När en avisering för Application Insights upptäcks distributionen kan förbli gated eller återställas tillbaka förrän varningen har åtgärdats. Om alla kontroller har klarat kan distributioner fortsätta automatiskt från test till produktion utan att behöva manuella åtgärder. 

## <a name="configure-continuous-monitoring"></a>Konfigurera en löpande övervakning

1. Välj en befintlig VSTS.

2. Hovra över **bygga och släpper** > Välj **versioner** > klickar du på den **plustecknet** > **skapa versionen definition** > Sök efter **övervakning** > **Azure App Service-distributionen med kontinuerlig övervakning.**

   ![Ny VSTS versionen Definition](.\media\app-insights-continuous-monitoring\001.png)

3. Klicka på **tillämpas.**

4. Bredvid rött utropstecken markerar du texten i blått till **visa aktiviteter för miljön.**

   ![Visa uppgifter i miljön](.\media\app-insights-continuous-monitoring\002.png)

   En konfigurationen visas, använder du följande tabell för att fylla i fälten inkommande.

    | Parameter        | Värde |
   | ------------- |:-----|
   | **Namn**      | Namn som beskriver versionen definition miljö |
   | **Azure-prenumeration** | Listrutan fylls med Azure-prenumerationer som länkas till VSTS-kontot|
   | **Apptjänst namn** | Manuell inmatning av ett nytt värde kan krävas för det här fältet för andra alternativ |
   | **Resursgrupp**    | Listrutan fylls med tillgängliga resursgrupper |
   | **Resursnamnet för Application Insights** | Listrutan fylls med alla Application Insights-resurser som motsvarar den tidigare valda resursgruppen.

5. Välj **konfigurera Application Insights aviseringar**

6. Standard Varningsregler, Välj **spara** > Ange en kommentar > klickar du på **OK**

## <a name="modify-alert-rules"></a>Ändra Varningsregler

1. Om du vill ändra de fördefinierade aviseringsinställningarna, klicka på rutan med **ellipserna...**  till höger om **Varna regler.**

   (Out of box fyra Varningsregler finns: tillgänglighet, misslyckade begäranden, serversvarstid, undantag.)

2. Klicka på listrutan symbolen bredvid **tillgänglighet.**

3. Ändra tillgängligheten **tröskelvärdet** så att de uppfyller dina krav på tjänsten.

   ![Ändra varning](.\media\app-insights-continuous-monitoring\003.png)

4. Välj **OK** > **spara** > Ange en kommentar > klickar du på **OK.**

## <a name="add-deployment-conditions"></a>Lägg till villkor för distribution

1. Klicka på **Pipeline** > Välj den **före** eller **efter distributionen villkor** symbol beroende på de steg som kräver en kontinuerlig övervakning gate.

   ![Före distributionen villkor](.\media\app-insights-continuous-monitoring\004.png)

2. Ange **Gates** till **aktiverad** > **godkännande gates**> klickar du på **Lägg till.**

3. Välj **Azure-Monitor** (det här alternativet ger dig möjlighet att åtkomst aviseringar både från Azure-Monitor och Application Insights)

    ![Azure Monitor](.\media\app-insights-continuous-monitoring\005.png)

4. Ange en **Gates timeout** värde.

5. Ange en **samplingsintervall.**

## <a name="deployment-gate-status-logs"></a>Distributionsloggar gate status

När du har lagt till distribution gates skyddar en avisering i Application Insights, vilket överskrider din tidigare definierade tröskelvärdet distributionen från oönskade versionen befordran. När aviseringen har lösts fortsätta distributionen automatiskt.

Om du vill se det här beteendet, Välj **versioner** > högerklickar du på versionen namnet **öppna** > **loggar.**

![Logs](.\media\app-insights-continuous-monitoring\006.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om VSTS bygga och versionen försök med dessa [Snabbstart.](https://docs.microsoft.com/vsts/build-release/)
