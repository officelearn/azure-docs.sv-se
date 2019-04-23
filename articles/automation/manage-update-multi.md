---
title: Hantera uppdateringar för flera virtuella Azure-datorer
description: Den här artikeln beskriver hur du hanterar uppdateringar för Azure-datorer.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16fe2d23fdd07f8f150cc010b0a1d232c761c77f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798680"
---
# <a name="manage-updates-for-multiple-machines"></a>Hantera uppdateringar av flera datorer

Du kan använda lösningen för uppdateringshantering för att hantera uppdateringar och korrigeringar för dina Windows- och Linux-datorer. Från [Azure Automation](automation-offering-get-started.md)-kontot kan du:

- Publicera virtuella datorer
- Bedöma statusen för tillgängliga uppdateringar
- Schemalägga installation av nödvändiga uppdateringar
- Granska distributionsresultaten för att verifiera att uppdateringarna har tillämpats för alla virtuella datorer som uppdateringshantering är aktiverat

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda uppdateringshantering behöver du:

- En virtuell dator eller en dator med ett operativsystem som stöds installerat.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Uppdateringshantering stöds på följande operativsystem:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Stöder bara uppdatera utvärderingar.         |
|Windows Server 2008 R2 SP1 och senare     |Windows PowerShell 4.0 eller senare krävs. ([Hämta WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1 rekommenderas för ökad tillförlitlighet. ([Hämta WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.        |
|Ubuntu 14.04 LTS, 16.04 LTS och 18.04 LTS (x86/x64)      |Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.         |

> [!NOTE]
> Konfigurera om Unattended Upgrade-paketet om du vill inaktivera automatiska uppdateringar för att undvika att uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Mer information finns i avsnittet om [automatiska uppdateringar i handboken för Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.

Den här lösningen stöder inte en Log Analytics-agenten för Linux som är konfigurerad för att rapportera till flera Azure Log Analytics-arbetsytor.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivera uppdateringshantering för virtuella Azure-datorer

Öppna ditt Automation-konto i Azure-portalen och välj sedan **uppdateringshantering**.

Välj **lägga till virtuella Azure-datorer**.

![Lägg till Virtuella Azure-flik](./media/manage-update-multi/update-onboard-vm.png)

Välj en virtuell dator som du vill publicera. 

Under **Aktivera hantering av uppdateringar**väljer **aktivera** att publicera den virtuella datorn.

![Dialogrutan Aktivera hantering av uppdateringar](./media/manage-update-multi/update-enable.png)

När onboardingsprocessen har slutförts, har uppdateringshantering aktiverats för den virtuella datorn.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Aktivera uppdateringshantering för icke-Azure virtuella datorer och datorer

Information om hur du aktiverar uppdateringshantering för icke - Azure Windows-datorer och datorer, se [ansluta Windows-datorer till Azure Monitor-tjänsten i Azure](../log-analytics/log-analytics-windows-agent.md).

Information om hur du aktiverar uppdateringshantering för Azure Linux-datorer och datorer, se [ansluta dina Linux-datorer till Azure Monitor-loggar](../log-analytics/log-analytics-agent-linux.md).

## <a name="view-computers-attached-to-your-automation-account"></a>Visa datorer som är kopplat till ditt Automation-konto

När du aktiverar uppdateringshantering för dina datorer, du kan visa information om datorn genom att välja **datorer**. Du kan se information *datornamn*, *efterlevnadsstatus*, *miljö*, *OS-typ*, *kritiska och säkerhetsuppdateringarna*, *andra uppdateringar som installerats*, och *uppdatera agentberedskap* för dina datorer.

  ![Visa fliken för datorer](./media/manage-update-multi/update-computers-tab.png)

Datorer som nyligen har aktiverats för uppdateringshantering kanske har ännu inte utvärderats. Kompatibilitetsstatus för tillstånd för dessa datorer är **ej utvärderat**. Här är en lista över möjliga värden för efterlevnadstillstånd:

- **Kompatibla**: Datorer som är inte saknar kritiska eller säkerhetsuppdateringar.

- **Icke-kompatibla**: Datorer som saknar minste en kritisk uppdatering eller säkerhetsuppdatering.

- **Ej utvärderat**: Uppdatera utvärderingsdata har inte tagits emot från datorn inom den förväntade tidsramen. För Linux-datorer är den förväntade tidsramen under de senaste 3 timmarna. För Windows-datorer är den förväntade tidsramen under de senaste 12 timmarna.

Om du vill visa statusen för agenten, väljer du länken i den **uppdatera AGENTBEREDSKAP** kolumn. Om du väljer det här alternativet öppnas den **Hybrid Worker** fönstret och visar status för Hybrid Worker. Följande bild visar ett exempel på en agent som inte har anslutits till hantering av uppdateringar under en längre tidsperiod:

![Visa fliken för datorer](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Visa en uppdateringsbedömning

När uppdateringshantering är aktiverat visas fönstret **Uppdateringshantering**. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

## <a name="collect-data"></a>Samla in data

Agenter som installerats på virtuella datorer och datorer samlar in data om uppdateringar. Agenterna skickar data till hantering av Azure.

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från Linux-agenter och initierar installationen av nödvändiga uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp. |
| Azure-lagringskonto |Nej |Azure Storage innehåller inte information om systemuppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

När en dator är klar en sökning efter uppdateringskompatibilitet vidarebefordrar agenten informationen gruppvis till Azure Monitor-loggar. På en Windows-dator körs kompatibilitetsgenomsökningen var 12: e timme som standard.

Förutom genomsökningsschemat initieras sökningen för uppdateringskompatibilitet inom 15 minuter från MMA startas innan installationen av uppdateringen och efteråt.

För en Linux-dator utförs kompatibilitetsgenomsökningen var tredje timme som standard. Om MMA-agenten startas initieras en kompatibilitetsgenomsökning inom 15 minuter.

Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data från hanterade datorer.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

Schemalägga en distribution som överensstämmer med versionen schema och fönstret för att installera uppdateringarna. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Att schemalägga en ny uppdateringsdistribution för en eller flera virtuella datorer under **uppdateringshantering**väljer **distribution av schemauppdatering**.

I den **ny uppdateringsdistribution** fönstret anger du följande information:

- **Namn**: Ange ett unikt namn som identifierar uppdateringsdistributionen.
- **Operativsystem**: Välj **Windows** eller **Linux**.
- **Grupper som ska uppdateras (förhandsversion)**: Definiera en fråga som baseras på en kombination av prenumeration, resursgrupper, platser och taggar för att skapa en dynamisk grupp med virtuella Azure-datorer som ska ingå i din distribution. Mer information finns i [Dynamiska grupper](automation-update-management.md#using-dynamic-groups)
- **Datorer som ska uppdateras**: Välj en sparad sökning importerat gruppen, eller datorer att välja de datorer som du vill uppdatera. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**. Du kan se hälsotillståndet för datorn innan du schemalägga distributionen av uppdateringen. Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md)

  ![Nya rutan för distribution av uppdatering](./media/manage-update-multi/update-select-computers.png)

- **Uppdatera klassificering**: Välj vilka typer av programvara ska ingå i uppdateringsdistributionen. En beskrivning av klassificeringstyper finns i [Uppdateringsklassificeringar](automation-update-management.md#update-classifications). Klassificeringstyper:
  - Kritiska uppdateringar
  - Säkerhetsuppdateringar
  - Samlade uppdateringar
  - Funktionspaket
  - Service pack
  - Definitionsuppdateringar
  - Verktyg
  - Uppdateringar

- **Uppdateringar att inkludera/exkludera** – detta öppnar sidan **Inkludera/exkludera**. Uppdateringar som ska inkluderas eller exkluderas visas på en separat flik. Mer information om hur inkludering hanteras och finns i [inkluderingsbeteende](automation-update-management.md#inclusion-behavior)

- **Schemainställningar**: Du kan godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid. Du kan också ange en annan tid.

   Du kan också ange om distributionen ska ske en gång eller enligt ett schema med återkommande tider. Du ställer in ett återkommande schema under **upprepning**väljer **återkommande**.

   ![Dialogrutan Schemainställningar](./media/manage-update-multi/update-set-schedule.png)

- **Förskript och efterskript**: Välj vilka skript som ska köras före och efter distributionen. Mer information finns i [Hantera skript före och efter](pre-post-scripts.md).
- **Underhållsperiod (minuter)**: Ange tidsperioden som uppdateringsdistributionen ska utföras. Den här inställningen hjälper till att säkerställa att ändringarna utförs inom ditt definierade servicefönster.

- **Starta om kontrollen** – den här inställningen avgör hur omstarter hanteras för distributionen.

   |Alternativ|Beskrivning|
   |---|---|
   |Starta om vid behov| **(Standard)**  Om det behövs kan en omstart initieras om underhållsperioden tillåter.|
   |Starta alltid om|En omstart initieras oavsett om en sådan krävs. |
   |Starta aldrig om|Oavsett om en omstart krävs, undertrycks omstarter.|
   |Endast omstart – uppdateringar installeras inte|Det här alternativet ignorerar installerar uppdateringar och endast initierar en omstart.|

När du är klar med att konfigurera schemat, väljer den **skapa** vill gå tillbaka till statusinstrumentpanelen. Den **schemalagd** tabellen visar distributionsschemat som du skapade.

> [!NOTE]
> Uppdateringshantering har stöd för distribution av förstapartsuppdateringar och förnedladdning av korrigeringar. Detta kräver ändringar på de system som korrigeras, se [första part och pre hämta support](automation-update-management.md#firstparty-predownload) att lära dig hur du konfigurerar de här inställningarna på dina system.

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** under **Uppdateringshantering**.

Om distributionen körs är dess status **Pågår**. När distributionen har slutförts ändras statusen till **lyckades**.

Om en eller flera uppdateringar i distributionen misslyckas visas statusen **Misslyckades delvis**.

![Status för uppdateringsdistribution](./media/manage-update-multi/update-view-results.png)

Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

Den **Uppdateringsresultat** visar det totala antalet uppdateringar och distributionsresultat för den virtuella datorn. Tabellen till höger visar detaljer för varje uppdatering och installationsresultaten. Installationsresultaten kan ha något av följande värden:

- **Inget försök har gjorts**: Uppdateringen installerades inte eftersom fanns tillräckligt med tid tillgänglig utifrån underhållsfönstret som definierats.
- **Lyckades**: Uppdateringen lyckades.
- **Misslyckades**: Uppdateringen misslyckades.

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen Utdata om du vill se jobbströmmen för den runbook som hanterar distributionen av uppdateringen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hantering av uppdateringar, inklusive loggar, utdata och fel i [lösningen för uppdateringshantering i Azure](../operations-management-suite/oms-solution-update-management.md).

