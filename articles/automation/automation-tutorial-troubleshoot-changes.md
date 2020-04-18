---
title: Felsöka ändringar på en virtuell Azure-dator | Microsoft Docs
description: Använd Ändringsspårning för att felsöka ändringar i en virtuell Azure-dator.
services: automation
ms.subservice: change-inventory-management
keywords: change, tracking, automation
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 89f5e00c75b6b85c9a14de02504136907cde62b5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604700"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Felsöka ändringar i miljön

I den här självstudien får du lära dig att felsöka ändringar i en virtuell Azure-dator. Genom att aktivera ändringsspårning kan du spåra ändringar av program-, fil-, Linux-demoner, Windows-tjänster och Windows-registernycklar på dina datorer.
Om du identifierar dessa konfigurationsändringar kan du få hjälp med att precisera driftproblem i miljön.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Publicera en virtuell dator för spårning av ändringar och lager
> * Sök i ändringsloggar efter stoppade enheter
> * Konfigurera spårning av ändringar
> * Aktivera aktivitetslogganslutning
> * Utlösa en händelse
> * Visa ändringar
> * Konfigurera varningar

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto](automation-offering-get-started.md) som innehåller watcher- och action-runbooks och Watcher-uppgiften.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Först måste du aktivera Ändra spårning och lager för din virtuella dator för den här självstudien. Om du redan har aktiverat någon annan automatiseringslösning för en virtuell dator kan du hoppa över det här steget.

1. Välj **Virtuella datorer** på menyn till vänster och välj en virtuell dator i listan.
1. Välj **Lager** under **Operationer**på den vänstra menyn . Sidan Lager öppnas.

