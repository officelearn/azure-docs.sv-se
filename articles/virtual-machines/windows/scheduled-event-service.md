---
title: Övervaka schemalagda händelser för dina virtuella Windows-datorer i Azure
description: Lär dig hur du övervakar dina virtuella Azure-datorer för schemalagda händelser.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073320"
---
# <a name="monitoring-scheduled-events"></a>Övervaka schemalagda händelser

Uppdateringar tillämpas på olika delar av Azure varje dag, för att hålla tjänsterna som körs på dem säkra och uppdaterade. Förutom planerade uppdateringar kan oplanerade händelser också inträffa. Om till exempel maskinvaruförsämring eller fel upptäcks kan Azure-tjänster behöva utföra oplanerat underhåll. Med hjälp av direktmigrering, minne bevara uppdateringar och i allmänhet hålla en strikt bar på effekterna av uppdateringar, i de flesta fall dessa händelser är nästan transparent för kunder, och de har ingen inverkan eller på sin höjd orsaka några sekunder av virtuell dator frysa. Men för vissa program kan även några sekunder av frysning av virtuella datorer orsaka en inverkan. Att veta i förväg om kommande Azure-underhåll är viktigt för att säkerställa den bästa upplevelsen för dessa program. [Tjänsten Schemalagda händelser](scheduled-events.md) ger dig ett programmatiskt gränssnitt som ska meddelas om kommande underhåll och gör att du smidigt kan hantera underhållet. 

I den här artikeln visar vi hur du kan använda schemalagda händelser som ska meddelas om underhållshändelser som kan påverka dina virtuella datorer och skapa grundläggande automatisering som kan hjälpa till med övervakning och analys.


## <a name="routing-scheduled-events-to-log-analytics"></a>Routning av schemalagda händelser till Log Analytics

Schemalagda händelser är tillgängliga som en del av [Azure Instance Metadata Service](instance-metadata-service.md), som är tillgänglig på alla virtuella Azure-datorer. Kunder kan skriva automatisering för att fråga slutpunkten för sina virtuella datorer för att hitta schemalagda underhållsmeddelanden och utföra åtgärder, som att spara tillståndet och ta den virtuella datorn ur rotation. Vi rekommenderar fastighetsautomation för att registrera schemalagda händelser så att du kan ha en granskningslogg över Azure-underhållshändelser. 

I den här artikeln kommer vi att gå igenom hur du fångar underhåll schemalagda händelser till Log Analytics. Sedan kommer vi att utlösa några grundläggande meddelandeåtgärder, som att skicka ett e-postmeddelande till ditt team och få en historisk bild av alla händelser som har påverkat dina virtuella datorer. För händelseaggregering och automatisering använder vi [Log Analytics,](/azure/azure-monitor/learn/quick-create-workspace)men du kan använda vilken övervakningslösning som helst för att samla in dessa loggar och utlösa automatisering.

![Diagram som visar händelselivscykeln](./media/notifications/events.png)

## <a name="prerequisites"></a>Krav

