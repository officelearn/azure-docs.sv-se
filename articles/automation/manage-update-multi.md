---
title: Hantera uppdateringar för flera virtuella Azure-datorer
description: Den här artikeln beskriver hur du hanterar uppdateringar för virtuella Azure-datorer.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59a00f5605f7664148b65f2ec9a88fbaa9057ccf
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946065"
---
# <a name="manage-updates-for-multiple-machines"></a>Hantera uppdateringar av flera datorer

Du kan använda uppdateringen hanteringslösning för att hantera uppdateringar och korrigeringsfiler för Windows och Linux virtuella datorer. Från [Azure Automation](automation-offering-get-started.md)-kontot kan du:

- Publicera virtuella datorer
- Bedöma statusen för uppdateringar
- Schemalägga installationen av obligatoriska uppdateringar
- Granska agentdistributionresultaten att verifiera att uppdateringar har använts på alla virtuella datorer som har aktiverat uppdateringshantering

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda uppdateringshantering, behöver du:

- Ett kör som-konto för Azure Automation. Information om hur du skapar en finns [komma igång med Azure Automation](automation-offering-get-started.md).
- En virtuell dator eller en dator med ett operativsystem som stöds installerat.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Uppdateringshantering stöds på följande operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Stöder bara uppdatera bedömningar.         |
|Windows Server 2008 R2 SP1 och senare     |Windows PowerShell 4.0 eller senare krävs. ([Hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 rekommenderas för ökad tillförlitlighet. ([Hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 12.04 LTS, 14.04 LTS och 16.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

> [!NOTE]
> Konfigurera om Unattended Upgrade-paketet om du vill inaktivera automatiska uppdateringar för att undvika att uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Mer information finns i avsnittet om [automatiska uppdateringar i handboken för Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.

Den här lösningen stöder inte en Operations Management Suite (OMS) Agent för Linux som är konfigurerad för att rapportera till flera arbetsytor i Azure logganalys.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivera uppdateringshantering för virtuella Azure-datorer

Öppna ditt Automation-konto i Azure-portalen och välj sedan **uppdateringshantering**.

Välj **lägga till virtuella Azure-datorer**.

![Lägg till Virtuella Azure-fliken](./media/manage-update-multi/update-onboard-vm.png)

Välj en virtuell dator som du vill publicera. 

Under **aktivera uppdateringshantering**väljer **aktivera** publicera den virtuella datorn.

![Dialogrutan Aktivera hantering av uppdateringar](./media/manage-update-multi/update-enable.png)

När onboardingsprocessen har slutförts har uppdateringshantering aktiverats för den virtuella datorn.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Aktivera uppdateringshantering för Azure-virtuella datorer och datorer

Information om hur du aktiverar uppdateringshantering för Azure Windows virtuella datorer och datorer, finns [ansluta Windows-datorer i Azure logganalys-tjänsten](../log-analytics/log-analytics-windows-agent.md).

Information om hur du aktiverar uppdateringshantering för icke - Azure Linux virtuella datorer och datorer, finns [ansluta Linux-datorer till logganalys](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visa datorer som är kopplad till ditt Automation-konto

När du aktiverar uppdateringshantering för dina datorer, kan du visa information om datorn genom att välja **datorer**. Du kan visa information om *datornamnet*, *kompatibilitetsstatus*, *miljö*, *OS-typen*, *kritiska och säkerhetsuppdateringar är installerade*, *andra uppdateringar som installerats*, och *uppdatera agent beredskap* för datorer.

  ![Visa fliken för datorer](./media/manage-update-multi/update-computers-tab.png)

Datorer som nyligen har aktiverats för uppdateringshantering kanske inte har bedömts ännu. Kompatibilitetsstatus för tillstånd för dessa datorer är **utvärderas inte**. Här är en lista över möjliga värden för kompatibilitetstillstånd:

- **Kompatibla**: datorer som är inte saknar kritiska uppdateringar eller säkerhetsuppdateringar.

- **Icke-kompatibel**: datorer som saknar minst en kritisk eller säkerhetsuppdateringen.

- **Utvärdera inte**: assessment-data för uppdateringen har inte tagits emot från datorn i den förväntade tidsperioden. För Linux-datorer är tidsperioden som förväntat under de senaste 3 timmarna. För Windows-datorer är den förväntade tidsperioden under de senaste 12 timmarna.

Om du vill visa statusen för agenten, Välj länken i den **UPDATE AGENT beredskap** kolumn. Det här alternativet öppnas den **Hybrid Worker** rutan och visar status för Worker-hybriden. Följande bild visar ett exempel på en agent som inte har anslutits till uppdateringshantering under en längre tidsperiod:

![Visa fliken för datorer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När uppdateringshantering har aktiverats kan den **uppdateringshantering** fönstret öppnas. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

## <a name="collect-data"></a>Samla in data

Agenter som installerats på datorer med virtuella datorer och samla in data om uppdateringar. Agenterna skicka data till Azure uppdateringshantering.

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Uppdateringshantering samlar in information om uppdateringar från Windows-agenter och sedan startar installationen av obligatoriska uppdateringar. |
| Linux-agenter |Ja |Uppdateringshantering samlar in information om uppdateringar från Linux-agenter och sedan startar installationen av obligatoriska uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Uppdateringshantering samlar in information om uppdateringar från agenter i en ansluten hanteringsgrupp. |
| Azure-lagringskonto |Nej |Azure Storage innehåller information om uppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

En sökning körs två gånger om dagen för alla hanterade Windows-datorer. Var 15: e minut Windows API kallas för att söka efter de senaste uppdateringstiden att fastställa om status har ändrats. Om status ändras, startar en genomsökning för kompatibilitet. En sökning körs var 3: e timme för varje hanterad Linux-dator.

Det kan ta mellan 30 minuter och 6 timmar för instrumentpanelen för att visa uppdaterade data från hanterade datorer.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

Planera en distribution som överensstämmer med versionen schema och tjänsten fönstret för att installera uppdateringarna. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Så här schemalägger du en ny uppdateringsdistribution för en eller flera virtuella datorer under **uppdateringshantering**väljer **schema distribution**.

I den **ny uppdatera distribution** rutan anger du följande information:

- **Namnet**: Ange ett unikt namn för att identifiera distributionen av uppdateringen.
- **Operativsystemet**: Välj **Windows** eller **Linux**.
- **Datorer för att uppdatera**: Välj virtuella datorer som du vill uppdatera. Beredskap för datorn visas i den **UPDATE AGENT beredskap** kolumn. Du kan se hälsotillståndet för datorn innan du schemalägga distributionen av uppdateringen.

  ![Nya update distribution rutan](./media/manage-update-multi/update-select-computers.png)

- **Uppdatera klassifikationen**: Välj typerna av program som ska ingå i distributionen av uppdateringen. En beskrivning av typer klassificering finns [uppdatera klassificeringar](automation-update-management.md#update-classifications). Klassificeringstyper:
  - Kritiska uppdateringar
  - Säkerhetsuppdateringar
  - Samlade uppdateringar
  - Funktionspaket
  - Service pack
  - Definitionsuppdateringar
  - Verktyg
  - Uppdateringar

- **Uppdateringar för att utesluta**: det här alternativet öppnas den **undanta** sidan. Ange KB-artiklar eller paket som ska undantas.

- **Schemainställningar**: Du kan godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid. Du kan också ange en annan tid.

   Du kan också ange om distributionen ska ske en gång eller enligt ett schema med återkommande tider. Att ställa in ett återkommande schema under **återkommande**väljer **återkommande**.

   ![Dialogrutan Schemainställningar](./media/manage-update-multi/update-set-schedule.png)
- **Underhållsperiod (minuter)**: Ange tidsperioden som du vill att distributionen av uppdateringen ska ske. Den här inställningen hjälper till att säkerställa att ändringarna utförs inom ditt definierade servicefönster.

När du är klar med att konfigurera schemat, väljer den **skapa** vill gå tillbaka till instrumentpanelen status. Den **schemalagda** tabellen visar distributionsschemat som du skapade.

> [!WARNING]
> För uppdateringar som kräver en omstart av den virtuella datorn startas om automatiskt.

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När schemalagd distribution har startat kan du se statusen för distributionen på de **Programuppdateringsdistributioner** fliken **uppdateringshantering**.

Om distributionen körs är dess status **Pågår**. När distributionen är klar har statusen ändras till **lyckades**.

Om en eller flera uppdateringar i distributionen misslyckas visas statusen **Misslyckades delvis**.

![Status för uppdateringsdistribution](./media/manage-update-multi/update-view-results.png)

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

Den **Uppdatera resultat** visar det totala antalet uppdateringar och distributionsresultat för den virtuella datorn. I högra tabellen ger en detaljerad uppdelning av varje uppdatering och resultatet av installationen. Installationsresultaten kan ha något av följande värden:

- **Inget försök**: uppdateringen har inte installerats eftersom tillräckligt med tid tillgänglig baserat på det definierade underhållsfönstret.
- **Lyckades**: uppdateringen har slutförts.
- **Det gick inte**: uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen Utdata om du vill se jobbström runbook som hanterar distributionen av uppdateringen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

- Mer information om uppdateringshantering, inklusive loggar, utdata och fel, finns [uppdatering hanteringslösning i Azure](../operations-management-suite/oms-solution-update-management.md).
