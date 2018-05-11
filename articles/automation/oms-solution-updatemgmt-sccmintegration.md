---
title: Rikta uppdateringar med hjälp av SCCM-samlingar i Azure Automation - uppdateringshantering
description: Den här artikeln är avsedd att hjälpa dig att konfigurera System Center Configuration Manager med den här lösningen för att hantera uppdateringar av SCCM-hanterade datorer.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 196021a95b57c3295de79ca13f578bdb70c39df0
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Integrera System Center Configuration Manager med uppdateringshantering

Kunder som har investerat i System Center Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av programuppdateringar som en del av sin cykel för hantering av programuppdatering (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och före mellanlagring distributioner av programuppdateringar i Configuration Manager och få detaljerad statusinformation för slutförda distributioner med hjälp av den [uppdatera hanteringslösning](automation-update-management.md). Om du använder Configuration Manager för rapportering av uppdateringen kompatibilitet men inte för att hantera distributioner med Windows-servrar, kan du fortsätta rapportering i Configuration Manager när säkerhetsuppdateringar hanteras med uppdatera hanteringslösningen.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha den [uppdatering hanteringslösning](automation-update-management.md) lagts till i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av System Center Configuration Manager-miljön måste också rapportera till arbetsytan Log Analytics som även har lösningen för uppdateringshantering aktiverad.
* Den här funktionen är aktiverad i System Center Configuration Manager aktuella versionen 1606 och högre. Om du vill integrera webbplatsen för central administration av Configuration Manager eller en fristående primär webbplats med Log Analytics och importera samlingar läser du [Anslut Konfigurationshanteraren till Log Analytics](../log-analytics/log-analytics-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update om de inte får säkerhetsuppdateringar från Configuration Manager.   

Hur du hanterar klienter som finns i Azure IaaS med den befintliga Configuration Manager-miljön beror främst på anslutningen mellan dina Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla ändringar du kan behöva göra i Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguration

### <a name="manage-software-updates-from-configuration-manager"></a>Hantera programuppdateringar från Configuration Manager 

Utför följande steg om du kommer att fortsätta att hantera distributioner från Configuration Manager. Azure Automation ansluter till Configuration Manager att installera uppdateringar för klientdatorer som är ansluten till logganalys-arbetsytan. Uppdateringsinnehållet är tillgängligt från cachelagringen från klientdatorn som om distributionen hanterades av Configuration Manager.

1. Skapa en programuppdateringsdistribution från den översta nivån i Configuration Manager-hierarkin med metoden som beskrivs i informationen om att [distribuera programuppdateringar](/sccm/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Det här beteendet hanteras av uppdatera hanteringslösningen genom att skapa en schemalagd uppdateringsdistribution i nästa steg.

1. Välj i Azure Automation **uppdateringshantering**. Skapa en ny distribution som beskrivs i följande [skapar en distribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importeras grupper** på den **typen** listrutan för att välja rätt Configuration Manager-samling. Tänk på följande viktiga punkter: en. Om ett underhållsfönster har definierats för den valda enhetssamlingen i Configuration Manager, medlemmar i samlingen respektera den i stället för den **varaktighet** inställningen som definieras i schemalagd distribution.
    b. Medlemmar i målsamlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller OMS-gateway).

När du har slutfört distributionen av uppdateringen via Azure Automation måldatorer som är medlemmar i den valda datorgruppen kommer att installera uppdateringar på den schemalagda tiden från sina lokala klientcachen. Du kan [visa status för uppdateringsdistributionen](automation-tutorial-update-management.md#view-results-of-an-update-deployment) för att övervaka resultatet av distributionen.

### <a name="manage-software-updates-from-azure-automation"></a>Hantera programuppdateringar från Azure Automation

För att hantera uppdateringar för virtuella Windows Server-datorer som är Configuration Manager-klienter måste du konfigurera klientens princip för att avaktivera funktionen för hantering av programuppdatering för alla klienter som hanteras av lösningen. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här principen och hur du konfigurerar den finns i [how to configure client settings in System Center Configuration Manager](/sccm/core/clients/deploy/configure-client-settings) (konfigurera klientinställningar i System Center Configuration Manager).

När du utför den här konfigurationsändringen måste du skapa en ny distribution som beskrivs i följande [skapar en distribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importeras grupper** på den **typen** listrutan och välj lämplig Configuration Manager-samling.

## <a name="next-steps"></a>Nästa steg
