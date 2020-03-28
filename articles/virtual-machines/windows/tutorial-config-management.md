---
title: Självstudiekurs - Hantera windows-konfiguration för virtuella datorer i Azure
description: I den här självstudien får du lära dig hur du identifierar ändringar och hanterar paketuppdateringar på en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238582"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Självstudiekurs: Övervaka ändringar och uppdatera en virtuell Windows-dator i Azure

Med Azure [Change Tracking](../../automation/change-tracking.md) and [Update Management](../../automation/automation-update-management.md)kan du enkelt identifiera ändringar i dina virtuella Windows-datorer i Azure och hantera operativsystemuppdateringar för dessa virtuella datorer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Hantera Windows-uppdateringar.
> * Övervaka ändringar och lager.

## <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt interaktivt skal som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerade och konfigurerade för att använda med ditt Azure-konto.

Om du vill öppna ett kodblock i Cloud Shell väljer du bara **Prova det** i det övre högra hörnet av kodblocket.

Du kan också öppna Cloud Shell i [https://shell.azure.com/powershell](https://shell.azure.com/powershell)en separat webbläsarflik genom att gå till . Välj **Kopiera** för att kopiera kodblock, klistra in dem på fliken Cloud Shell och välj returtangenten för att köra koden.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

För att konfigurera övervaknings- och uppdateringshantering i Azure i den här självstudiekursen behöver du en virtuell Windows-dator i Azure.

Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa sedan den virtuella datorn med [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). I följande exempel skapas `myVM` en `East US` virtuell dator som namnges på platsen. Om de inte redan finns skapas resursgruppen `myResourceGroupMonitor` och stödnätverksresurserna:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Det tar några minuter att skapa resurserna och den virtuella datorn.

## <a name="manage-windows-updates"></a>Hantera Windows-uppdateringar

Update Management hjälper dig att hantera uppdateringar och korrigeringar för dina virtuella Azure Windows-datorer. Direkt från den virtuella datorn kan du snabbt:

- Bedöma statusen för tillgängliga uppdateringar.
- Schemalägga installation av obligatoriska uppdateringar.
- Granska distributionsresultat för att verifiera att uppdateringar har tillämpats på den virtuella datorn.

Prisinformation finns i [Automatiseringspriser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Så här aktiverar du uppdateringshantering för den virtuella datorn:

1. Välj **Virtuella datorer**längst till vänster i fönstret .
1. Välj en virtuell dator i listan.
1. Välj **Uppdatera hantering**i fönstret **Operationer** i fönstret Vm.
1. Fönstret **Aktivera uppdateringshantering** öppnas.

Validering görs för att avgöra om uppdateringshantering är aktiverat för den här virtuella datorn. Valideringen omfattar kontroller för en Log Analytics-arbetsyta, för ett länkat Automation-konto och om lösningen finns på arbetsytan.

Du använder en [Log Analytics-arbetsyta](../../log-analytics/log-analytics-overview.md) för att samla in data som genereras av funktioner och tjänster som uppdateringshantering. Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.

Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan du använda Azure Automation för att köra runbooks mot virtuella datorer. Sådana åtgärder omfattar nedladdning eller tillämpning av uppdateringar.

Valideringsprocessen kontrollerar också om den virtuella datorn är etablerad med MMA (Microsoft Monitoring Agent) och Automation Hybrid Runbook Worker. Du använder agenten för att kommunicera med den virtuella datorn och få information om uppdateringsstatus.

I fönstret **Aktivera uppdateringshantering** väljer du arbetsyte- och automatiseringskontot Log Analytics och väljer sedan **Aktivera**. Lösningen tar upp till 15 minuter att aktiveras.

Alla följande förutsättningar som saknas under introduktion läggs automatiskt till:

* [Log Analytics-arbetsyta](../../log-analytics/log-analytics-overview.md)
* [Automation](../../automation/automation-offering-get-started.md)
* En [hybridkörningsarbetare](../../automation/automation-hybrid-runbook-worker.md)som är aktiverad på den virtuella datorn

När lösningen har aktiverats öppnas **uppdateringshanteringsfönstret.** Konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas och välj sedan **Aktivera**. Om dessa alternativ verkar nedtonade aktiveras en annan automatiseringslösning för den virtuella datorn och den lösningens arbetsyta och Automation-konto måste användas.

![Aktivera lösning för uppdateringshantering](./media/tutorial-monitoring/manageupdates-update-enable.png)

Det kan ta upp till 15 minuter innan uppdateringshanteringslösningen aktiveras. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Azure Monitor-loggar. Det kan ta från 30 minuter till 6 timmar för data att bli tillgängliga för analys.

### <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När uppdateringshantering har aktiverats visas **uppdateringshanteringsfönstret.** När utvärderingen av uppdateringar är klar visas en lista över saknade uppdateringar på fliken **Saknade uppdateringar.**

 ![Visa uppdateringsstatus](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du väljer vilka uppdateringstyper som ska inkluderas i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Om du vill schemalägga en ny uppdateringsdistribution för den virtuella datorn väljer du **Schemalägg uppdateringsdistribution** högst upp i **uppdateringshanteringsfönstret.** Ange följande information i **fönstret Ny uppdateringsdistribution:**

| Alternativ | Beskrivning |
| --- | --- |
| **Namn** |Ange ett unikt namn för att identifiera uppdateringsdistributionen. |
|**Operativsystem**| Välj antingen **Linux** eller **Windows**.|
| **Grupper som ska uppdateras** |För virtuella datorer som finns på Azure definierar du en fråga baserat på en kombination av prenumeration, resursgrupper, platser och taggar. Den här frågan skapar en dynamisk grupp av Azure-värddass datorer som ska inkluderas i distributionen. </br></br>För virtuella datorer som inte finns på Azure väljer du en befintlig sparad sökning. Med den här sökningen kan du välja en grupp av dessa virtuella datorer som ska inkluderas i distributionen. </br></br> Mer information finns i [Dynamiska grupper](../../automation/automation-update-management-groups.md).|
| **Maskiner för att uppdatera** |Välj **Sparad sökning,** **Importerad grupp**eller **Datorer**.<br/><br/>Om du väljer **Maskiner**kan du välja enskilda datorer i listrutan. Beredskapen för varje dator visas i kolumnen **UPDATE AGENT READINESS** i tabellen.</br></br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../../azure-monitor/platform/computer-groups.md) |
|**Uppdatera klassificeringar**|Välj alla nödvändiga uppdateringsklassificeringar.|
|**Inkludera/exkludera uppdateringar**|Välj det här alternativet om du vill öppna fönstret **Inkludera/Uteslut.** Uppdateringar som ska inkluderas och de som ska uteslutas finns på separata flikar. Mer information om hur inkludering hanteras finns i [Schemalägga en uppdateringsdistribution](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment). |
|**Schemalägg inställningar**|Välj tid att starta och välj antingen **En gång** eller **Återkommande**.|
| **Förskript + Postskript**|Välj de skript som ska köras före och efter distributionen.|
| **Fönstret Underhåll** | Ange antalet minuter som angetts för uppdateringar. Giltiga värden varierar från 30 till 360 minuter. |
| **Omstartskontroll**| Välj hur omstarter ska hanteras. Tillgängliga val är:<ul><li>**Starta om vid behov**</li><li>**Starta alltid om**</li><li>**Starta aldrig om**</li><li>**Starta bara om**</li></ul>**Starta om om det behövs** är standardvalet. Om du väljer **Starta om Endast**installeras inte uppdateringar.|

När du har konfigurerat schemat klickar du på **Skapa** för att återgå till statusinstrumentpanelen. Tabellen **Schemalagd** visar distributionsschemat som du skapade.

Du kan också skapa uppdateringsdistributioner programmässigt. Mer information om hur du skapar en uppdateringsdistribution med REST API finns i [Konfigurationer för programuppdatering - Skapa](/rest/api/automation/softwareupdateconfigurations/create). Det finns också en exempelkörningsbok som du kan använda för att skapa en veckovis uppdateringsdistribution. Mer information om den här runbooken finns i [Skapa en veckovis uppdateringsdistribution för en eller flera virtuella datorer i en resursgrupp](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har startat kan du se distributionsstatusen på fliken **Uppdatera distributioner** i **uppdateringshanteringsfönstret.**

Om distributionen körs visas dess status som "Pågår". När statusen har slutförts ändras den till "Lyckades". Men om några uppdateringar i distributionen misslyckas, är statusen "Delvis misslyckades".

Välj den slutförda uppdateringsdistributionen för att se instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för specifik distribution](./media/tutorial-monitoring/manageupdates-view-results.png)

Panelen **Uppdatera resultat** visar en sammanfattning av det totala antalet uppdateringar och distributionsresultat på den virtuella datorn. Tabellen till höger visar en detaljerad uppdelning av varje uppdatering och installationsresultaten. Varje resultat har något av följande värden:

* **Inte försök:** Uppdateringen är inte installerad. Det fanns inte tillräckligt med tid tillgänglig baserat på den definierade underhållsfönstrets varaktighet.
* **Lyckades**: Uppdateringen lyckades.
* **Misslyckades**: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen **Utdata** om du vill visa jobbströmmen för runbooken som ansvarar för att hantera uppdateringsdistributionen på måldatorn.

Välj **Fel** om du vill visa detaljerad information om eventuella distributionsfel.

## <a name="monitor-changes-and-inventory"></a>Övervaka ändringar och inventering

Du kan samla in och visa en inventering av program-, -filer-, Linux-demoner, Windows-tjänster och Windows-registernycklar på dina datorer. Genom att spåra konfigurationerna på dina datorer kan du identifiera driftproblem i hela din miljö och bättre förstå dina datorers tillstånd.

### <a name="enable-change-and-inventory-management"></a>Aktivera ändrings- och lagerhantering

Så här aktiverar du ändring och lagerhantering för den virtuella datorn:

1. Välj **Virtuella datorer**längst till vänster i fönstret .
1. Välj en virtuell dator i listan.
1. Under **Operationer** i fönstret Vm väljer du antingen **Lager-** eller **Ändringsspårning**.
1. Fönstret **Aktivera ändringsspårning och lager** öppnas.

Konfigurera arbetsytan, logganalysarbetsytan och automationskontot som ska användas och välj sedan **Aktivera**. Om alternativen visas nedtonade är en automatiseringslösning redan aktiverad för den virtuella datorn. I så fall måste det redan aktiverade arbetsytan och automationskontot användas.

Även om lösningarna visas separat i menyn är de samma lösning. När du aktiverar den ena aktiveras båda för den virtuella datorn.

![Aktivera Ändringsspårning och lager](./media/tutorial-monitoring/manage-inventory-enable.png)

När lösningen har aktiverats kan det ta lite tid innan lagret samlas in på den virtuella datorn innan data visas.

### <a name="track-changes"></a>Spåra ändringar

På den virtuella datorn under **OPERATIONER**väljer du **Ändra spårning** och väljer sedan **Redigera inställningar**. Fönstret **Ändra spårning** öppnas. Välj typ av inställning som du vill spåra och klicka sedan på **+ Lägg till** för att konfigurera inställningarna.

De tillgängliga inställningsalternativen för Windows är:

* Windows-registret
* Windows-filer

Detaljerad information om ändringsspårning finns i [Felsöka ändringar på en virtuell dator](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Visa inventering

På den virtuella datorn väljer du **Lager** under **OPERATIONER**. På fliken **Programvara** finns det en tabell som visar programvaran som har hittats. Information på hög nivå för varje programpost visas i tabellen. Dessa uppgifter inkluderar programvarans namn, version, utgivare och senast uppdaterad tid.

![Visa inventering](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Övervaka aktivitetsloggar och ändringar

Välj **Hantera aktivitetslogganslutning** i fönstret **Ändra spårning** **Azure Activity log** på den virtuella datorn. Välj **Anslut** för att ansluta ändringsspårning till Azure-aktivitetsloggen för din virtuella dator.

När ändringsspårning har aktiverats går du till **fönstret Översikt** för den virtuella datorn och väljer **Stoppa** för att stoppa den virtuella datorn. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den virtuella datorn har frigörs väljer du **Start** för att starta om den virtuella datorn.

Om du stoppar och startar om en virtuell dator loggas en händelse i aktivitetsloggen. Gå tillbaka till fönstret **Ändra spårning** och välj fliken **Händelser** längst ned i fönstret. Efter ett tag visas händelserna i diagrammet och tabellen. Du kan välja varje händelse om du vill visa detaljerad information för händelsen.

![Visa händelser i aktivitetsloggen](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Det föregående diagrammet visar ändringar som har skett med tiden. När du har lagt till en Azure Activity Log-anslutning visar linjediagrammet högst upp Azure Activity Log-händelser.

Varje rad med stapeldiagram representerar en annan spårbar ändringstyp. Dessa typer är Linux-demoner, filer, Windows-registernycklar, programvara och Windows-tjänster. På fliken **Ändra** visas ändringsinformationen. Ändringarna visas i den ordning som inträffade när varje inträffat, och den senaste ändringen visas först.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och granskat ändringsspårning och uppdateringshantering för den virtuella datorn. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp och virtuell dator.
> * Hantera Windows-uppdateringar.
> * Övervaka ändringar och lager.

Gå till nästa självstudiekurs om du vill lära dig mer om hur du övervakar den virtuella datorn.

> [!div class="nextstepaction"]
> [Övervaka virtuella datorer](tutorial-monitor.md)
