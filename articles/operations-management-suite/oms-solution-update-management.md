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
ms.date: 03/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 18aa17f6af7fe492f3875e1af7cb06b613f171af
ms.lasthandoff: 04/03/2017


---
# <a name="update-management-solution-in-oms"></a>Uppdateringshanteringslösning i OMS
Uppdateringshanteringslösningen i OMS gör att du kan du hantera uppdateringar för dina Windows- och Linux-datorer.  Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och starta processen för att installera nödvändiga uppdateringar för servrar. 

## <a name="prerequisites"></a>Krav
* Lösningen stöder endast utvärderingar av uppdateringar av Windows Server 2008 eller senare och uppdateringsdistributioner av Windows Server 2012 och högre.  Installationsalternativ för Server Core och Nano Server stöds inte.
* Windows-klientoperativsystem stöds inte.  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update.  
  
  > [!NOTE]
  > Windows-agenten kan inte hanteras samtidigt av System Center Configuration Manager.  
  > 
  > 
* Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.  OMS-agenten för Linux hämtas från [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Konfiguration
Utför följande steg för att lägga till uppdateringshanteringslösningen till din OMS-arbetsyta och lägga till Linux-agenter. Windows-agenter läggs till automatiskt utan någon ytterligare konfiguration.

> [!NOTE]
> Om du aktiverar den här lösningen konfigureras en Windows-dator som du ansluter till din OMS-arbetsyta automatiskt som en Hybrid Runbook Worker för att ge stöd för runbooks som ingår i den här lösningen.  Den är emellertid inte registrerad med några Hybrid Worker-grupper som du redan kanske har definierat i ditt Automation-konto.  Du kan lägga till den till en Hybrid Runbook Worker-grupp i ditt Automation-konto så att den stöder Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet.  Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.   

1. Lägg till uppdateringshanteringslösningen i din OMS-arbetsyta med hjälp av den process som beskrivs i [Lägg till OMS-lösningar](../log-analytics/log-analytics-add-solutions.md) från lösningsgalleriet.  
2. I OMS-portalen OMS väljer du **Inställningar** och sedan **Anslutna källor**.  Observera **arbetsyte-ID** och antingen **primärnyckel** eller **sekundärnyckel**.
3. Utför följande steg för varje Linux-dator.
   
   a.    Installera den senaste versionen av OMS-agent för Linux genom att köra följande kommandon.  Ersätt <Workspace ID> med arbetsyte-ID och <Key> med antingen primär- eller sekundärnyckel.
   
        cd ~
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID>  -s <PrimaryKey> -d opinsights.azure.com 

   b. Ta bort agenten med hjälp av den process som beskrivs i avsnittet [Avinstallera OMS-agenten för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#uninstalling-the-oms-agent-for-linux).  

## <a name="management-packs"></a>Hanteringspaket
Om din hanteringsgrupp för System Center Operations Manager är ansluten till din OMS-arbetsytan installeras därefter följande hanteringspaket i Operations Manager när du lägger till den här lösningen. Det krävs ingen konfigurering eller underhåll av dessa hanteringspaket. 

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

Mer information om hur lösningens hanteringspaket uppdateras finns i [Anslut Operations Manager till Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Datainsamling
### <a name="supported-agents"></a>Agenter som stöds
I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Lösningen samlar in information om systemuppdateringar från Linux-agenter. |
| Operations Manager-hanteringsgrupp |Ja |Lösningen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp.<br>En direktanslutning från Operations Manager-agenten till Log Analytics krävs inte. Data vidarebefordras från hanteringsgruppen till OMS-databasen. |
| Azure Storage-konto |Nej |Azure Storage inkluderar inte information om systemuppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens
För varje hanterad Windows-dator utförs en genomsökning två gånger per dag.  När en uppdatering har installerats uppdateras dess information inom 15 minuter.  

För varje hanterad Linux-dator utförs en sökning var tredje timme.  

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till uppdateringshanteringslösningen i OMS-arbetsytan läggs panelen **Uppdateringshantering** till på OMS-instrumentpanelen. Den här panelen visar antal och en grafisk representation av hur många datorer i din miljö som för närvarande kräver systemuppdateringar.<br><br>
![Sammanfattningspanel uppdateringshantering](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Visning av kontroll av uppdateringar
Klicka på ikonen **Uppdateringshantering** för att öppna instrumentpanelen **Uppdateringshantering**. Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar upp till tio objekt som matchar denna kolumns kriterier för specificerat omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
| --- | --- |
| **Datorer som saknar uppdateringar** | |
| Kritiska eller säkerhetsuppdateringar |Visar upp de tio datorer som främst saknar uppdateringar, sorterade efter antalet uppdateringar som saknas. Klicka på ett datornamn för att köra en loggsökning som returnerar alla uppdateringsposter för denna dator. |
| Kritiska eller säkerhetsuppdateringar som är äldre än 30 dagar |Identifierar antalet datorer som saknar kritiska eller säkerhetsuppdateringar, grupperade efter hur lång tid det är sedan uppdateringen publicerades. Klicka på någon av posterna för att köra en loggsökning som returnerar alla saknade och kritiska uppdateringar. |
| **Nödvändiga uppdateringar som saknas** | |
| Kritiska eller säkerhetsuppdateringar |Visar klassificeringar av uppdateringar som datorer saknar, sorterat efter antalet datorer som saknar uppdateringar i kategorin. Klicka på en klassificering för att köra en loggsökning som returnerar alla uppdateringsposter för denna klassificering. |
| **Distributioner av uppdateringar** | |
| Distributioner av uppdateringar |Antalet aktuella, schemalagda distributioner av uppdateringar och tiden fram till nästa schemalagda körning.  Klicka på panelen för att visa scheman, körningar som för närvarande görs och slutförda uppdateringar eller för att schemalägga en ny distribution. |

<br>  
![Instrumentpanel för sammanfattning av uppdateringshantering](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Instrumentpanelsvy över uppdateringshantering för dator](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Instrumentpanelsvy över uppdateringshantering för paket](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Installera uppdateringar
När uppdateringar har utvärderats för alla Windows-datorer i din miljö kan du installera nödvändiga uppdateringar genom att skapa en *Uppdateringsdistribution*.  En uppdateringsdistribution är en schemalagd installation av nödvändiga uppdateringar för en eller flera Windows-datorer.  Du kan ange datum och tid för distributionen förutom den dator eller grupp av datorer som ska inkluderas.  

Uppdateringar installeras av runbooks i Azure Automation.  Du kan inte kan se dessa runbook-flöden och de kräver inte någon konfigurering.  När en uppdateringsdistribution skapas så skapar den ett schema som startar en masteruppdaterings-runbook vid den angivna tidpunkten för de datorer som ingår.  Denna master-runbook startar en underordnad runbook på varje Windows-agent som utför installationen av de nödvändiga uppdateringarna.  

För virtuella datorer som skapats från de Red Hat Enterprise Linux (RHEL)-avbildningar på begäran som finns i Azure Marketplace är de registrerade för att få åtkomst till [Red Hat Update Infrastructure (RHUI)](../virtual-machines/linux/update-infrastructure-redhat.md) som är distribuerat i Azure.  Andra Linux-distributioner måste uppdateras från distributionens fildatabas på nätet genom de metoder som distributionerna stöder.  

### <a name="viewing-update-deployments"></a>Visa uppdateringsdistributioner
Klicka på ikonen **Uppdateringsdistribution** för att visa en lista över befintliga uppdateringsdistributioner.  De grupperas efter status – **Schemalagda**, **Kör** och **Slutförda**.<br><br> ![Uppdatera sidan med scheman över uppdateringsdistributioner](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

De egenskaper som visas för varje uppdateringsdistribution beskrivs i följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Namn |Uppdateringsdistributionens namn. |
| Schema |Typ av schema.  *OneTime* är för närvarande det enda möjliga värdet. |
| Starttid |Datum och tid då uppdateringsdistributionen har schemalagts att starta. |
| Varaktighet |Antalet minuter som uppdateringsdistributionen har tillåtelse att köra.  Om alla uppdateringar inte installeras inom denna tidsrymd måste de återstående uppdateringarna vänta tills nästa uppdateringsdistribution. |
| Servrar |Antal datorer som påverkas av uppdateringsdistributionen. |
| Status |Uppdateringsdistributionens aktuella status.<br><br>Möjliga värden:<br>-    Ej startad<br>- Kör<br>- Färdig |

Klicka på en uppdateringsdistribution för att visa detaljerad information inklusive kolumnerna i följande tabell.  Dessa kolumner är inte ifyllda inte om uppdateringsdistributionen inte har startats.<br>

| Kolumn | Beskrivning |
| --- | --- |
| **Datorresultat** | |
| Slutförda |Visar antalet datorer i uppdateringsdistributionen efter status.  Klicka på en status för att köra en loggsökning som returnerar alla uppdaterade poster med denna status för uppdateringsdistributionen. |
| Installationsstatus för datorer |Visar vilka datorer som ingår i uppdateringsdistributionen och andelen uppdateringar som har installerats i procent. Klicka på någon av posterna för att köra en loggsökning som returnerar alla saknade och kritiska uppdateringar. |
| **Uppdatera instansresultat** | |
| Installationsstatus för instanser |Visar klassificeringar av uppdateringar som datorer saknar, sorterat efter antalet datorer som saknar uppdateringar i kategorin. Klicka på en dator för att köra en loggsökning som returnerar alla uppdateringsposter för denna dator. |

<br><br> ![Översikt över uppdateringsdistributionens resultat](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Skapa en uppdateringsdistribution
Skapa en ny uppdateringsdistribution genom att klicka på knappen **Lägg till** längst upp på skärmen för att öppna sidan **Ny uppdateringsdistribution**.  Du måste ange värden för egenskaperna i följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Namn |Unikt namn som identifierar uppdateringsdistributionen. |
| Tidszon |Tidszonen för starttiden. |
| Starttid |Datum och tidpunkt för start av uppdateringsdistributionen. |
| Varaktighet |Antalet minuter som uppdateringsdistributionen har tillåtelse att köra.  Om alla uppdateringar inte installeras inom denna tidsrymd måste de återstående uppdateringarna vänta tills nästa uppdateringsdistribution. |
| Datorer |Namnen på datorer eller datorgrupper som ska ingå i uppdateringsdistributionen.  Markera en eller flera poster i listrutan. |

<br><br> ![Ny sida för uppdateringsdistribution](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Tidsintervall
Som standard är omfånget för de data som analyseras i uppdateringshanteringslösningen från alla anslutna hanteringsgrupper genererade inom den senaste dagen. 

Om du vill ändra tidsintervallet för data väljer du **Databaserat för** längst upp på instrumentpanelen. Du kan välja poster som skapats eller uppdaterats under de senaste 7 dagarna, senaste dagen eller 6 timmarna. Du kan även välja **Anpassat** och ange ett eget datumintervall.<br><br> ![Anpassat alternativ för tidsintervall](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Log Analytics-poster
Uppdateringshanteringslösningen skapar två typer av poster i OMS-databasen.

### <a name="update-records"></a>Uppdateringsposter
En post med typen av **uppdatering** skapas för varje uppdatering som antingen installerats eller behövs på varje dator. Uppdateringsposter har egenskaper enligt följande tabell.

| Egenskap | Beskrivning |
| --- | --- |
| Typ |*Uppdatering* |
| SourceSystem |Källan som godkände installationen av uppdateringen.<br>Möjliga värden:<br>- Microsoft Update<br>-    Windows Update<br>-    SCCM<br>- Linux-servrar (hämtade från pakethanterare) |
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

<br>
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
| Alla datorer med saknade uppdateringar |Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| Saknade uppdateringar för datorn "COMPUTER01.contoso.com" (ersätt med namnet på din egen dator) |Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |
| Alla datorer som saknar kritiska uppdateringar eller säkerhetsuppdateringar |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") |
| Kritiska uppdateringar eller säkerhetsuppdateringar som krävs för datorer där uppdateringarna görs manuellt |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |
| Felhändelser för datorer som saknar kritiska uppdateringar eller säkerhetsuppdateringar |Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false &#124; Distinct Computer} |
| Alla datorer med saknade samlade uppdateringar |Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| Separata, saknade uppdateringar bland samtliga datorer |Type=Update UpdateState=Needed Optional=false &#124; Distinct Title |
| WSUS datormedlemskap |Type=UpdateSummary &#124; measure count() by WSUSServer |
| Konfigurering av automatiska uppdateringar |Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |
| Datorer med automatiska uppdateringar inaktiverat |Type=UpdateSummary WindowsUpdateSetting=Manual |
| Lista över alla Linux-datorer som har en tillgänglig paketuppdatering |Type=Update and OSType=Linux and UpdateState!="Not needed" &#124; measure count() by Computer |
| Lista över alla Linux-datorer som har en tillgänglig paketuppdatering vilken åtgärdar kritiska problem eller säkerhetsproblem |Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") &#124; measure count() by Computer |
| Lista över alla paket som har en tillgänglig uppdatering |Type=Update and OSType=Linux and UpdateState!="Not needed" |
| Lista över alla paket som har en tillgänglig uppdatering vilken åtgärdar kritiska problem eller säkerhetsproblem |Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") |
| Lista över alla "Ubuntu"-datorer med en tillgänglig uppdatering |Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |

## <a name="next-steps"></a>Nästa steg
* Använd loggsökningar i [Log Analytics](../log-analytics/log-analytics-log-searches.md) för att visa detaljerad uppdateringsinformation.
* [Skapa egna instrumentpaneler](../log-analytics/log-analytics-dashboards.md) som visar uppdateringskompatibilitet för dina hanterade datorer.
* [Skapa aviseringar](../log-analytics/log-analytics-alerts.md) som visas när viktiga uppdateringar saknas på datorer eller när automatiska uppdateringar är inaktiverade för en dator.  