![Aktivera ändring](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

Konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas och klicka på **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

En [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som Inventering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Under introduktionen etableras den virtuella datorn med Log Analytics-agenten för Windows och en Hybrid Runbook Worker.
Agenten används för att kommunicera med den virtuella datorn och få information om installerad programvara.

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren.
När lösningen har aktiverats flödar information om installerad programvara och ändringar på den virtuella datorn till Azure Monitor-loggar.
Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Använda ändringsspårning i Azure Monitor-loggar

Ändringsspårning genererar loggdata som skickas till Azure Monitor-loggar.
Om du vill söka i loggarna genom att köra frågor väljer du **Logga Analytics** högst upp på sidan Ändra spårning.
Data för ändringsspårning `ConfigurationChange`lagras under typen .
Följande exempel på Log Analytics-fråga returnerar alla Windows-tjänster som har stoppats.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Mer information om hur du kör och söker efter loggfiler i Azure Monitor-loggar finns i [Azure Monitor-loggar](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Konfigurera spårning av ändringar

Med ändringsspårning kan du spåra ändringar i konfigurationen på den virtuella datorn. I följande anvisningar ser du hur du konfigurerar spårning av registernycklar och filer.

När du ska välja vilka filer och registernycklar du ska samla in och spåra ska du välja **Redigera inställningar** överst på sidan Ändringsspårning.

> [!NOTE]
> Inventering och ändringsspårning använder samma samlingsinställningar och konfigureras på arbetsytenivå.

På sidan Konfiguration av arbetsyta lägger du till de Windows-registernycklar, Windows-filer eller Linux-filer som ska spåras, som beskrivs i de följande tre avsnitten.

### <a name="add-a-windows-registry-key"></a>Lägga till en Windows-registernyckel

1. På fliken **Windows-registret** väljer du **Lägg till**. 

1. På sidan Lägg till Windows-registret för ändringsspårning anger du informationen för nyckeln att spåra och klickar på **Spara**

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Windows-registernyckel   | Sökvägen för att söka efter filen Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="add-a-windows-file"></a>Lägga till en Windows-fil

1. På fliken **Windows-filer** väljer du **Lägg till**. 

1. På sidan Lägg till Windows-fil för ändringsspårning anger du informationen för filen eller katalogen för att spåra och klickar på **Spara**

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Ange sökväg     | Sökvägen för att söka efter filen Till exempel: "c:\temp\\\*.txt"<br>Du kan också använda miljövariabler som "%winDir%\System32\\\*.*"         |
|Rekursion     | Avgör om rekursion används när du letar efter objektet som ska spåras.        |
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: **True** eller **False**.|

### <a name="add-a-linux-file"></a>Lägga till en Linux-fil

1. På fliken **Linux-filer** väljer du **Lägg till**. 

1. På sidan Lägg till Linux-fil för ändringsspårning anger du informationen för filen eller katalogen för att spåra och klickar på **Spara**.

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
|Ladda upp filinnehåll för alla inställningar| Aktiverar eller inaktiverar uppladdning av filinnehåll vid spårade ändringar. Tillgängliga alternativ: True eller False.|

   > [!NOTE]
   > Alternativet **Hantera länkar** rekommenderas inte. Hämtning av filinnehåll stöds inte.

## <a name="enable-activity-log-connection"></a>Aktivera aktivitetslogganslutning

Från sidan Ändringsspårning på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. Den här uppgiften öppnar sidan Azure-aktivitetslogg. Klicka på **Anslut** om du vill ansluta ändringsspårning till Azure-aktivitetsloggen för den virtuella datorn.

När den här inställningen är aktiverad går du till sidan Översikt för din virtuella dator och väljer **Stoppa** för att stoppa din virtuella dator. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den har frigjorts väljer du **Starta** för att starta om din virtuella dator.

När en virtuell dator stoppas och startas loggar den virtuella datorn en händelse i aktivitetsloggen. Gå tillbaka till sidan Ändringsspårning. Välj fliken **Händelser** längst ned på sidan. Efter ett tag visas händelserna i diagrammet och tabellen. Som i föregående steg kan du välja varje händelse för att visa detaljerad information om händelsen.

![Visa information om ändringar i portalen](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Visa ändringar

När ändringsspårningen och inventeringslösningen har aktiverats kan du visa resultatet på sidan Ändringsspårning.

Från din virtuella dator väljer du **Ändringsspårning** under **ÅTGÄRDER**.

![Skärmbild som visar en lista över ändringar i den virtuella datorn](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

Diagrammet visar ändringar som har skett över tid.
När du har lagt till en aktivitetslogganslutning visar linjediagrammet högst upp händelser i Azure-aktivitetsloggen.
Varje rad med stapeldiagram representerar en annan spårningsbar ändringstyp.
Typerna är Linux-daemons, filer, Windows-registernycklar, programvara och Windows-tjänster.
På fliken Ändra visas information för de ändringar som visas i visualiseringen i fallande tidsordning för när ändringen skedde (senaste först).
På fliken **Händelser** visar tabellen de anslutna aktivitetslogghändelserna och deras motsvarande information med det senaste först.

I resultatet kan du se att det fanns flera ändringar i systemet, däribland ändringar i tjänster och programvara. Du kan använda filtren högst upp på sidan för att filtrera resultatet efter **Typ av ändring** eller efter ett tidsintervall.

Välj en **Ändring av Windows-tjänster.** I det här valet öppnas sidan Ändra information med information om ändringen och värdena före och efter ändringen. I den här instansen stoppades tjänsten Software Protection.

![Visa information om ändringar i portalen](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurera varningar

Det kan vara användbart att granska ändringar i Azure-portalen, men det är bättre att kunna få aviseringar när en ändring sker, till exempel en stoppad tjänst.

Om du vill lägga till en avisering för en stoppad tjänst går du till Azure-portalen och sedan till **Övervaka**. Under **Delade tjänster** väljer du sedan **Aviseringar** och klickar på **+ Ny aviseringsregel**

Klicka på **Markera** för att välja en anslutning. På sidan Välj en resurs väljer du **Logga Analytics** på den **nedrullningsbara menyn Filter efter resurstyp.** Välj Log Analytics-arbetsytan och välj sedan **Klar**.

![Välj en resurs](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Klicka på **Lägg till villkor** på sidan Konfigurera signallogiken väljer du **Anpassad loggsökning** i tabellen. Ange sedan följande fråga i textrutan Sökfråga:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Den här frågan returnerar de datorer där W3SVC-tjänsten stoppades i det angivna tidsintervallet.

Under **Aviseringslogik** går du till **Tröskelvärde** och anger **0**. När du är klar väljer du **Klar**.

![Konfigurera signallogiken](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

Välj **Skapa ny** under **Åtgärdsgrupper**. En åtgärdsgrupp är en grupp av åtgärder som kan användas i flera aviseringar. Dessa åtgärder kan inkludera, men är inte begränsade till, e-postmeddelanden, runbooks, webhooks och mycket mer. Mer information om åtgärdsgrupper finns i [Skapa och hantera åtgärdsgrupper](../azure-monitor/platform/action-groups.md).

Under **Aviseringsinformation** anger du ett namn och en beskrivning för aviseringen. Ställ in **Allvarlighetsgrad** på **Information (Sev 2)**, **Varning (Sev 1)** eller **Kritisk (Sev 0)**.

I rutan **Åtgärdsgruppnamn** anger du ett namn för aviseringen och ett kortnamn. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

Under **Åtgärder** anger du ett namn för åtgärden, till exempel **E-postadministratörer**. Under **ÅTGÄRDSTYP**, välj **e-post/SMS/Push/röst**. Under **INFORMATION** väljer du **Redigera detaljer**.

![Lägg till åtgärdsgrupp](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

I rutan e-post/SMS/Push/röst, ange ett namn. Välj kryssrutan **e-post** och ange sedan en giltig e-postadress. Klicka på **OK** i fönstret och klicka sedan på **OK** på sidan Lägg till åtgärdsgrupp.

För att anpassa ämnesraden för e-postaviseringen går du till **Skapa regel** och **Anpassa åtgärder**. Där väljer du **E-postämne**. När du är klar väljer du **Skapa varningsregel**. Varningen berättar när en distribution lyckas och vilka datorer som var en del av denna uppdaterade distributionskörning.

Följande bild är ett exempel på ett e-postmeddelande som tas emot när W3SVC-tjänsten stoppas.

![e-post](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Publicera en virtuell dator för spårning av ändringar och lager
> * Sök i ändringsloggar efter stoppade enheter
> * Konfigurera spårning av ändringar
> * Aktivera aktivitetslogganslutning
> * Utlösa en händelse
> * Visa ändringar
> * Konfigurera varningar

Fortsätt till översikten över lösningen för ändringsspårning och inventering för att läsa mer om det.

> [!div class="nextstepaction"]
> [Ändringshantering och inventeringslösning](automation-change-tracking.md)

