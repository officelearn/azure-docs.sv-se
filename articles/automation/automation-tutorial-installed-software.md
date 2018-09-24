---
title: Identifiera vilken programvara som är installerad på dina datorer med Azure Automation | Microsoft Docs
description: Använd Inventering för att upptäcka vilken programvara som är installerad på datorerna i din miljö.
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: d8fb8ca84f68e6e8c503375b3b2fae1ce1eb72ba
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989217"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Identifiera vilken programvara som är installerad på dina datorer med och utan Azure

I den här självstudien får du lära dig hur du upptäcker vilken programvara som är installerad i din miljö. Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Aktivera lösningen
> * Publicera en virtuell Azure-dator
> * Publicera en virtuell dator utan Azure
> * Visa installerade program
> * Sök inventeringsloggar för installerad programvara

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto](automation-offering-get-started.md) för bevakaren samt runbook-flöden för åtgärd och bevakaraktivitet.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Först måste du aktivera Ändringsspårning och Inventering för den här självstudien. Om du redan har aktiverat **Ändringsspårning** kan du hoppa över det här steget.

Gå till ditt Automation-konto och välj **Inventering** under **KONFIGURATIONSHANTERING**.

Välj Log Analytics-arbetsytan och Automation-kontot och klicka på **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicering av konfigurationsbanderoll för Inventering](./media/automation-tutorial-installed-software/enableinventory.png)

Aktivera lösningen genom att konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas. Klicka sedan på **Aktivera**. Om fälten är nedtonade betyder det att en annan automatiseringslösning är aktiverad för den virtuella datorn. Samma arbetsyta och Automation-konto måste användas.

En [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som Inventering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren.
När lösningen har aktiverats flödar information om installerad programvara och ändringar på den virtuella datorn till Log Analytics.
Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

## <a name="onboard-a-vm"></a>Publicera en virtuell dator

I ditt Automation-konto går du till **Inventering** under **KONFIGURATIONSHANTERING**.

Välj **+ Lägg till virtuell Azure-dator**. Sidan **Virtuella datorer** öppnas där du kan välja en befintlig virtuell dator i listan. Välj den virtuella dator som du vill publicera. På sidan som öppnas klickar du på **Aktivera** för att aktivera lösningen på den virtuella datorn. Microsofts hanteringsagent distribueras till den virtuella datorn och konfigurerar agenten till att prata med den Log Analytics-arbetsyta som du konfigurerade när du aktiverade lösningen. Det kan ta ett par minuter att slutföra registreringen. I det här steget kan du välja en ny virtuell dator i listan och publicera en annan virtuell dator.

## <a name="onboard-a-non-azure-machine"></a>Publicera en virtuell dator utan Azure

Lägg till datorer utan Azure genom att installera agenten för [Windows](../log-analytics/log-analytics-agent-windows.md) eller [Linux](automation-linux-hrw-install.md), beroende på vilket operativsystem du har. När agenten är installerad går du till ditt Automation-konto och **Inventering** under **KONFIGURATIONSHANTERING**. När du klickar på **Hantera datorer** visas en lista med de datorer som rapporterar till Log Analytics-arbetsytan och som inte har någon aktiverad lösning. Välj lämpligt alternativ för din miljö.

* **Aktivera på alla tillgängliga datorer** – Med det här alternativet aktiveras lösningen på alla datorer som rapporterar till Log Analytics-arbetsytan just nu.
* **Aktivera på alla tillgängliga och framtida datorer** – Med det här alternativet aktiveras lösningen på alla datorer som rapporterar till Log Analytics-arbetsytan och därefter på alla framtida datorer som läggs till i arbetsytan.
* **Aktivera på valda datorer** – Med det här alternativet aktiveras lösningen endast på de datorer som du har markerat.

![Hantera datorer](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Visa installerade program

När ändringsspårningen och inventeringslösningen har aktiverats kan du visa resultatet på sidan **Inventering**.

Från ditt Automation-konto väljer du **Inventering** under **KONFIGURATIONSHANTERING**.

På sidan **Inventering** klickar du på fliken **Programvara**.

På fliken **Programvara** finns det en tabell som visar den programvara som har hittats. Programvaran grupperas efter programnamn och version.

Utförlig information för varje programpost visas i tabellen. Informationen omfattar programvarans namn, version, utgivare, senaste uppdateringstid (den senaste uppdateringstid som rapporterats av en dator i gruppen) och datorer (antalet datorer som har programvaran).

![Programvaruinventering](./media/automation-tutorial-installed-software/inventory-software.png)

Klicka på en rad om du vill visa egenskaperna för programposterna och namnen på datorerna som innehåller programvaran.

Om du vill söka efter en specifik programvara eller en grupp med programvara kan du söka i textrutan rakt ovanför programvarulistan.
Med filtret kan du söka utifrån programvarans namn, version eller utgivare.

Om du exempelvis söker efter ”Contoso” returneras all programvara med ett namn, utgivare eller version som innehåller ”Contoso”.

## <a name="search-inventory-logs-for-installed-software"></a>Sök inventeringsloggar för installerad programvara

Inventering genererar loggdata som skickas till Log Analytics. Om du vill söka i loggarna genom att köra frågor väljer du **Log Analytics** högst upp i fönstret **Inventering**.

Inventeringsdata lagras under typen **ConfigurationData**.
Följande exempelfråga i Log Analytics returnerar inventeringsresultaten där utgivaren är ”Microsoft Corporation”.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Mer information om hur du kör och söker efter loggfiler i Log Analytics finns [Azure Log Analytics](../log-analytics/log-analytics-queries.md).

### <a name="single-machine-inventory"></a>Inventering för en enda dator

Om du vill se programvaruinventeringen för en enda dator kan du få åtkomst till Inventering från resurssidan för den virtuella Azure-datorn eller använda Log Analytics för att filtrera fram motsvarande dator.
Följande Log Analytics-exempelfråga returnerar en lista över programvara för en dator med namnet ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att visa programvaruinventering, till exempel hur du:

> [!div class="checklist"]
> * Aktivera lösningen
> * Publicera en virtuell Azure-dator
> * Publicera en virtuell dator utan Azure
> * Visa installerade program
> * Sök inventeringsloggar för installerad programvara

Fortsätt till översikten över lösningen för ändringsspårning och inventering för att läsa mer om det.

> [!div class="nextstepaction"]
> [Ändringshantering och inventeringslösning](automation-change-tracking.md)