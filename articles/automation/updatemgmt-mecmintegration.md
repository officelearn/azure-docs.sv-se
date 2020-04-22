---
title: Använda Azure Automation Update Management med Configuration Manager-klienter
description: Den här artikeln är avsedd att hjälpa dig att konfigurera Microsoft Endpoint Configuration Manager med den här lösningen för att distribuera programuppdateringar till ConfigMgr-klienter.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678737"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Distribuera uppdateringar till Microsoft Endpoint Configuration Manager-klienter med Uppdateringshantering

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter förlitar sig också på sin styrka och mognad i hanteringen av programuppdateringar som en del av sin cykel för hantering av programuppdateringar (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och förproduktion av programuppdateringsdistributioner i Configuration Manager och få detaljerad status för slutförda uppdateringsdistributioner med [uppdateringshantering](automation-update-management.md). Om du använder Configuration Manager för uppdateringskompatibilitetsrapportering men inte för att hantera uppdateringsdistributioner med Dina Windows-servrar kan du fortsätta att rapportera till Configuration Manager medan säkerhetsuppdateringar hanteras med lösning för uppdateringshantering.

## <a name="prerequisites"></a>Krav

* Du måste ha [lösningen Update Management](automation-update-management.md) tillagd i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av Configuration Manager-miljön måste också rapportera till log analytics-arbetsytan som också har lösningen Update Management aktiverad.
* Den här funktionen är aktiverad i Configuration Manager nuvarande gren version 1606 och högre. Om du vill integrera den centrala administrationsplatsen för Configuration Manager eller en fristående primär plats med Azure Monitor-loggar och importsamlingar läser du [Loggfiler](../azure-monitor/platform/collect-sccm.md)för Connect Configuration Manager till Azure Monitor .  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update om de inte får säkerhetsuppdateringar från Configuration Manager.   

Hur du hanterar klienter som finns i Azure IaaS med den befintliga Configuration Manager-miljön beror främst på anslutningen mellan dina Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla ändringar du kan behöva göra i Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguration

### <a name="manage-software-updates-from-configuration-manager"></a>Hantera programuppdateringar från Configuration Manager 

Utför följande steg om du kommer att fortsätta att hantera distributioner från Configuration Manager. Azure Automation ansluter till Configuration Manager för att installera uppdateringar på klientdatorer som är anslutna till logganalysarbetsytan. Uppdateringsinnehållet är tillgängligt från cachelagringen från klientdatorn som om distributionen hanterades av Configuration Manager.

1. Skapa en programuppdateringsdistribution från den översta platsen i Configuration Manager-hierarkin med hjälp av den process som beskrivs i [Distribuera programuppdateringar](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Det här beteendet hanteras av lösningslösningen för uppdateringshantering genom att skapa en schemalagd uppdateringsdistribution i nästa steg.

1. I Azure Automation väljer du **Uppdateringshantering**. Skapa en ny distribution enligt stegen som beskrivs i [Skapa en uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **Importerade grupper** i listrutan **Typ** för att välja lämplig Configuration Manager-samling. Tänk på följande viktiga punkter: a. Om ett underhållsfönster har definierats i den valda Configuration Manager-enhetssamlingen, respekterar medlemmar i samlingen det i stället för inställningen **Varaktighet** som definierats i den schemalagda distributionen.
    b. Medlemmar i målsamlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller via Log Analytics-gatewayen).

När du har slutfört uppdateringsdistributionen via Azure Automation installerar måldatorerna som är medlemmar i den valda datorgruppen uppdateringar vid den schemalagda tiden från deras lokala klientcache. Du kan [visa status för uppdateringsdistributionen](automation-tutorial-update-management.md#view-results-of-an-update-deployment) för att övervaka resultatet av distributionen.

### <a name="manage-software-updates-from-azure-automation"></a>Hantera programuppdateringar från Azure Automation

För att hantera uppdateringar för virtuella Windows Server-datorer som är Configuration Manager-klienter måste du konfigurera klientens princip för att avaktivera funktionen för hantering av programuppdatering för alla klienter som hanteras av lösningen. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här principinställningen och hur du konfigurerar den finns i [Så här konfigurerar du klientinställningar i Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

När du har utfört den här konfigurationsändringen skapar du en ny distribution enligt stegen som beskrivs i [Skapa en uppdateringsdistribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och väljer **Importerade grupper** i listrutan **Typ** för att välja lämplig Configuration Manager-samling.

## <a name="next-steps"></a>Nästa steg

