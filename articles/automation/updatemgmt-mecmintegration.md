---
title: Använda Azure Automation Uppdateringshantering med Configuration Manager klienter
description: Den här artikeln är avsedd att hjälpa dig att konfigurera Microsoft Endpoint Configuration Manager med den här lösningen för att distribuera program uppdateringar till ConfigMgr-klienter.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2dbc33aa56c7e930596ba6806ba1dd2e128e1c82
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780223"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Distribuera uppdateringar till Microsoft Endpoint Configuration Manager-klienter med Uppdateringshantering

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av program uppdateringar som en del av program uppdaterings hanterings cykeln (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och förinstallera program uppdaterings distributioner i Configuration Manager och få detaljerad status för slutförda uppdaterings distributioner med [uppdateringshantering](automation-update-management.md). Om du använder Configuration Manager för uppdatering av Kompatibilitetsrapport, men inte för att hantera uppdaterings distributioner med dina Windows-servrar, kan du fortsätta att rapportera till Configuration Manager medan säkerhets uppdateringar hanteras med Uppdateringshantering.

## <a name="prerequisites"></a>Krav

* Du måste ha [uppdateringshantering](automation-update-management.md) lagt till i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av din Configuration Manager miljö måste också rapportera till Log Analytics-arbetsytan som också har Uppdateringshantering aktiverat.
* Den här funktionen är aktive rad i Configuration Manager aktuella gren version 1606 och högre. Om du vill integrera Configuration Manager Central administrations plats eller en fristående primär plats med Azure Monitor loggar och importera samlingar, granskar [du anslut Configuration Manager till Azure Monitor loggar](../azure-monitor/platform/collect-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update om de inte får säkerhetsuppdateringar från Configuration Manager.

Hur du hanterar klienter som finns i Azure IaaS med den befintliga Configuration Manager-miljön beror främst på anslutningen mellan dina Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla ändringar du kan behöva göra i Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguration

### <a name="manage-software-updates-from-configuration-manager"></a>Hantera programuppdateringar från Configuration Manager

Utför följande steg om du kommer att fortsätta att hantera distributioner från Configuration Manager. Azure Automation ansluter till Configuration Manager för att tillämpa uppdateringar på klient datorerna som är anslutna till din Log Analytics-arbetsyta. Uppdateringsinnehållet är tillgängligt från cachelagringen från klientdatorn som om distributionen hanterades av Configuration Manager.

1. Skapa en program uppdaterings distribution från platsen på den översta nivån i Configuration Manager hierarkin med hjälp av processen som beskrivs i [distribuera program uppdateringar](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Detta beteende hanteras i Uppdateringshantering genom att en schemalagd uppdaterings distribution skapas i nästa steg.

1. I Azure Automation väljer du **uppdateringshantering**. Skapa en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importerade grupper** i list rutan **typ** för att välja lämplig Configuration Manager samling. Tänk på följande viktiga punkter: a. Om ett underhålls fönster har definierats för den valda Configuration Manager enhets samlingen, följer medlemmarna i samlingen den i stället för **varaktighets** inställningen som definierats i den schemalagda distributionen.
    b. Medlemmar i mål samlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller via Log Analytics Gateway).

När du har slutfört uppdaterings distributionen via Azure Automation kommer de mål datorer som är medlemmar i den valda dator gruppen att installera uppdateringar vid den schemalagda tiden från deras lokala klientcachen. Du kan [visa status för uppdateringsdistributionen](automation-tutorial-update-management.md#view-results-of-an-update-deployment) för att övervaka resultatet av distributionen.

### <a name="manage-software-updates-from-azure-automation"></a>Hantera program uppdateringar från Azure Automation

Om du vill hantera uppdateringar för virtuella Windows Server-datorer som Configuration Manager klienter måste du konfigurera klient principen för att inaktivera funktionen hantering av programuppdateringar för alla klienter som hanteras av Uppdateringshantering. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här princip inställningen och hur du konfigurerar den finns [i Konfigurera klient inställningar i Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

När du har genomfört den här konfigurations ändringen skapar du en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välja **importerade grupper** i list rutan **typ** för att välja lämplig Configuration Manager samling.

## <a name="next-steps"></a>Nästa steg

Skapa en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment).