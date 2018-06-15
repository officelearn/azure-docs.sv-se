---
title: Felsöka ändringar på en virtuell Azure-dator | Microsoft Docs
description: Använd Ändringsspårning för att felsöka ändringar i en virtuell Azure-dator.
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: change, tracking, automation
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/28/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 62d34f82749900e161bebdb7a1a8d470b2e85bbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054699"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Felsöka ändringar i miljön

I den här självstudien får du lära dig att felsöka ändringar i en virtuell Azure-dator. När du aktiverar Ändringsspårning kan du spåra ändringar i program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer.
Om du identifierar dessa konfigurationsändringar kan du få hjälp med att precisera driftproblem i miljön.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för spårning av ändringar och lager
> * Sök i återställningsloggar efter stoppade enheter
> * Konfigurera spårning av ändringar
> * Aktivera aktivitetslogganslutning
> * Utlösa en händelse
> * Visa ändringar

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto](automation-offering-get-started.md) för bevakaren och åtgärdsrunbooks och bevakaraktiviteten.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Först måste du aktivera Ändringsspårning och inventering för dina virtuella datorer för att genomföra den här självstudien. Om du redan har aktiverat någon annan automatiseringslösning för en virtuell dator kan du hoppa över det här steget.

1. På menyn till vänster väljer du **Virtuella datorer** och väljer en virtuell dator på listan
1. På den vänstra menyn, under avsnittet **ÅTGÄRDER** klickar du på **Inventering**. Sidan **Ändringsspårning** öppnas.

![Aktivera ändring](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) Skärmen **Ändringsspårning** öppnas. Konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas och klicka på **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

En [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som Inventering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Under publiceringen etableras den virtuella datorn med MMA och Hybrid Worker.
Den här agenten används för att kommunicera med den virtuella datorn och hämta information om installerad programvara.

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren.
När lösningen har aktiverats flödar information om installerad programvara och ändringar på den virtuella datorn till Log Analytics.
Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

## <a name="using-change-tracking-in-log-analytics"></a>Använda Ändringsspårning i Log Analytics

Ändringsspårning genererar loggdata som skickas till Log Analytics. Om du vill söka i loggarna genom att köra frågor väljer du **Log Analytics** högst upp i fönstret **Ändringsspårning**.
Ändringsspårningsdata lagras under typen **ConfigurationChange**. Följande exempel på Log Analytics-fråga returnerar alla Windows-tjänster som har stoppats.

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Mer information om hur du kör och söker efter loggfiler i Log Analytics finns [Azure Log Analytics](https://docs.loganalytics.io/index).

## <a name="configure-change-tracking"></a>Konfigurera spårning av ändringar

Med ändringsspårning kan du spåra ändringar i konfigurationen på den virtuella datorn. I följande anvisningar ser du hur du konfigurerar spårning av registernycklar och filer.
 
När du ska välja vilka filer och registernycklar du ska samla in och spåra ska du välja **Redigera inställningar** överst på sidan **Ändringsspårning**.

> [!NOTE]
> Inventering och ändringsspårning använder samma samlingsinställningar och konfigureras på arbetsytenivå.

I fönstret **Arbetsytekonfiguration** lägger du till Windows-registernycklarna, Windows-filerna eller Linux-filerna som ska spåras enligt beskrivningen i nästa tre avsnitt.

### <a name="add-a-windows-registry-key"></a>Lägga till en Windows-registernyckel

1. På fliken **Windows-registret** väljer du **Lägg till**.
    Fönstret **Lägg till Windows-register för ändringsspårning** öppnas.

3. I **Lägg till Windows-register för ändringsspårning** anger du informationen för nyckeln som ska spåras och klickar på **Spara**

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Windows-registernyckel   | Sökvägen för att söka efter filen Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>Lägga till en Windows-fil

1. På fliken **Windows-filer** väljer du **Lägg till**. Fönstret **Lägg till Windows-fil för ändringsspårning** öppnas.

1. I **Lägg till Windows-fil för ändringsspårning** anger du informationen för filen eller katalogen som ska spåras och klickar på **Spara**

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: ”c:\temp\myfile.txt”       |

### <a name="add-a-linux-file"></a>Lägga till en Linux-fil

1. På fliken **Linux-filer** väljer du **Lägg till**. Fönstret **Lägg till Linux-fil för ändringsspårning** öppnas.

1. I **Lägg till Linux-fil för ändringsspårning** anger du informationen för filen eller katalogen som ska spåras och klickar på **Spara**

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: ”/etc/*.conf”       |
|Sökvägstyp     | Typ av objekt som ska spåras, möjliga värden är Fil och Katalog        |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Använda Sudo     | Den här inställningen styr om sudo ska användas vid sökningen efter objektet.         |
|Länkar     | Den här inställningen styr hur symboliska länkar ska hanteras när de passerar kataloger.<br> **Ignorera** – Ignorerar symboliska länkar och inkluderar inte refererade filer/kataloger<br>**Följ** – Följer de symboliska länkarna under rekursion och inkluderar refererade filer/kataloger<br>**Hantera** – Följer de symboliska länkarna och tillåter ändring av behandling av returnerat innehåll      |

   > [!NOTE]   
   > Länkalternativet ”Hantera” rekommenderas inte. Hämtning av filinnehåll stöds inte.

## <a name="enable-activity-log-connection"></a>Aktivera aktivitetslogganslutning

Från sidan **Ändringsspårning** på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. Den här uppgiften öppnar sidan **Azure-aktivitetslogg**. Välj **Anslut** för att ansluta Ändringsspårning till Azure-aktivitetsloggen för din virtuella dator.

När den här inställningen är aktiverad går du till sidan **Översikt** för din virtuella dator och väljer **Stoppa** för att stoppa din virtuella dator. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den har frigjorts väljer du **Starta** för att starta om din virtuella dator.

När en virtuell dator stoppas och startas loggar den virtuella datorn en händelse i aktivitetsloggen. Gå tillbaka till sidan **Ändringsspårning**. Välj fliken **Händelser** längst ned på sidan. Efter ett tag visas händelserna i diagrammet och tabellen. Som i föregående steg kan du välja varje händelse för att visa detaljerad information om händelsen.

![Visa information om ändringar i portalen](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Visa ändringar

När ändringsspårningen och inventeringslösningen har aktiverats kan du visa resultatet på sidan **Ändringsspårning**.

Från din virtuella dator väljer du **Ändringsspårning** under **ÅTGÄRDER**.

![Skärmbild som visar en lista över ändringar i den virtuella datorn](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

Diagrammet visar ändringar som har skett över tid.
När du har lagt till en aktivitetslogganslutning visar linjediagrammet högst upp händelser i Azure-aktivitetsloggen.
Varje rad med stapeldiagram representerar en annan spårningsbar ändringstyp.
Typerna är Linux-daemons, filer, Windows-registernycklar, programvara och Windows-tjänster.
På fliken Ändra visas information för de ändringar som visas i visualiseringen i fallande tidsordning för när ändringen skedde (senaste först).
På fliken **Händelser** visar tabellen de anslutna aktivitetslogghändelserna och deras motsvarande information med det senaste först.

I resultatet kan du se att det fanns flera ändringar i systemet, däribland ändringar i tjänster och programvara. Du kan använda filtren högst upp på sidan för att filtrera resultatet efter **Typ av ändring** eller efter ett tidsintervall.

Välj en **WindowsServices**-ändring så öppnas fönstret **Ändra information**. Fönstret Ändra information visar information om ändringen och värdena före och efter ändringen. I den här instansen stoppades tjänsten Software Protection.

![Visa information om ändringar i portalen](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för spårning av ändringar och lager
> * Sök i återställningsloggar efter stoppade enheter
> * Konfigurera spårning av ändringar
> * Aktivera aktivitetslogganslutning
> * Utlösa en händelse
> * Visa ändringar

Fortsätt till översikten över lösningen för ändringsspårning och inventering för att läsa mer om det.

> [!div class="nextstepaction"]
> [Ändringshantering och inventeringslösning](automation-change-tracking.md)
