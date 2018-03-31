---
title: Hantera uppdateringar för flera virtuella Azure-datorer
description: Det här ämnet beskriver hur du hanterar uppdateringar för virtuella Azure-datorer.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c227af1afa95243390152918a6b81015dbaceaeb
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="manage-updates-for-multiple-machines"></a>Hantera uppdateringar av flera datorer

Du kan använda uppdateringshantering för att hantera uppdateringar och korrigeringar av virtuella Windows- och Linux-datorer. Från [Azure Automation](automation-offering-get-started.md)-kontot kan du:

- Publicera virtuella datorer.
- Bedöma statusen för tillgängliga uppdateringar.
- Schemalägga installation av obligatoriska uppdateringar.
- Granska distributionsresultaten för att bekräfta att uppdateringarna har verkställts på alla virtuella datorer där uppdateringshantering har aktiverats.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda uppdateringshantering behöver du:

* Ett kör som-konto för Azure Automation. Anvisningar om hur du skapar ett konto finns i [Komma igång med Azure Automation](automation-offering-get-started.md).

* En virtuell dator eller en dator med ett operativsystem som stöds installerat.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Uppdateringshantering stöds på följande operativsystem.

### <a name="windows"></a>Windows

* Windows Server 2008 eller senare och uppdateringsdistributioner av Windows Server 2008 R2 SP1 och senare. Nano Server stöds inte.

  Stöd för att distribuera uppdateringar till Windows Server 2008 R2 SP1 kräver .NET Framework 4.5 och Windows Management Framework 5.0 eller senare.

* Windows-klientoperativsystem stöds inte.

Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update.

> [!NOTE]
> System Center Configuration Manager kan inte hantera Windows-agenten samtidigt.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) och 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) och 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)  
* Ubuntu 12.04 LTS och senare (x86/x64)   

