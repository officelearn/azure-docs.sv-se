---
title: "Rikta uppdateringar med hjälp av SCCM-samlingar i OMS-uppdateringshantering | Microsoft Docs"
description: "Den här artikeln är avsedd att hjälpa dig att konfigurera System Center Configuration Manager med den här lösningen för att hantera uppdateringar av SCCM-hanterade datorer."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 04540524f83e367f92912171ddc55b6e6f82f80e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management"></a>Integrera System Center Configuration Manager med OMS-uppdateringshantering

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av programuppdateringar som en del av sin cykel för hantering av programuppdatering (SUM).  

Utifrån den befintliga integrering du idag har mellan OMS och Configuration Manager kan du rapportera och uppdatera hanterade Windows-servrar genom att skapa och förinstallera programuppdateringsdistributioner i Configuration Manager, och du får detaljerad status för slutförda uppdateringsdistributioner med [lösningen för uppdateringshantering](../operations-management-suite/oms-solution-update-management.md). Om du använder Configuration Manager för uppdatering av efterlevnadsrapportering men inte för att hantera uppdateringsdistributioner med dina Windows-servrar kan du fortsätta att rapportera till Configuration Manager, och säkerhetsuppdateringar hanteras med OMS-lösningen för uppdateringshantering.

## <a name="prerequisites"></a>Krav

* Du måste ha [lösningen för uppdateringshantering](../operations-management-suite/oms-solution-update-management.md) i din Log Analytics-arbetsyta och sammanlänkad med ditt Automation-konto i samma resursgrupp och region.   
* Windows-servrar som för närvarande hanteras av System Center Configuration Manager-miljön måste också rapportera till arbetsytan Log Analytics som även har lösningen för uppdateringshantering aktiverad.  
* Funktionen har stöd för den aktuella System Center Configuration Manager-versionen 1606 och högre.  Om du vill integrera webbplatsen för central administration av Configuration Manager eller en fristående primär webbplats med Log Analytics och importera samlingar läser du [Anslut Konfigurationshanteraren till Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update om de inte får säkerhetsuppdateringar från Configuration Manager.   

Hur du hanterar klienter som finns i Azure IaaS med den befintliga Configuration Manager-miljön beror främst på anslutningen mellan dina Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla ändringar du kan behöva göra i Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna.  För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking).    

## <a name="configuration"></a>Konfiguration

### <a name="manage-software-updates-from-configuration-manager"></a>Hantera programuppdateringar från Configuration Manager 

Utför följande steg om du kommer att fortsätta att hantera distributioner från Configuration Manager.  OMS ansluts till Configuration Manager för att tillämpa uppdateringar för klientdatorer som är anslutna till din Log Analytics-arbetsyta. Uppdateringsinnehållet är tillgängligt från cachelagringen från klientdatorn som om distributionen hanterades av Configuration Manager.  

1. Skapa en programuppdateringsdistribution från den översta nivån i Configuration Manager-hierarkin med metoden som beskrivs i informationen om att [distribuera programuppdateringar](https://docs.microsoft.com/en-us/sccm/sum/deploy-use/deploy-software-updates).  Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Beteendet hanteras av OMS-lösningen för uppdateringshantering som skapar en schemalagd uppdateringsdistribution i nästa steg.  

1. I OMS-portalen öppnar du instrumentpanelen Uppdateringshantering.  Skapa en ny distribution genom att följa stegen i [Skapa en uppdateringsdistribution](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) och välja lämplig Configuration Manager-samling som representeras som en OMS-datorgrupp från listrutan.  Tänk på följande viktiga punkter:
    1. Om ett underhållsfönster har definierats för den valda enhetssamlingen i Configuration Manager kan medlemmar av samlingen kontrollera den istället för inställningen **Varaktighet** som är angiven i den schemalagda distributionen i OMS.
    1. Medlemmar i målsamlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller OMS-gateway).  

När distributionen av uppdateringen är slutförd med OMS-lösningen installerar de måldatorer som är medlemmar av den valda datorgruppen uppdateringar vid den schemalagda tiden från sin lokala klientcache.  Du kan [visa status för uppdateringsdistributionen](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments) för att övervaka resultatet av distributionen.  


### <a name="manage-software-updates-from-oms"></a>Hantera programuppdateringar från OMS

För att hantera uppdateringar för virtuella Windows Server-datorer som är Configuration Manager-klienter måste du konfigurera klientens princip för att avaktivera funktionen för hantering av programuppdatering för alla klienter som hanteras av lösningen.  Som standard riktar sig klientinställningarna till alla enheter i hierarkin.  Mer information om den här principen och hur du konfigurerar den finns i [how to configure client settings in System Center Configuration Manager](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings) (konfigurera klientinställningar i System Center Configuration Manager).  

När du har utfört konfigurationsändringen skapar du en ny distribution genom att följa stegen i [Skapa en uppdateringsdistribution](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) och välja lämplig Configuration Manager-samling som representeras som en OMS-datorgrupp från listrutan. 

