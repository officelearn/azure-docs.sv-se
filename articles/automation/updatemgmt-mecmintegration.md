---
title: Integrera Azure Automation Uppdateringshantering med Windows-slutpunkt Configuration Manager
description: Den här artikeln beskriver hur du konfigurerar Microsoft Endpoint Configuration Manager med Uppdateringshantering för att distribuera program uppdateringar till Manager-klienter.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 183189253d11638751e1f8283b202f122131b005
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836319"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Integrera Uppdateringshantering med Windows-slutpunkt Configuration Manager

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av program uppdateringar som en del av program uppdaterings hanterings cykeln (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och förinstallera distributioner av program uppdateringar i Windows-slutpunkt Configuration Manager och få detaljerad status för slutförda uppdaterings distributioner med [uppdateringshantering](automation-update-management.md). Om du använder Windows-slutpunkt Configuration Manager för uppdatering av Kompatibilitetsrapport, men inte för att hantera uppdaterings distributioner med dina Windows-servrar, kan du fortsätta att rapportera till Konfigurations hanteraren medan säkerhets uppdateringar hanteras med Azure Automation Uppdateringshantering.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha [Azure Automation uppdateringshantering](automation-update-management.md) lagts till i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av din Windows-slutpunkt Configuration Managers miljö måste också rapportera till Log Analytics-arbetsytan som också har Uppdateringshantering aktiverat.
* Den här funktionen är aktive rad i Windows-slutpunkt Configuration Manager aktuell gren version 1606 och högre. Om du vill integrera din Windows-slutpunkt Configuration Manager Central administrations plats eller en fristående primär plats med Azure Monitor loggar och importera samlingar, granskar [du anslut Configuration Manager till Azure Monitor loggar](../azure-monitor/platform/collect-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en Windows Server Update Services-server (WSUS) eller ha åtkomst till Microsoft Update om de inte tar emot säkerhets uppdateringar från Windows-slutpunkten Configuration Manager.

Hur du hanterar klienter som finns i Azure-IaaS med din befintliga Windows-slutpunkt Configuration Managers miljö beror huvudsakligen på vilken anslutning du har mellan Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla design ändringar som du kan behöva göra i din Windows-slutpunkt Configuration Manager-infrastruktur och relaterad kostnad för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Hantera program uppdateringar från Windows-slutpunkt Configuration Manager

Utför följande steg om du kommer att fortsätta hantera uppdaterings distributioner från Windows-slutpunkt Configuration Manager. Azure Automation ansluter till Windows-slutpunkten Configuration Manager för att tillämpa uppdateringar på klient datorerna som är anslutna till din Log Analytics-arbetsyta. Uppdaterings innehåll är tillgängligt från klient datorns cache som om distributionen hanterades av Windows-slutpunkten Configuration Manager.

1. Skapa en program uppdaterings distribution från platsen på den översta nivån i Configuration Manager hierarki för Windows-slutpunkt med processen som beskrivs i [distribuera program uppdateringar](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Detta beteende hanteras i Uppdateringshantering genom att en schemalagd uppdaterings distribution skapas i nästa steg.

1. I Azure Automation väljer du **uppdateringshantering**. Skapa en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välj **importerade grupper** i list rutan **typ** för att välja lämplig Windows-slutpunkt Configuration Manager samling. Tänk på följande viktiga punkter: a. Om ett underhålls fönster har definierats på den valda Windows-slutpunkten Configuration Manager enhets samling, följer medlemmarna i samlingen den i stället för **varaktighets** inställningen som definierats i den schemalagda distributionen.
    b. Medlemmar i mål samlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller via Log Analytics Gateway).

När du har slutfört uppdaterings distributionen via Azure Automation kommer de mål datorer som är medlemmar i den valda dator gruppen att installera uppdateringar vid den schemalagda tiden från deras lokala klientcachen. Du kan [visa status för uppdateringsdistributionen](automation-tutorial-update-management.md#view-results-of-an-update-deployment) för att övervaka resultatet av distributionen.

## <a name="manage-software-updates-from-azure-automation"></a>Hantera program uppdateringar från Azure Automation

Om du vill hantera uppdateringar för virtuella Windows Server-datorer som är Windows-slutpunkt Configuration Manager klienter, måste du konfigurera klient principen för att inaktivera funktionen hantering av programuppdateringar för alla klienter som hanteras av Uppdateringshantering. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här princip inställningen och hur du konfigurerar den finns [i Konfigurera klient inställningar i Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

När du har genomfört den här konfigurations ändringen skapar du en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment) och välja **importerade grupper** i list rutan **typ** för att välja lämplig Windows-slutpunkt Configuration Manager samling.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar ett integrations schema finns i [Schemalägga en uppdaterings distribution](automation-tutorial-update-management.md#schedule-an-update-deployment).