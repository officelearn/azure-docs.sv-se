---
title: "Uppdateringshanteringslösning i OMS | Microsoft Docs"
description: "Den här artikeln är avsedd att hjälpa dig att förstå hur du använder den här lösningen för att hantera uppdateringar för dina Windows- och Linux-datorer."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 03a6c1f20632691c08f5de4afe74eacc6f79608e
ms.contentlocale: sv-se
ms.lasthandoff: 05/03/2017


---
# <a name="update-management-solution-in-oms"></a>Uppdateringshanteringslösning i OMS
Uppdateringshanteringslösningen i OMS gör att du kan du hantera uppdateringar för dina Windows- och Linux-datorer.  Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och starta processen för att installera nödvändiga uppdateringar för servrar. 

## <a name="solution-components"></a>Lösningskomponenter

Datorer som hanteras av OMS använder följande för att utföra utvärderingar och uppdatera distribueringar: 

* OMS-agent för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux 
* Automation Hybrid Runbook Worker 
* Microsoft Update eller Windows Server Update Services för Windows-datorer

Följande diagram visar en konceptuell vy över beteenden och dataflöde över hur lösningen utvärderar och tillämpar uppdateringar till alla anslutna Windows Server- och Linux-datorer i en arbetsyta.    

#### <a name="windows-server"></a>Windows Server
![Processflöde för hantering av Windows Server-uppdateringar](media/oms-solution-update-management/update-mgmt-windows-updateworkflow.png)

#### <a name="linux"></a>Linux
![Processflöde för hantering av Linux-uppdateringar](media/oms-solution-update-management/update-mgmt-linux-updateworkflow.png)

När datorn har sökt efter uppdateringskompatibilitet vidarebefordrar OMS-agenten informationen gruppvis till OMS. På en Window-dator utförs en kompatibilitetssökning var 12:e timme som standard.  Förutom sökningsschemat initieras sökningen för uppdateringskompatibilitet inom 15 minuter om Microsoft Monitoring Agent (MMA) startas om före installationen av uppdateringen och efteråt.  Kompatibilitetsgenomsökningen utförs var 3:e timme som standard med en Linux-dator och en kompatibilitetsgenomsökning initieras inom 15 minuter efter att MMA-agenten startas om.  

Kompatibilitetsinformationen bearbetas och sammanfattas sedan i de instrumentpaneler som ingår i lösningen eller som är sökbara med användardefinierade eller fördefinierade frågor.  Lösningen rapporterar hur uppdaterad datorn är utifrån vilken källa du är konfigurerad att synkronisera med.  Om Windows-datorn är konfigurerad för att rapportera till WSUS kan resultatet variera beroende på vad Microsoft Updates visar, beroende på när WSUS senast synkroniserades med Microsoft Update.  Samma sak gäller för Linux-datorer som är konfigurerade för att rapportera till en lokal repo versus en offentlig repo.   

Du kan distribuera och installera programuppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution.  Uppdateringar som klassificeras som *valfria* ingår inte i distributionsomfattningen för Windows-datorer, utan endast nödvändiga uppdateringar.  En schemalagd distribution definierar vilka måldatorer som får tillämpliga uppdateringar, antingen genom att uttryckligen ange datorer eller välja en [datorgrupp](../log-analytics/log-analytics-computer-groups.md) som baseras på loggsökningar för en viss uppsättning datorer.  Du kan även ange ett schema för att godkänna och ange en tidsperiod när uppdateringar ska kunna installeras.  Uppdateringar installeras av runbooks i Azure Automation.  Du kan inte kan se dessa runbook-flöden och de kräver inte någon konfigurering.  När en uppdateringsdistribution skapas så skapar den ett schema som startar en masteruppdaterings-runbook vid den angivna tidpunkten för de datorer som ingår.  Denna master-runbook startar en underordnad runbook på varje agent som utför installationen av de nödvändiga uppdateringarna.       

Vid det datum och den tid som anges i uppdateringsdistributionen kör måldatorerna distributionen parallellt.  En sökning utförs först för att kontrollera om uppdateringarna fortfarande krävs och installerar dem.  Detta är viktigt att observera för WSUS-klientdatorer – om uppdateringarna inte är godkända i WSUS misslyckas uppdateringsdistributionen.  Resultaten av de tillämpade uppdateringarna vidarebefordras till OMS som ska bearbetas och sammanfattas i instrumentpaneler eller genom att söka efter händelser.     

## <a name="prerequisites"></a>Krav
* Lösningen stöder utvärderingar av uppdateringar av Windows Server 2008 eller senare och uppdateringsdistributioner av Windows Server 2012 och högre.  Installationsalternativ för Server Core och Nano Server stöds inte.
* Windows-klientoperativsystem stöds inte.  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update.  
  
    > [!NOTE]
    > Windows-agenten kan inte hanteras samtidigt av System Center Configuration Manager.  
    >
* CentOS 6 (x86/x64) och 7 (x64)
* Red Hat Enterprise 6 (x86/x64) och 7 (x64)
* SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)
* Ubuntu 12.04 LTS och senare x86/x64  
* Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.  

    > [!NOTE]
    > En OMS-agent för Linux som konfigurerats för att rapportera till flera OMS-arbetsytor stöds inte av den här lösningen.  
    > 

Mer information om hur du installerar OMS-agenten för Linux och hämtar den senaste versionen finns i [Operations Management Suite-agenten för Linux](https://github.com/microsoft/oms-agent-for-linux).  Information om hur du installerar OMS-agenten för Windows finns i [Operations Management Suite-agenten för Windows](../log-analytics/log-analytics-windows-agents.md).  

## <a name="solution-components"></a>Lösningskomponenter
Lösningen består av följande resurser som läggs till i ditt Automation-konto och ansluter direkt agenter eller Operations Manager-anslutna hanteringsgrupper. 

### <a name="management-packs"></a>Hanteringspaket
Om din hanteringsgrupp för System Center Operations Manager är ansluten till din OMS-arbetsytan installeras därefter följande hanteringspaket i Operations Manager.  Dessa hanteringspaket också har installerats på direktanslutna Windows-datorer när du lägger till den här lösningen. Det finns inget att konfigurera eller hantera med dessa hanteringspaket. 

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="hybrid-worker-groups"></a>Hybrid Worker-grupper
När du har aktiverat den här lösningen konfigureras en Windows-dator direkt som du ansluter till din OMS-arbetsyta automatiskt som en Hybrid Runbook Worker för att ge stöd för runbook-flöden som ingår i den här lösningen.  Varje Windows-dator som hanteras av lösningen listas på bladet Hybrid Runbook Worker Groups (Hybrid Runbook Worker-grupper) på Automation-kontot följt av namnkonventionen *Värdnamn FQDN_GUID*.  Du kan inte ha de här grupperna som mål med runbook-flöden i ditt konto. Då misslyckas de. Grupperna är endast avsedda att stödja hanteringslösningen.   

Du kan emellertid lägga till Windows-datorer till en Hybrid Runbook Worker-grupp i ditt Automation-konto så att den stöder runbook-flöden för Automation så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet.  Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.  

## <a name="configuration"></a>Konfiguration
Utför följande steg för att lägga till uppdateringshanteringslösningen till din OMS-arbetsyta och bekräfta att agenterna rapporterar. Windows-agenter som redan är anslutna till ditt arbetsområde läggs till automatiskt utan någon ytterligare konfiguration. 

Du kan distribuera lösningen på följande sätt:

* Från Azure Marketplace i Azure Portal genom att välja antingen erbjudandet Automatisering och kontroll eller lösningen för uppdateringshantering
* Från OMS-lösningsgalleriet i OMS-arbetsytan

Om du redan har ett Automation-konto och en OMS-arbetsyta länkade i samma resursgrupp och region verifieras din konfiguration när du väljer Automation & Control, och endast lösningen installeras och konfigureras i båda tjänsterna.  Om du väljer lösningen för uppdateringshantering från Azure Marketplace blir beteendet detsamma.  Om ingen av tjänsterna är distribuerade i din prenumeration följer du anvisningarna på bladet **Skapa ny lösning** och bekräfta att du vill installera de andra förvalda rekommenderade lösningarna.  Du kan även lägga till uppdateringshanteringslösningen i din OMS-arbetsyta med hjälp av de steg som beskrivs i [Lägg till OMS-lösningar](../log-analytics/log-analytics-add-solutions.md) från lösningsgalleriet.  

### <a name="confirm-oms-agents-and-operations-manager-management-group-connected-to-oms"></a>Bekräfta OMS-agenter och Operations Manager-hanteringsgrupper som är anslutna till OMS

Om du vill kontrollera att direktanslutna OMS-agenter för Linux och Windows kommunicerar med OMS kan du efter några minuter köra följande loggsökning:

* Linux – `Type=Heartbeat OSType=Linux | top 500000 | dedup SourceComputerId | Sort Computer | display Table`.  

* Windows – `Type=Heartbeat OSType=Windows | top 500000 | dedup SourceComputerId | Sort Computer | display Table`

På en Windows-dator kan du läsa följande för att verifiera agentanslutning med OMS:

1.  Öppna Microsoft Monitoring Agent i kontrollpanelen. På fliken **Azure Log Analytics (OMS)** visar agenten ett meddelande där det står: **The Microsoft Monitoring Agent has successfully connected to the Microsoft Operations Management Suite service** (Microsoft Monitoring Agent har anslutits till tjänsten Microsoft Operations Management Suite).   
2.  Öppna Windows Event Log, gå till **Program- och tjänstloggar\Operations Manager** och sök efter händelse-ID 3000 och 5002 från källans tjänstanslutning.  Dessa händelser anger att datorn har registrerats med OMS-arbetsytan och tar emot konfigurationen.  

Om agenten inte kan kommunicera med OMS-tjänsten och den är konfigurerad för att kommunicera med internet genom en brandvägg eller proxyserver kan du bekräfta att brandväggen eller proxyservern har konfigurerats korrekt genom att läsa [Konfigurera inställningar för proxy och brandvägg i Log Analytics](../log-analytics/log-analytics-proxy-firewall.md).
  
Nya Linux-agenter visar statusen **Uppdaterad** när en utvärdering har utförts.  Den här processen kan ta upp till 6 timmar. 

För att bekräfta att en Operations Manager-hanteringsgrupp kommunicerar med OMS läser du [Validate Operations Manager Integration with OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms) (Verifiera Operations Manager-integrering med OMS).

## <a name="data-collection"></a>Datainsamling
### <a name="supported-agents"></a>Agenter som stöds
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Linux-agenter och initierar installationen av nödvändiga uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br>En direktanslutning från Operations Manager-agenten till Log Analytics krävs inte. Data vidarebefordras från hanteringsgruppen till OMS-databasen. |
| Azure Storage-konto |Nej |Azure Storage inkluderar inte information om systemuppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens
För varje hanterad Windows-dator utförs en genomsökning två gånger per dag. Var 15:e minut anropas Windows API för att fråga efter den senaste uppdateringstiden för att fastställa om statusen har ändrats, och i så fall om en fullständig genomsökning har initierats.  För varje hanterad Linux-dator utförs en sökning var tredje timme. 

Det kan ta alltifrån 30 minuter upp till 6 timmar för instrumentpanelen att visa uppdaterade data från hanterade datorer.   

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till uppdateringshanteringslösningen i OMS-arbetsytan läggs panelen **Uppdateringshantering** till på OMS-instrumentpanelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Sammanfattningspanel uppdateringshantering](media/oms-solution-update-management/update-management-summary-tile.png)  


