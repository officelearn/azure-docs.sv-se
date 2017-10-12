---
title: Arkitekturen i Operations Management Suite (OMS) | Microsoft Docs
description: "Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala och molnbaserade infrastruktur.  I den här artikeln identifierar vi de olika tjänster som ingår i OMS. Artikeln innehåller också länkar till mer detaljerat innehåll om tjänsterna."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="oms-architecture"></a>OMS-arkitekturen
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) är en samling molnbaserade tjänster för hantering av dina lokala och molnbaserade miljöer.  I den här artikeln beskrivs de olika lokala och molnbaserade komponenterna i OMS och deras övergripande molntjänstarkitektur.  Mer information finns i dokumentationen för respektive tjänst.

## <a name="log-analytics"></a>Log Analytics
Alla data som samlas in av [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) lagras i OMS-databasen som ligger i Azure.  Anslutna källor genererar data som samlas in till OMS-databasen.  Det finns för närvarande tre typer av anslutna datakällor som stöds.

* En agent som installeras på en [Windows](../log-analytics/log-analytics-windows-agents.md)- eller [Linux](../log-analytics/log-analytics-linux-agents.md)-dator som är ansluten direkt till OMS.
* En SCOM-hanteringsgrupp (System Center Operations Manager) [som ansluts till Log Analytics](../log-analytics/log-analytics-om-agents.md) .  SCOM-agenter fortsätter att kommunicera med hanteringsservrar som vidarebefordrar händelse- och prestandadata till Log Analytics.
* Ett [Azure-lagringskontot](../log-analytics/log-analytics-azure-storage.md) som samlar in [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)-data från en arbetsroll, webbroll eller virtuell dator i Azure.

Datakällor definierar vilka data som Log Analytics samlar in från anslutna källor, inklusive händelseloggar och prestandaräknare.  Lösningar utökar funktionerna i OMS och kan enkelt läggas till på din arbetsyta från [OMS-lösningsgalleriet](../log-analytics/log-analytics-add-solutions.md).  Vissa lösningar kan kräva en direktanslutning till Log Analytics från SCOM-agenter medan andra kan kräva att en ytterligare agent installeras.

Log Analytics har en webbaserad portal som du kan använda för att hantera OMS-resurser, lägga till och konfigurera OMS-lösningar och visa och analysera data i OMS-databasen.

![Den övergripande Log Analytics-arkitekturen](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
[Azure Automation-runbooks](http://azure.microsoft.com/documentation/services/automation) körs i Azure-molnet och kan komma åt resurser som finns i Azure, i andra molntjänster eller som kan nås från det offentliga Internet.  Du kan också ange lokala datorer i ditt lokala datacenter med hjälp av [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) så att runbooks kan komma åt lokala resurser.

[DSC-konfigurationer](../automation/automation-dsc-overview.md) som lagras i Azure Automation kan tillämpas direkt på virtuella datorer i Azure.  Andra fysiska och virtuella datorer kan begära konfigurationer från Azure Automation DSC-hämtningsservern.

Azure Automation har en OMS-lösning som visar statistik samt länkar till Azure Portal där du kan utföra önskade åtgärder.

![Den övergripande Azure Automation-arkitekturen](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
Skyddade data i [Azure Backup](http://azure.microsoft.com/documentation/services/backup) lagras i ett säkerhetskopieringsvalv som finns i en viss geografisk region.  Data replikeras inom samma region och kan också, beroende på typen av valv, replikeras till en annan region för ytterligare redundans.

Azure Backup har tre grundläggande scenarier.

* Windows-dator med Azure Backup-agent.  I det här scenariot kan du säkerhetskopiera filer och mappar från valfri Windows-server eller Windows-klient direkt till ditt Azure-säkerhetskopieringsvalv.  
* System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server. I det här scenariot kan du använda DPM eller Microsoft Azure Backup Server för att säkerhetskopiera filer och mappar samt programarbetsbelastningar som SQL och SharePoint till lokal lagring och sedan replikera till ditt Azure-säkerhetskopieringsvalv.
* Azure Virtual Machine-tillägg.  I det här scenariot kan du säkerhetskopiera virtuella Azure-datorer till ditt Azure-säkerhetskopieringsvalv.

Azure Backup har en OMS-lösning som visar statistik samt länkar till Azure Portal där du kan utföra önskade åtgärder.

![Den övergripande Azure Backup-arkitekturen](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) samordnar replikering, redundansväxling och återställning av virtuella datorer och fysiska servrar. Replikeringsdata utväxlas mellan Hyper-V-värdar, VMware-hypervisorer och fysiska servrar på primära och sekundära datacenter eller mellan datacentret och Azure Storage.  Site Recovery lagrar metadata i valv som finns i en viss geografisk Azure-region. Inga replikerade data lagras av Site Recovery-tjänsten.

Azure Site Recovery har tre grundläggande replikeringsscenarier.

**Replikering av virtuella Hyper-V-datorer**

* Om Hyper-V-datorer hanteras i VMM-moln kan du replikera till ett sekundärt datacenter eller till Azure Storage.  Replikeringen till Azure sker via en säker Internetanslutning.  Replikeringen till ett sekundärt datacenter sker via det lokala nätverket.
* Om virtuella Hyper-V-datorer inte hanteras av VMM kan du bara replikera till Azure Storage.  Replikeringen till Azure sker via en säker Internetanslutning.

**Replikering av virtuella VMWare-datorer**

* Du kan replikera virtuella VMware-datorer till ett sekundärt datacenter som kör VMware eller till Azure Storage.  Replikeringen till Azure kan ske via en VPN för plats till plats eller Azure ExpressRoute eller via en säker Internetanslutning. Replikeringen till ett sekundärt datacenter sker via InMage Scout-datakanalen.

**Replikering av fysiska Windows- och Linux-servrar** 

* Du kan replikera fysiska servrar till ett sekundärt datacenter eller till Azure Storage. Replikeringen till Azure kan ske via en VPN för plats till plats eller Azure ExpressRoute eller via en säker Internetanslutning. Replikeringen till ett sekundärt datacenter sker via InMage Scout-datakanalen.  Azure Site Recovery har en OMS-lösning som visar en del statistik, men du måste använda Azure Portal för att utföra åtgärder.

![Den övergripande Azure Site Recovery-arkitekturen](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Läs mer om [Azure Automation](https://azure.microsoft.com/documentation/services/automation).
* Läs mer om [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Läs mer om [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

