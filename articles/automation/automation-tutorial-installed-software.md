---
title: "Identifiera vilken programvara som är installerad på dina datorer med Azure Automation | Microsoft Docs"
description: "Använd Inventering för att upptäcka vilken programvara som är installerad på datorerna i din miljö."
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: bdd638d0612a8ddee1a0ddb4fd4579f8da14b887
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Identifiera vilken programvara som är installerad på dina datorer med och utan Azure

I den här självstudien får du lära dig hur du upptäcker vilken programvara som är installerad i din miljö. Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för spårning av ändringar och lager
> * Visa installerade program
> * Sök inventeringsloggar för installerad programvara

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto](automation-offering-get-started.md) för bevakaren och åtgärdsrunbooks och bevakaraktiviteten.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Först måste du aktivera Ändringsspårning och inventering för dina virtuella datorer för att genomföra den här självstudien. Om du redan har aktiverat någon annan automatiseringslösning för en virtuell dator kan du hoppa över det här steget.

1. På menyn till vänster väljer du **Virtuella datorer** och väljer en virtuell dator på listan
2. På den vänstra menyn, under avsnittet **Åtgärder** klickar du på **Inventering**. Sidan **Ändringsspårning och inventering** öppnas.

Verifieringen utförs för att fastställa om inventering är aktiverat för den här virtuella datorn.
Verifieringen söker efter en Log Analytics-arbetsyta och ett länkat Automation-konto, och om lösningen är i arbetsytan.

En [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som Inventering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Verifieringsprocessen kontrollerar också om den virtuella datorn har etablerats med MMA och Hybrid Worker.
Den här agenten används för att kommunicera med den virtuella datorn och hämta information om installerad programvara.
Verifieringsprocessen kontrollerar också om den virtuella datorn har etablerats med MMA och Automation Hybrid Runbook Worker.

Om förutsättningarna inte uppfylls visas en banderoll som ger dig möjlighet att aktivera lösningen.

![Publicering av konfigurationsbanderoll för Inventering](./media/automation-tutorial-installed-software/enableinventory.png)

Klicka på banderollen för att aktivera lösningen.
Om några av följande krav saknades efter verifieringen läggs de till automatiskt:

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-arbetsyta
* [Automation](./automation-offering-get-started.md)
* En [Hybrid runbook worker](./automation-hybrid-runbook-worker.md) aktiveras på den virtuella datorn

Skärmen **Ändringsspårning och inventering** öppnas. Konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas och klicka på **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

![Aktivera fönstret Lösning för ändringsspårning](./media/automation-tutorial-installed-software/installed-software-enable.png)

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren.
När lösningen har aktiverats flödar information om installerad programvara och ändringar på den virtuella datorn till Log Analytics.
Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

## <a name="view-installed-software"></a>Visa installerade program

När ändringsspårningen och inventeringslösningen har aktiverats kan du visa resultatet på sidan **Inventering**.

Från din virtuella dator väljer du **Inventering** under **ÅTGÄRDER**.

På sidan **Inventering** klickar du på fliken **Programvara**.

På fliken **Programvara** finns det en tabell som anger programvaran som har hittats. Programvaran grupperas efter programnamn och version.

Utförlig information för varje programpost visas i tabellen. Informationen omfattar programvarans namn, version, utgivare, senaste uppdateringstid (den senaste uppdateringstid som rapporterats av en dator i gruppen) och datorer (antalet datorer som har programvaran).

![Programvaruinventering](./media/automation-tutorial-installed-software/inventory-software.png)

Klicka på en rad om du vill visa egenskaperna för programposterna och namnen på datorerna som innehåller programvaran.

Om du vill söka efter en specifik programvara eller en grupp med programvara kan du söka i textrutan rakt ovanför programvarulistan.
Med filtret kan du söka utifrån programvarans namn, version eller utgivare.

Om du exempelvis söker efter ”Contoso” returneras all programvara med ett namn, utgivare eller version som innehåller ”Contoso”.

## <a name="search-inventory-logs-for-installed-software"></a>Sök inventeringsloggar för installerad programvara

Inventering genererar loggdata som skickas till Log Analytics. Om du vill söka i loggarna genom att köra frågor väljer du **Log Analytics** högst upp i fönstret **Inventering**.

Inventeringsdata lagras under typen **ConfigurationData**.
Följande Log Analytics-exempelfråga returnerar utgivarna som innehåller ”Microsoft” och antalet programposter (grupperade efter SoftwareName och Computer) för varje utgivare.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Mer information om hur du kör och söker efter loggfiler i Log Analytics finns [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventering för en enda dator

Om du vill se programvaruinventeringen för en enda dator kan du få åtkomst till Inventering från resurssidan för den virtuella Azure-datorn eller använda Log Analytics för att filtrera fram motsvarande dator. Följande Log Analytics-exempelfråga returnerar en lista över programvara för en dator med namnet ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att visa programvaruinventering, till exempel hur du:

> [!div class="checklist"]
> * Publicera en virtuell dator för spårning av ändringar och lager
> * Visa installerade program
> * Sök inventeringsloggar för installerad programvara

Fortsätt till översikten över lösningen för ändringsspårning och inventering för att läsa mer om det.

> [!div class="nextstepaction"]
> [Ändringshantering och inventeringslösning](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)