## <a name="viewing-update-assessments"></a>Visning av kontroll av uppdateringar
Klicka på ikonen **Uppdateringshantering** för att öppna instrumentpanelen **Uppdateringshantering**.<br><br> ![Instrumentpanel för sammanfattning av uppdateringshantering](./media/oms-solution-update-management/update-management-dashboard.png)<br> 

Den här instrumentpanelen innehåller en detaljerad analys av uppdateringsstatus efter typ av operativsystem och uppdateringsklassificering – kritiskt, säkerhet med flera (till exempel definitionsuppdatering). När panelen **Distributioner av uppdateringar** är vald omdirigeras du till sidan Distributioner av uppdateringar där du kan visa scheman, distributioner som för närvarande körs, slutförda distributioner eller schemalägga en ny distribution.  

Du kan köra en loggsökning som returnerar alla poster genom att klicka på den specifika panelen. Om du vill köra en fråga i en viss kategori och för fördefinierade villkor väljer du någon från tillgänglighetslistan under kolumnen **Vanliga frågor om uppdatering**.    

## <a name="installing-updates"></a>Installera uppdateringar
När uppdateringar har utvärderats för alla Linux- och Windows-datorer i din arbetsyta kan du installera nödvändiga uppdateringar genom att skapa en *Uppdateringsdistribution*.  En uppdateringsdistribution är en schemalagd installation av nödvändiga uppdateringar för en eller flera datorer.  Du kan ange datum och tid för distributionen förutom den dator eller grupp av datorer som ska inkluderas i distribueringens omfång.  Läs mer om datorgrupper i [Computer groups in Log Analytics](../log-analytics/log-analytics-computer-groups.md) (Datorgrupper i Log Analytics).  När du inkluderar datorgrupper i din distribution utvärderas gruppmedlemskap bara en gång när schemat skapas.  Efterföljande ändringar i en grupp visas inte.  Undvik detta genom att ta bort den schemalagda distributionen och återskapa den. 

> [!NOTE]
> Virtuella Windows-datorer som distribueras från Azure Marketplace som standard är inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten.  Det beteendet ändras inte när du har lagt till lösningen eller virtuella Windows-datorer i arbetsytan.  Om du inte aktivt har hanterat uppdateringar med den här lösningen gäller standardbeteendet (uppdateringar tillämpas automatiskt).  

För virtuella datorer som skapats från de Red Hat Enterprise Linux (RHEL)-avbildningar på begäran som finns i Azure Marketplace är de registrerade för att få åtkomst till [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) som är distribuerat i Azure.  Andra Linux-distributioner måste uppdateras från distributionens fildatabas på nätet genom de metoder som distributionerna stöder.  