> [!NOTE]  
> Konfigurera om Unattended Upgrade-paketet om du vill inaktivera automatiska uppdateringar för att undvika att uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Mer information finns i avsnittet om [automatiska uppdateringar i handboken för Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.

Den här lösningen stöder inte en OMS-Agent för Linux konfigurerad för att rapportera till flera logganalys arbetsytor.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivera uppdateringshantering för virtuella Azure-datorer

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Hantering av uppdateringar** i den vänstra rutan.
3. Välj **Lägg till virtuell Azure-dator** högst upp i fönstret.
   ![Fliken Lägg till virtuell Azure-dator](./media/manage-update-multi/update-onboard-vm.png)
4. Välj en virtuell dator som du vill publicera. Dialogrutan **Aktivera hantering av uppdateringar** visas.
5. Välj **Aktivera**.

   ![Dialogrutan Aktivera hantering av uppdateringar](./media/manage-update-multi/update-enable.png)

Uppdateringshantering har aktiverats för den virtuella datorn.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Aktivera uppdateringshantering för virtuella datorer som inte använder Azure och datorer

Instruktioner om hur du aktiverar uppdateringshantering för virtuella datorer som inte använder Azure Windows och datorer finns i [Connect Windows computers to the Log Analytics service in Azure](../log-analytics/log-analytics-windows-agent.md) (Anslut Windows-datorer till Log Analytics-tjänsten i Azure).

Instruktioner om hur du aktiverar uppdateringshantering för virtuella Linux-datorer och datorer som inte använder Azure finns i [Connect your Linux Computers to Log Analytics](../log-analytics/log-analytics-agent-linux.md) (Anslut dina Linux-datorer till Log Analytics).

## <a name="view-computers-attached-to-your-automation-account"></a>Visa datorer som är anslutna till ditt Automation-konto
När du har aktiverat uppdateringshantering för dina datorer kan du visa deras information genom att klicka på **Datorer**. Datorinformation som *Namn*, *Efterlevnad*, *Miljö*, *OS-typ*, *Kritiska uppdateringar och säkerhetsuppdateringar* och *Övriga uppdateringar* finns tillgänglig. 

  ![Visa fliken för datorer](./media/manage-update-multi/update-computers-tab.png)

Datorer som nyligen har aktiverats för uppdateringshantering kanske ännu inte har utvärderats. Efterlevnadstillståndet för dessa datorer skulle ha statusen  *	Ej utvärderat*.  Här följer en lista över värden för efterlevnadstillstånd:
* Kompatibel – Datorer som inte saknar kritiska uppdateringar eller säkerhetsuppdateringar.
* Inkompatibel – Datorer som saknar minste en kritisk uppdatering eller säkerhetsuppdatering.
* Ej utvärderat – Data för kontroll av uppdateringar har inte tagits emot från datorn inom den förväntade tidsramen.  För Linux-datorer under de tre senaste timmarna och för Windows-datorer under de 12 senaste timmarna.  

## <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När uppdateringshantering är aktiverat visas dialogrutan **Hantering av uppdateringar**. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

## <a name="collect-data"></a>Samla in data

Agenter som installerats på virtuella datorer och datorer samlar in data om uppdateringar och skickar dem till Azures uppdateringshantering.

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från Linux-agenter och initierar installationen av nödvändiga uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp. |
| Azure Storage-konto |Nej |Azure Storage inkluderar inte information om systemuppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

För varje hanterad Windows-dator körs en genomsökning två gånger per dag. Var 15:e minut anropas Windows API för att fråga efter den senaste uppdateringstiden för att fastställa om statusen har ändrats. I så fall startar en kompatibilitetssökning. För varje hanterad Linux-dator körs en sökning var tredje timme.

Det kan ta 30 minuter upp till 6 timmar innan instrumentpanelen visar uppdaterade data från hanterade datorer.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster.
Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Schemalägg en ny uppdateringsdistribution för en eller flera virtuella datorer genom att välja **Distribution av schemauppdatering** längst upp i dialogrutan **Hantering av uppdateringar**. I fönstret **Ny uppdateringsdistribution** anger du följande:

* **Namn**: Ett unikt namn som identifierar uppdateringsdistributionen.
* **OS-typ**: Välj Windows eller Linux.
* **Datorer att uppdatera**: Välj de virtuella datorer som du vill uppdatera.

  ![Fönstret ”Ny uppdateringsdistribution”](./media/manage-update-multi/update-select-computers.png)

* **Uppdatera klassificering**: Välj vilka typer av programvara som ska tas med i uppdateringsdistributionen. Klassificeringstyper:
  * Kritiska uppdateringar
  * Säkerhetsuppdateringar
  * Samlade uppdateringar
  * Funktionspaket
  * Service pack
  * Definitionsuppdateringar
  * Verktyg
  * Uppdateringar
* **Schemainställningar**: Du kan godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid. Du kan även ange en annan tid.
   Du kan också ange om distributionen ska ske en gång eller enligt ett schema med återkommande tider. Välj alternativet **Återkommande** under **Återkommande** för att ange ett återkommande schema.

   ![Dialogrutan Schemainställningar](./media/manage-update-multi/update-set-schedule.png)

* **Underhållsperiod (minuter)**: Ange den tidsperiod då uppdateringsdistributionen ska utföras. Den här inställningen hjälper till att säkerställa att ändringarna utförs inom ditt definierade servicefönster.

När du har konfigurerat schemat klickar du på **Skapa** för att komma tillbaka till statusinstrumentpanelen. Tabellen **Schemalagt** visar det distributionsschema som du precis skapade.

> [!WARNING]
> Om en uppdatering kräver omstart startas den virtuella datorn om automatiskt.

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har påbörjats kan du se status för distributionen på fliken **Uppdateringsdistributioner** i dialogrutan **Hantering av uppdateringar**.
Om distributionen körs är dess status **Pågår**. När distributionen har slutförts ändras statusen till **Lyckades**.
Om en eller flera uppdateringar i distributionen misslyckas visas statusen **Misslyckades delvis**.

![Status för uppdateringsdistribution](./media/manage-update-multi/update-view-results.png)

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

Fönstret **Uppdatera resultat** visar en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn.
Tabellen till höger visar detaljer för varje uppdatering och installationsresultaten. Installationsresultaten kan ha något av följande värden:

* Inget försök har gjorts: Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* Lyckades: Uppdateringen lyckades.
* Misslyckades: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Klicka på panelen **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om uppdateringshantering – inklusive loggar, utdata och fel--finns [uppdatera hanteringslösning i Azure](../operations-management-suite/oms-solution-update-management.md).

