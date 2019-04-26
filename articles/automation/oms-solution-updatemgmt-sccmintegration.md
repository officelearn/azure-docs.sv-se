---
title: Rikta uppdateringar med hjälp av SCCM-samlingar i Azure Automation - hantering av uppdateringar
description: Den här artikeln är avsedd att hjälpa dig att konfigurera System Center Configuration Manager med den här lösningen för att hantera uppdateringar av SCCM-hanterade datorer.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddc055be45eae923be31d7d11621c9427660bf74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499877"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integrera System Center Configuration Manager med uppdateringshantering

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av programuppdateringar som en del av sin cykel för hantering av programuppdatering (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och förinstallera programuppdateringsdistributioner i Configuration Manager och få detaljerad status för slutförda uppdateringsdistributioner med den [lösningen för uppdateringshantering](automation-update-management.md). Om du använder Configuration Manager för uppdatering av efterlevnadsrapportering men inte för att hantera uppdateringsdistributioner med dina Windows-servrar kan fortsätta du att rapportera till Configuration Manager medan säkerhetsuppdateringar hanteras med lösningen för uppdateringshantering.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha den [uppdateringshanteringslösningen](automation-update-management.md) lagts till i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av System Center Configuration Manager-miljön måste också rapportera till Log Analytics-arbetsytan som även har lösningen för uppdateringshantering aktiverad.
* Den här funktionen är aktiverad i System Center Configuration Manager current branch-versionen 1606 och högre. Om du vill integrera den centrala administrationswebbplatsen för Configuration Manager eller en fristående primär plats med Azure Monitor-loggar och importera samlingar, granska [Anslut Konfigurationshanteraren till Azure Monitor loggar](../azure-monitor/platform/collect-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update om de inte får säkerhetsuppdateringar från Configuration Manager.   

Hur du hanterar klienter som finns i Azure IaaS med den befintliga Configuration Manager-miljön beror främst på anslutningen mellan dina Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla ändringar du kan behöva göra i Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguration

### <a name="manage-software-updates-from-configuration-manager"></a>Hantera programuppdateringar från Configuration Manager 

Utför följande steg om du kommer att fortsätta att hantera distributioner från Configuration Manager. Azure Automation ansluter till Configuration Manager att tillämpa uppdateringar för klientdatorer som är anslutna till Log Analytics-arbetsytan. Uppdateringsinnehållet är tillgängligt från cachelagringen från klientdatorn som om distributionen hanterades av Configuration Manager.

1. Skapa en programuppdateringsdistribution från den översta nivån i Configuration Manager-hierarkin med metoden som beskrivs i informationen om att [distribuera programuppdateringar](/sccm/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Det här beteendet hanteras av lösningen för uppdateringshantering genom att skapa en schemalagd uppdateringsdistribution i nästa steg.

1. I Azure Automation, väljer **uppdateringshantering**. Skapa en ny distribution genom att följa stegen som beskrivs i [skapa en Uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importerade grupper** på den **typ** listrutan att välja rätt Configuration Manager-samling. Tänk på följande viktiga punkter: en. Om ett underhållsfönster har definierats på den valda enhetssamlingen i Configuration Manager kan medlemmar i samlingen respekterar det i stället för den **varaktighet** angiven i den schemalagda distributionen.
    b. Medlemmar i målsamlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller Log Analytics-gateway).

När du har slutfört distributionen av uppdateringen via Azure Automation, installerar de måldatorer som är medlemmar i den valda datorgruppen uppdateringar vid den schemalagda tiden från sin lokala klientcache. Du kan [visa status för uppdateringsdistributionen](automation-tutorial-update-management.md#view-results-of-an-update-deployment) för att övervaka resultatet av distributionen.

### <a name="manage-software-updates-from-azure-automation"></a>Hantera programuppdateringar från Azure Automation

För att hantera uppdateringar för virtuella Windows Server-datorer som är Configuration Manager-klienter måste du konfigurera klientens princip för att avaktivera funktionen för hantering av programuppdatering för alla klienter som hanteras av lösningen. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här principen och hur du konfigurerar den finns i [how to configure client settings in System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings) (konfigurera klientinställningar i System Center Configuration Manager).

När du har utfört konfigurationsändringen skapar du en ny distribution genom att följa stegen som beskrivs i [skapa en Uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importerade grupper** på den **typen** listrutan att välja lämplig Configuration Manager-samling.

## <a name="next-steps"></a>Nästa steg