I det här exemplet måste du skapa en [virtuell Windows-dator i en tillgänglighetsuppsättning](tutorial-availability-sets.md). Schemalagda händelser ger meddelanden om ändringar som kan påverka någon av de virtuella datorerna i din tillgänglighetsuppsättning, Molntjänst, Skalningsuppsättning för virtuella datorer eller fristående virtuella datorer. Vi kommer att köra en [tjänst](https://github.com/microsoft/AzureScheduledEventsService) som av omröstningar för schemalagda händelser på en av de virtuella datorer som kommer att fungera som en insamlare, för att få händelser för alla andra virtuella datorer i tillgänglighetsuppsättningen.    

Ta inte bort gruppresursgruppen i slutet av självstudien.

Du måste också [skapa en Log Analytics-arbetsyta](/azure/azure-monitor/learn/quick-create-workspace) som vi använder för att samla information från de virtuella datorerna i tillgänglighetsuppsättningen.

## <a name="set-up-the-environment"></a>Konfigurera miljön

Du bör nu ha två inledande virtuella datorer i en tillgänglighetsuppsättning. Nu måste vi skapa en 3: e VM, som kallas myCollectorVM, i samma tillgänglighet set. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Hämta installationen .zip-filen för projektet från [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Anslut till **myCollectorVM** och kopiera ZIP-filen till den virtuella datorn och extrahera alla filer. Öppna en PowerShell-prompt på den virtuella datorn. Flytta din fråga till `SchService.ps1`mappen som `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`innehåller , till exempel: och konfigurera tjänsten.

```powershell
.\SchService.ps1 -Setup
```

Starta tjänsten.

```powershell
.\SchService.ps1 -Start
```

Tjänsten startar nu avsökning var 10:e sekund för schemalagda händelser och godkänner händelserna för att påskynda underhållet.  Freeze, Reboot, Redeploy och Preempt är de händelser som fångas upp av Schemahändelser.   Observera att du kan utöka skriptet så att det utlöser vissa åtgärder innan du godkänner händelsen.

Verifiera tjänststatusen och kontrollera att den körs.

```powershell
.\SchService.ps1 -status  
```

Detta bör `Running`återvända .

Tjänsten startar nu avsökning var 10:e sekund för schemalagda händelser och godkänner händelserna för att påskynda underhållet.  Freeze, Reboot, Redeploy och Preempt är de händelser som fångas upp av Schemahändelser. Du kan utöka skriptet så att det utlöser vissa åtgärder innan du godkänner händelsen.

När någon av ovanstående händelser fångas in av tjänsten Schemalägg händelse loggas den i händelsestatus för programhändelseloggen, händelsetyp, resurser (virtuella datornamn) och inteförinnan (minsta uppsägningstid). Du kan hitta händelserna med ID 1234 i programhändelseloggen.

När tjänsten har konfigurerats och startats loggas händelser i Windows Application-loggarna.   Om du vill verifiera detta fungerar startar du om en av de virtuella datorerna i tillgänglighetsuppsättningen och du bör se en händelse som loggas i Loggboken i Windows Loggar > Programlogg som visar att den virtuella datorn startas om. 

![Skärmbild av loggboken.](./media/notifications/event-viewer.png)

När händelser fångas in av tjänsten Schemahändelse loggas den i programmet även med händelsestatus, händelsetyp, resurser (VM-namn) och NotBefore (minsta uppsägningstid). Du kan hitta händelserna med ID 1234 i programhändelseloggen.

> [!NOTE] 
> I det här exemplet var de virtuella datorerna i en tillgänglighetsuppsättning, vilket gjorde det möjligt för oss att ange en enda virtuell dator som insamlaren för att lyssna och dirigera schemalagda händelser till vår logganalys fungerar utrymme. Om du har fristående virtuella datorer kan du köra tjänsten på alla virtuella datorer och sedan ansluta dem individuellt till din logganalysarbetsyta.
>
> För vår uppsättning, valde vi Windows, men du kan designa en liknande lösning på Linux.

När som helst kan du stoppa/ta bort den `–stop` `–remove`schemalagda händelsetjänsten med hjälp av växlarna och .

## <a name="connect-to-the-workspace"></a>Ansluta till arbetsytan


Vi vill nu ansluta en Log Analytics Workspace till insamlaren VM. Log Analytics-arbetsytan fungerar som en lagringsplats och vi konfigurerar händelseloggsamling för att fånga in programloggarna från insamlarens virtuella dator. 

 Om du vill dirigera schemalagda händelser till händelseloggen, som sparas som programlogg av vår tjänst, måste du ansluta den virtuella datorn till logganalysarbetsytan.  
 
1. Öppna sidan för arbetsytan som du skapade.
1. Under **Anslut till en datakälla** väljer du Virtuella **Azure-datorer (VMs).**

    ![Ansluta till en virtuell dator som datakälla](./media/notifications/connect-to-data-source.png)

1. Sök efter och välj **myCollectorVM**. 
1. På den nya sidan för **myCollectorVM**väljer du **Anslut**.

Detta installerar [Microsoft Monitoring-agenten](/azure/virtual-machines/extensions/oms-windows) på den virtuella datorn. Det tar några minuter att ansluta den virtuella datorn till arbetsytan och installera tillägget. 

## <a name="configure-the-workspace"></a>Konfigurera arbetsytan

1. Öppna sidan för arbetsytan och välj **Avancerade inställningar**.
1. Välj **Data** på den vänstra menyn och välj sedan **Windows-händelseloggar**.
1. I **Samla in från följande händelseloggar**börjar du skriva *program* och välj sedan **Program** i listan.

    ![Välj avancerade inställningar](./media/notifications/advanced.png)

1. Lämna **FEL,** **VARNING**och **INFORMATION** markerad och välj sedan **Spara** för att spara inställningarna.


> [!NOTE]
> Det kommer att bli en viss fördröjning, och det kan ta upp till 10 minuter innan loggen är tillgänglig. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Skapa en aviseringsregel med Azure Monitor 


När händelserna har pushats till Log Analytics kan du köra följande [fråga](/azure/azure-monitor/log-query/get-started-portal) för att leta efter schemahändelserna.

1. Högst upp på sidan väljer du **Loggar** och klistrar in följande i textrutan:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Välj **Spara**och skriv sedan *logQuery* för namnet, lämna **Query** som typ, skriv *VMLogs* som **kategori**och välj sedan **Spara**. 

    ![Spara frågan](./media/notifications/save-query.png)

1. Välj **Ny aviseringsregel**. 
1. På sidan **Skapa** regel `collectorworkspace` lämnar du som **resurs**.
1. Under **Villkor**väljer du transaktionen *När <login undefined>kundloggsökningen är *. Sidan **Konfigurera signallogik** öppnas.
1. Under **Tröskelvärde**anger du *0* och väljer sedan **Klar**.
1. Under **Åtgärder**väljer du **Skapa åtgärdsgrupp**. Sidan **Lägg till åtgärdsgrupp** öppnas.
1. Skriv *myActionGroup*i **Åtgärdsgruppnamn**.
1. Skriv **myActionGroup**i **Kort namn**.
1. Välj **minResourceGroupAvailability**i **resursgruppen**.
1. Under Åtgärder skriver du **E-post**i **ÅTGÄRDSNAMN** och väljer sedan **E-post/SMS/Push/Voice**. **Sidan E-post/SMS/Push/Voice** öppnas.
1. Välj **E-post**, skriv in din e-postadress och välj sedan **OK**.
1. Välj **OK**på sidan **Lägg till åtgärdsgrupp** . 
1. Skriv *myAlert* för **aviseringsregelnamnet**under **VARNINGSINFORMATION**på sidan **Skapa regel** och skriv sedan *E-postaviseringsregel* för **beskrivningen**.
1. När du är klar väljer du **Skapa aviseringsregel**.
1. Starta om en av de virtuella datorerna i tillgänglighetsuppsättningen. Inom några minuter bör du få ett e-postmeddelande om att aviseringen har utlösts.

Om du vill hantera dina aviseringsregler går du till resursgruppen, väljer **Aviseringar** på den vänstra menyn och väljer sedan **Hantera aviseringsregler** högst upp på sidan.

     
## <a name="next-steps"></a>Nästa steg

Mer information finns på [tjänstsidan schemalagda händelser](https://github.com/microsoft/AzureScheduledEventsService) på GitHub.