### <a name="viewing-update-deployments"></a>Visa uppdateringsdistributioner
Klicka på ikonen **Uppdateringsdistribution** för att visa en lista över befintliga uppdateringsdistributioner.  De grupperas efter status – **Schemalagda**, **Kör** och **Slutförda**.<br><br> ![Uppdatera sidan med scheman över uppdateringsdistributioner](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

De egenskaper som visas för varje uppdateringsdistribution beskrivs i följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Namn |Uppdateringsdistributionens namn. |
| Schema |Typ av schema.  Det tillgängliga alternativen är *En gång*, *Återkommande veckovis* eller *Återkommer månadsvis*. |
| Starttid |Datum och tid då uppdateringsdistributionen har schemalagts att starta. |
| Varaktighet |Antalet minuter som uppdateringsdistributionen har tillåtelse att köra.  Om alla uppdateringar inte installeras inom denna tidsrymd måste de återstående uppdateringarna vänta tills nästa uppdateringsdistribution. |
| Servrar |Antal datorer som påverkas av uppdateringsdistributionen.  |
| Status |Uppdateringsdistributionens aktuella status.<br><br>Möjliga värden:<br>– Ej startad<br>- Kör<br>- Färdig |

Välj en slutförd uppdateringsdistribution för att visa detaljerad information inklusive kolumnerna i följande tabell.  Dessa kolumner är inte ifyllda inte om uppdateringsdistributionen inte har startats.<br><br> ![Översikt över uppdateringsdistributionens resultat](./media/oms-solution-update-management/update-management-deploymentresults-dashboard.png)

| Kolumn | Beskrivning |
| --- | --- |
| **Vy över datorer** | |
| Windows-datorer |Visar antalet Windows-datorer i uppdateringsdistributionen efter status.  Klicka på en status för att köra en loggsökning som returnerar alla uppdaterade poster med denna status för uppdateringsdistributionen. |
| Linux-datorer |Visar antalet Linux-datorer i uppdateringsdistributionen efter status.  Klicka på en status för att köra en loggsökning som returnerar alla uppdaterade poster med denna status för uppdateringsdistributionen. |
| Installationsstatus för datorer |Visar vilka datorer som ingår i uppdateringsdistributionen och andelen uppdateringar som har installerats i procent. Klicka på någon av posterna för att köra en loggsökning som returnerar alla saknade och kritiska uppdateringar. |
| **Vy över uppdateringar** | |
| Windows-uppdateringar |Listar Windows-uppdateringar som ingår i Uppdatera distribution och deras installationsstatus för varje uppdatering.  Välj en uppdatering för att köra en loggsökning som ger alla uppdateringsposter för den specifika uppdateringen, eller klicka på statusen för att köra en loggsökning som ger alla uppdateringsposter för distributionen. | 
| Linux-uppdateringar |Listar Linux-uppdateringar som ingår i Uppdatera distribution och deras installationsstatus för varje uppdatering.  Välj en uppdatering för att köra en loggsökning som ger alla uppdateringsposter för den specifika uppdateringen, eller klicka på statusen för att köra en loggsökning som ger alla uppdateringsposter för distributionen. | 

### <a name="creating-an-update-deployment"></a>Skapa en uppdateringsdistribution
Skapa en ny uppdateringsdistribution genom att klicka på knappen **Lägg till** längst upp på skärmen för att öppna sidan **Ny uppdateringsdistribution**.  Du måste ange värden för egenskaperna i följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Namn |Unikt namn som identifierar uppdateringsdistributionen. |
| Tidszon |Tidszonen för starttiden. |
| Schematyp | Typ av schema.  Det tillgängliga alternativen är *En gång*, *Återkommande veckovis* eller *Återkommer månadsvis*.  
| Starttid |Datum och tidpunkt för start av uppdateringsdistributionen. **Obs!** Om du behöver distribuera omedelbart går det att köra en distribution tidigast 30 minuter efter den aktuella tiden. |
| Varaktighet |Antalet minuter som uppdateringsdistributionen har tillåtelse att köra.  Om alla uppdateringar inte installeras inom denna tidsrymd måste de återstående uppdateringarna vänta tills nästa uppdateringsdistribution. |
| Datorer |Namnen på datorer eller datorgrupper som ska ingå och mål för uppdateringsdistributionen.  Markera en eller flera poster i listrutan. |

<br><br> ![Ny sida för uppdateringsdistribution](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Tidsintervall
Som standard är omfånget för de data som analyseras i uppdateringshanteringslösningen från alla anslutna hanteringsgrupper genererade inom den senaste dagen. 

Om du vill ändra tidsintervallet för data väljer du **Databaserat för** längst upp på instrumentpanelen. Du kan välja poster som skapats eller uppdaterats under de senaste 7 dagarna, senaste dagen eller 6 timmarna. Du kan även välja **Anpassat** och ange ett eget datumintervall.

## <a name="log-analytics-records"></a>Log Analytics-poster
Uppdateringshanteringslösningen skapar två typer av poster i OMS-databasen.

### <a name="update-records"></a>Uppdateringsposter
En post med typen av **uppdatering** skapas för varje uppdatering som antingen installerats eller behövs på varje dator. Uppdateringsposter har egenskaper enligt följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Typ |*Uppdatering* |
| SourceSystem |Källan som godkände installationen av uppdateringen.<br>Möjliga värden:<br>- Microsoft Update<br>– Windows Update<br>– SCCM<br>- Linux-servrar (hämtade från pakethanterare) |
| Godkända |Anger om uppdateringen har godkänts för installation.<br> För Linux-servrar är detta för närvarande valfritt eftersom korrigeringar inte hanteras av OMS. |
| Klassificering för Windows |Uppdateringens klassificering.<br>Möjliga värden:<br>-    Program<br>- Kritisk uppdatering<br>- Definitionsuppdatering<br>- Funktionspaket<br>- Säkerhetsuppdatering<br>- Service pack<br>- Samlad uppdatering<br>- Uppdatering |
| Klassificering för Linux |Uppdateringens klassificering.<br>Möjliga värden:<br>- Kritisk uppdatering<br>- Säkerhetsuppdatering<br>- Övriga uppdateringar |
| Dator |Datorns namn. |
| InstallTimeAvailable |Anger om tidpunkten för installationen är tillgänglig från andra agenter som installerat samma uppdatering. |
| InstallTimePredictionSeconds |Beräknad installationstid i sekunder baserat på andra agenter som installerat samma uppdatering. |
| KBID |ID för den KB-artikel som beskriver uppdateringen. |
| ManagementGroupName |Namnet på hanteringsgruppen för SCOM-agenter.  För övriga agenter är detta AOI-<workspace ID>. |
| MSRCBulletinID |ID för den säkerhetsbulletin från Microsoft som beskriver uppdateringen. |
| MSRCSeverity |Allvarlighetsgraden i säkerhetsbulletinen från Microsoft.<br>Möjliga värden:<br>- Kritiskt<br>- Viktig<br>- Mellan |
| Valfri |Anger om uppdateringen är valfri. |
| Produkt |Namnet på den produkt som uppdateringen gäller för.  Klicka på **Visa** för att öppna artikeln i en webbläsare. |
| PackageSeverity |Allvarlighetsgraden för de problem som åtgärdas i den här uppdateringen, enligt rapport från Linux distributionsleverantörer. |
| PublishDate |Datum och tid när uppdateringen installerades. |
| RebootBehavior |Anger om uppdateringen kräver omstart.<br>Möjliga värden:<br>- canrequestreboot<br>- neverreboots |
| RevisionNumber |Uppdateringens revisionsnummer. |
| SourceComputerId |GUID för unik identifiering av datorn. |
| TimeGenerated |Datum och tid då posten senast uppdaterades. |
| Rubrik |Uppdateringens titel. |
| UpdateID |GUID för unik identifiering av uppdateringen. |
| UpdateState |Anger om uppdateringen är installerad på den här datorn.<br>Möjliga värden:<br>- Installerad - Uppdateringen är installerad på den här datorn.<br>- Krävs - Uppdateringen är inte installerad och krävs på den här datorn. |

När du utför en loggsökning som returnerar poster med typ av **Uppdatering** kan du välja vyn **Uppdateringar** som visar en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen. Du kan klicka på posterna i panelerna **Saknade och tillämpade uppdateringar** och **Nödvändiga och valfria uppdateringar** för att vyn ska omfatta dessa uppdateringar. Välj vyn **Lista** eller **Tabell** för att returnera de enskilda posterna.<br> 

![En loggsöknings uppdateringsvy med poster över uppdateringstyp](./media/oms-solution-update-management/update-la-view-updates.png)  

I vyn **Tabell** kan du klicka på **KBID** tillhörande en post för att öppna en webbläsare med KB-artikeln. Detta gör att du snabbt kan läsa information om en viss uppdatering.<br> 

![En loggsöknings tabellvy med paneler med poster över uppdateringstyp](./media/oms-solution-update-management/update-la-view-table.png)

I vyn **Lista** kan du klicka på länken **Visa** bredvid KBID för att öppna KB-artikeln.<br>

![En loggsöknings listvy med paneler med poster över uppdateringstyp](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>UpdateSummary-poster
En post med en typ av **UpdateSummary** skapas för varje Windows-agentdator. Den här posten uppdateras varje gång datorn genomsöks efter uppdateringar. **UpdateSummary**-poster har egenskaper enligt följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Typ |UpdateSummary |
| SourceSystem |OpsManager |
| Dator |Datorns namn. |
| CriticalUpdatesMissing |Antalet kritiska uppdateringar som datorn saknar. |
| ManagementGroupName |Namnet på hanteringsgruppen för SCOM-agenter. För övriga agenter är detta AOI-<workspace ID>. |
| NETRuntimeVersion |Den version av .NET-Runtime som finns installerad på datorn. |
| OldestMissingSecurityUpdateBucket |Bucket för att kategorisera den tidpunkt då den äldsta saknade säkerhetsuppdateringen på den här datorn publicerades.<br>Möjliga värden:<br>- Äldre<br>-    180 dagar sedan<br>- 150 dagar sedan<br>-    120 dagar sedan<br>- 90 dagar sedan<br>- 60 dagar sedan<br>-    30 dagar sedan<br>-    Nyligen |
| OldestMissingSecurityUpdateInDays |Antalet dagar sedan den äldsta saknade säkerhetsuppdateringen på den här datorn publicerades. |
| OsVersion |Den version av operativsystemet som finns installerad på datorn. |
| OtherUpdatesMissing |Antalet andra uppdateringar som datorn saknar. |
| SecurityUpdatesMissing |Antalet andra säkerhetsuppdateringar som datorn saknar. |
| SourceComputerId |GUID för unik identifiering av datorn. |
| TimeGenerated |Datum och tid då posten senast uppdaterades. |
| TotalUpdatesMissing |Det totala antalet uppdateringar som datorn saknar. |
| WindowsUpdateAgentVersion |Windows Update-agentens versionsnummer på datorn. |
| WindowsUpdateSetting |Inställningen för hur datorn ska installera viktiga uppdateringar.<br>Möjliga värden:<br>- Inaktiverad<br>- Meddela innan installation<br>- Schemalagd installation |
| WSUSServer |URL för WSUS-servern om datorn är konfigurerad för att använda en sådan. |

## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på sökningar i loggen för uppdateringsposter som har samlats in av den här lösningen. 

| Fråga | Beskrivning |
| --- | --- |
|Datorer Windows-baserade servrar som behöver uppdateras |`Type:Update OSType!=Linux UpdateState=Needed Optional=false Approved!=false | mät antal() efter dator` |
|Linux-servrar som behöver uppdateringar | `Type:Update OSType=Linux UpdateState!="Not needed" | mät antal() efter dator` |
| Alla datorer med saknade uppdateringar |`Type=Update UpdateState=Needed Optional=false | välj Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate` |
| Saknade uppdateringar fören specifik dator (ersätt värdet med namnet på din egen dator) |`Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" | välj Computer,Title,KBID,Product,UpdateSeverity,PublishedDate` |
| Alla datorer som saknar kritiska uppdateringar eller säkerhetsuppdateringar |`Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates"`) |
| Kritiska uppdateringar eller säkerhetsuppdateringar som krävs för datorer där uppdateringarna görs manuellt |`Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | Distinct Computer} | Distinct KBID` |
| Felhändelser för datorer som saknar kritiska uppdateringar eller säkerhetsuppdateringar |`Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false | Distinct Computer}` |
| Alla datorer med saknade samlade uppdateringar |`Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed| välj Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate` |
| Separata, saknade uppdateringar bland samtliga datorer |`Type=Update UpdateState=Needed Optional=false | Distinct Title` |
| Dator med Windows-baserad server med uppdateringar som misslyckades i en uppdateringskörning | `Type:UpdateRunProgress InstallationStatus=failed | mät antal() efter dator, Titel, UpdateRunName` |
| Linux-server med misslyckade uppdateringar vid en uppdateringskörning |`Type:UpdateRunProgress InstallationStatus=failed | mät antal() efter dator, Produkt, UpdateRunName` |
| WSUS datormedlemskap |`Type=UpdateSummary | mät antal() efter WSUSServer` |
| Konfigurering av automatiska uppdateringar |`Type=UpdateSummary | mät antal() efter WindowsUpdateSetting` |
| Datorer med automatiska uppdateringar inaktiverat |`Type=UpdateSummary WindowsUpdateSetting=Manual` |
| Lista över alla Linux-datorer som har en tillgänglig paketuppdatering |`Type=Update and OSType=Linux and UpdateState!="Not needed" | mät antal() efter dator` |
| Lista över alla Linux-datorer som har en tillgänglig paketuppdatering vilken åtgärdar kritiska problem eller säkerhetsproblem |`Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") | mät antal() efter dator` |
| Lista över alla paket som har en tillgänglig uppdatering |Type=Update and OSType=Linux and UpdateState!="Not needed" |
| Lista över alla paket som har en tillgänglig uppdatering vilken åtgärdar kritiska problem eller säkerhetsproblem |`Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates")` |
| Lista vilka uppdateringsdistributioner som har ändrade datorer |`Type:UpdateRunProgress | mät antal() efter UpdateRunName` |
|Datorer som har uppdaterats i den här uppdateringskörningen (ersätt värdet med uppdateringsdistributionens namn |`Type:UpdateRunProgress UpdateRunName="DeploymentName" | mät antal() efter dator` |
| Lista över alla "Ubuntu"-datorer med en tillgänglig uppdatering |`Type=Update and OSType=Linux and OSName = Ubuntu &| mät antal() efter dator` |

## <a name="troubleshooting"></a>Felsökning 

Det här avsnittet innehåller information för att hjälpa dig att felsöka fel med lösningen Hantering av uppdateringar.  

### <a name="how-do-i-troubleshoot-update-deployments"></a>Hur felsöker jag distributioner av uppdateringar?
Du kan visa resultatet för den runbook som ansvarar för att distribuera uppdateringarna som ingår i den schemalagda distributionen från bladet Jobb i ditt Automation-konto som är länkat till OMS-arbetsytan som stöder lösningen.  Runbooken **Patch-MicrosoftOMSComputer** är en underordnad runbook som har en specifik hanterad dator som mål. Om du granskar den utförliga strömmen visas detaljerad information för den distributionen.  Utdata visar vilka nödvändiga uppdateringar som är tillämpliga, hämtningsstatus, status för installationen och ytterligare information.<br><br> ![Jobbstatus för uppdateringsdistribution](media/oms-solution-update-management/update-la-patchrunbook-outputstream.png)<br>

Mer information finns i [Automation runbook output and messages](../automation/automation-runbook-output-and-messages.md) (Utdata och meddelanden för Automation-runbook).   
  
## <a name="next-steps"></a>Nästa steg
* Använd loggsökningar i [Log Analytics](../log-analytics/log-analytics-log-searches.md) för att visa detaljerad uppdateringsinformation.
* [Skapa egna instrumentpaneler](../log-analytics/log-analytics-dashboards.md) som visar uppdateringskompatibilitet för dina hanterade datorer.
* [Skapa aviseringar](../log-analytics/log-analytics-alerts.md) som visas när viktiga uppdateringar saknas på datorer eller när automatiska uppdateringar är inaktiverade för en dator.  


