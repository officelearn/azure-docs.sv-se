<properties
   pageTitle="Översikt över Operations Management Suite (OMS) | Microsoft Azure"
   description="Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala och molnbaserade infrastruktur.  I den här artikeln identifierar vi de olika tjänster som ingår i OMS. Artikeln innehåller också länkar till mer detaljerat innehåll om tjänsterna."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/05/2016"
   ms.author="bwren" />


# Översikt över Operations Management Suite (OMS)

Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala och molnbaserade infrastruktur.  Eftersom OMS implementeras som en molnbaserad tjänst kan du snabbt komma igång med minimal investering i infrastrukturtjänster.  Nya funktioner levereras automatiskt, vilket gör att du slipper löpande underhåll och uppgraderingskostnader.

Förutom att själv tillhandahålla värdefulla tjänster kan OMS integreras med System Center-komponenter som System Center Operations Manager för att utöka din befintliga hanteringsinvestering i molnet.  System Center och OMS kan tillsammans tillhandahålla en komplett hybridhanteringsmiljö.

Följande avsnitt innehåller en översiktlig beskrivning av de olika områdena i OMS och de tjänster som implementerar dem.  Avsnittet om OMS-arkitekturen innehåller en översikt över de olika OMS-komponenterna, som du kan titta igenom innan du läser den detaljerade dokumentationen för varje komponent.


## ![Insikter och analys](media/operations-management-suite-overview/icon-insight-analytics.png) Insikter och analys

Med [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) kan du samla in, korrelera, söka i och agera på logg- och prestandadata som genereras av operativsystem och program. Funktionen ger realtidsinsyn i användningen med hjälp av integrerade sökinstrumentpaneler och anpassade instrumentpaneler så att du snabbt kan analysera flera miljoner poster över alla arbetsbelastningar och servrar oavsett deras fysiska plats.

Du kan enkelt lägga till lösningar i Log Analytics som definierar de data som ska samlas in och logiken för analys.  Lösningar kan innehålla ytterligare funktioner som skickas automatiskt till agenter med minimal eller ingen konfiguration.  Förutom att använda analysverktygen som tillhandahålls av enskilda lösningar kan du göra anpassade sökningar i hela datauppsättningen för att korrelera data mellan system och program.  


## ![Automatisering och kontroll](media/operations-management-suite-overview/icon-automation-control.png) Automatisering och kontroll

Azure Automation automatiserar administrativa processer med [runbooks](../automation/automation-runbook-types.md) som baseras på PowerShell och som körs i Azure-molnet.  Runbooks kan komma åt alla produkter eller tjänster som kan hanteras med PowerShell, inklusive resurser i andra moln, till exempel Amazon Web Services (AWS).  Runbooks kan också köras på en server i ditt lokala datacenter för att hantera lokala resurser.

Azure Automation tillhandahåller konfigurationshantering med [PowerShell DSC](../automation/automation-dsc-overview.md).  Du kan skapa och hantera DSC-resurser som finns i Azure och använda dem i molnbaserade och lokala system för att definiera och automatiskt genomdriva deras konfiguration.


## ![Skydd och återställning](media/operations-management-suite-overview/icon-protection-recovery.png) Skydd och haveriberedskap

[Azure Backup](http://azure.microsoft.com/documentation/services/backup) skyddar dina programdata och behåller dem i flera år utan stora investeringar och med minimala driftskostnader.  Funktionen kan säkerhetskopiera data från fysiska och virtuella Windows-servrar och programarbetsbelastningar som SQL Server och SharePoint.  Den kan också användas av System Center Data Protection Manager (DPM) för att replikera skyddade data till Azure för redundans och långsiktig lagring.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att samordna replikering, redundansväxling och återställning av lokal virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows- eller Linux-servrar. Du kan replikera datorer till ett sekundärt datacenter eller utöka ditt datacenter genom att replikera dem till Azure. Site Recovery tillhandahåller också enkel redundansväxling och återställning för arbetsbelastningar. Funktionen kan integreras med mekanismer för haveriberedskap som SQL Server AlwaysOn och tillhandahåller återställningsplaner för enkel redundansväxling av arbetsbelastningar som är nivåindelade över flera datorer.


## ![Säkerhet och efterlevnad i OMS](media/operations-management-suite-overview/icon-security-compliance.png) Säkerhet och efterlevnad
Säkerhets- och efterlevnadsfunktionen hjälper dig att identifiera, utvärdera och undvika säkerhetsrisker i din infrastruktur.  Dessa funktioner i OMS implementeras genom flera lösningar i Log Analytics som analyserar loggdata och konfigurationer från agentsystem för att hjälpa dig att upprätthålla säkerheten i din miljö.

- [Säkerhets- och granskningslösningen](log-analytics-security-audit.md ) samlar in och analyserar säkerhetshändelser på hanterade datorer för att identifiera misstänkt aktivitet.
- [Lösningen för program mot skadlig kod](log-analytics-malware.md ) rapporter om statusen för program för skydd mot skadlig kod på hanterade datorer.  
- Systemuppdateringslösningen utför en analys av säkerhetsuppdateringarna och andra uppdateringar på de hanterade datorerna så att du lätt kan identifiera datorer som kräver korrigering.


## Nästa steg
- Läs mer om [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
- Läs mer om [Azure Automation](../automation/automation-intro.md).
- Läs mer om [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
- Läs mer om [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).



<!--HONumber=Sep16_HO3-->


