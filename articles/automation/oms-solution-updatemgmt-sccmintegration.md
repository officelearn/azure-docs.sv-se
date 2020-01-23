---
title: Använda Azure Uppdateringshantering med Configuration Manager-klienter
description: Den här artikeln är avsedd att hjälpa dig att konfigurera Microsoft Endpoint Configuration Manager med den här lösningen för att distribuera program uppdateringar till ConfigMgr-klienter.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513141"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Distribuera uppdateringar till Microsoft Endpoint Configuration Manager-klienter med Uppdateringshantering

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av program uppdateringar som en del av program uppdaterings hanterings cykeln (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och förinstallera programuppdateringsdistributioner i Configuration Manager och få detaljerad status för slutförda uppdateringsdistributioner med den [lösningen för uppdateringshantering](automation-update-management.md). Om du använder Configuration Manager för uppdatering av Kompatibilitetsrapport, men inte för att hantera uppdaterings distributioner med dina Windows-servrar, kan du fortsätta att rapportera till Configuration Manager medan säkerhets uppdateringar hanteras med Uppdateringshantering-lösningen.

## <a name="prerequisites"></a>Krav

* Du måste ha den [uppdateringshanteringslösningen](automation-update-management.md) lagts till i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av din Configuration Manager miljö behöver också rapportera till arbets ytan Log Analytics som också har Uppdateringshanterings lösningen aktive rad.
* Den här funktionen är aktive rad i Configuration Manager aktuella gren version 1606 och högre. Om du vill integrera Configuration Manager Central administrations plats eller en fristående primär plats med Azure Monitor loggar och importera samlingar, granskar [du anslut Configuration Manager till Azure Monitor loggar](../azure-monitor/platform/collect-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update om de inte får säkerhetsuppdateringar från Configuration Manager.   

Hur du hanterar klienter som finns i Azure IaaS med den befintliga Configuration Manager-miljön beror främst på anslutningen mellan dina Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla ändringar du kan behöva göra i Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguration

### <a name="manage-software-updates-from-configuration-manager"></a>Hantera programuppdateringar från Configuration Manager 

Utför följande steg om du kommer att fortsätta att hantera distributioner från Configuration Manager. Azure Automation ansluter till Configuration Manager att tillämpa uppdateringar för klientdatorer som är anslutna till Log Analytics-arbetsytan. Uppdateringsinnehållet är tillgängligt från cachelagringen från klientdatorn som om distributionen hanterades av Configuration Manager.

1. Skapa en program uppdaterings distribution från platsen på den översta nivån i Configuration Manager hierarkin med hjälp av processen som beskrivs i [distribuera program uppdateringar](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Det här beteendet hanteras av lösningen för uppdateringshantering genom att skapa en schemalagd uppdateringsdistribution i nästa steg.

1. I Azure Automation, väljer **uppdateringshantering**. Skapa en ny distribution genom att följa stegen som beskrivs i [skapa en Uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importerade grupper** på den **typ** listrutan att välja rätt Configuration Manager-samling. Tänk på följande viktiga punkter: en. Om ett underhållsfönster har definierats på den valda enhetssamlingen i Configuration Manager kan medlemmar i samlingen respekterar det i stället för den **varaktighet** angiven i den schemalagda distributionen.
    b. Medlemmar i målsamlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller Log Analytics-gateway).

När du har slutfört distributionen av uppdateringen via Azure Automation, installerar de måldatorer som är medlemmar i den valda datorgruppen uppdateringar vid den schemalagda tiden från sin lokala klientcache. Du kan [visa status för uppdateringsdistributionen](automation-tutorial-update-management.md#view-results-of-an-update-deployment) för att övervaka resultatet av distributionen.

### <a name="manage-software-updates-from-azure-automation"></a>Hantera programuppdateringar från Azure Automation

För att hantera uppdateringar för virtuella Windows Server-datorer som är Configuration Manager-klienter måste du konfigurera klientens princip för att avaktivera funktionen för hantering av programuppdatering för alla klienter som hanteras av lösningen. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här princip inställningen och hur du konfigurerar den finns [i Konfigurera klient inställningar i Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

När du har utfört konfigurationsändringen skapar du en ny distribution genom att följa stegen som beskrivs i [skapa en Uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importerade grupper** på den **typen** listrutan att välja lämplig Configuration Manager-samling.

## <a name="next-steps"></a>Nästa steg

