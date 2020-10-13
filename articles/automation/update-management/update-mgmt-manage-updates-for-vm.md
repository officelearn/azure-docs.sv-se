---
title: Hantera uppdateringar och korrigeringar för dina virtuella datorer i Azure Automation
description: Den här artikeln beskriver hur du använder Uppdateringshantering för att hantera uppdateringar och korrigeringar för dina virtuella Azure-och icke-Azure-datorer.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450533"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Hantera uppdateringar och korrigeringar för dina virtuella datorer

Program uppdateringar i Azure Automation Uppdateringshantering innehåller en uppsättning verktyg och resurser som kan hjälpa dig att hantera den komplexa uppgiften att spåra och tillämpa program uppdateringar på datorer i Azure och hybrid moln. En effektiv process för hantering av program uppdateringar är nödvändig för att upprätthålla drifts effektiviteten, lösa säkerhets problem och minska riskerna med ökad cyberhot säkerhetshot. Men på grund av teknikens föränderliga natur och den kontinuerliga framväxten av nya säkerhetshot, kräver effektiv hantering av programuppdateringar konsekvent, kontinuerlig uppmärksamhet.

> [!NOTE]
> Uppdateringshantering stöder distribution av uppdateringar från första part och för hands hämtning av dem. Detta stöd kräver ändringar på de system som uppdateras. Mer information om hur du konfigurerar inställningarna på dina system finns i [konfigurera Windows Update inställningar för Azure Automation uppdateringshantering](update-mgmt-configure-wuagent.md) .

Innan du försöker hantera uppdateringar för dina virtuella datorer bör du se till att du har aktiverat Uppdateringshantering på dem med någon av följande metoder:

* [Aktivera Uppdateringshantering från ett Automation-konto](update-mgmt-enable-automation-account.md)
* [Aktivera Uppdateringshantering genom att bläddra i Azure Portal](update-mgmt-enable-portal.md)
* [Aktivera Uppdateringshantering från en runbook](update-mgmt-enable-runbook.md)
* [Aktivera Uppdateringshantering från en virtuell Azure-dator](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Begränsa omfånget för distributionen

Uppdateringshantering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska ta emot uppdateringar. Mer information finns i [begränsa uppdateringshantering distributions omfång](update-mgmt-scope-configuration.md).

## <a name="compliance-assessment"></a>Kompatibilitetskontroll

Innan du distribuerar program uppdateringar till datorerna granskar du resultatet av uppdateringen av utvärderings kraven för aktiverade datorer. För varje program uppdatering registreras dess kompatibilitetstillstånd och när utvärderingen är klar samlas den in och vidarebefordras i bulk till Azure Monitor loggar.

På en Windows-dator körs kompatibilitetskontroll var 12: e timme som standard. Förutom den schemalagda genomsökningen initieras genomsökningen av kompatibiliteten inom 15 minuter från den Log Analytics agenten för Windows som startas om, innan uppdateringen installeras, och efter installationen av uppdateringen. Det är också viktigt att granska våra rekommendationer om hur du [konfigurerar den Windows Update klienten](update-mgmt-configure-wuagent.md) med uppdateringshantering för att undvika eventuella problem som gör att den inte hanteras korrekt.

För en Linux-dator utförs genomsökningen varje timme som standard. Om Log Analytics-agenten för Linux startas om initieras en kompatibilitetskontroll inom 15 minuter.

Resultatet av efterlevnaden presenteras i Uppdateringshantering för varje dator som utvärderas. För en ny dator som är aktive rad för hantering kan det ta upp till 30 minuter för instrument panelen att Visa uppdaterade data från den.

Granska [övervaknings program uppdateringar](update-mgmt-view-update-assessments.md) och lär dig hur du kan visa resultatet av efterlevnaden.

## <a name="deploy-updates"></a>Distribuera uppdateringar

När du har granskat resultatet av efterlevnaden är distributions fasen för program uppdatering processen för att distribuera program uppdateringar. Om du vill installera uppdateringar schemalägger du en distribution som överensstämmer med ditt versions schema och service fönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Läs [distribuera program uppdateringar](update-mgmt-deploy-updates.md) för att lära dig hur du schemalägger en uppdaterings distribution.

## <a name="review-update-deployments"></a>Granska uppdaterings distributioner

När distributionen är klar kan du granska processen för att avgöra om distributions distributionen efter dator eller mål grupp lyckades. Se [Granska distributions status](update-mgmt-deploy-updates.md#check-deployment-status) för att lära dig hur du kan övervaka distributions statusen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar aviseringar för att meddela dig om uppdaterings distributions resultat finns i [skapa aviseringar för uppdateringshantering](update-mgmt-configure-alerts.md).

* Du kan [fråga Azure Monitor loggar](update-mgmt-query-logs.md) för att analysera uppdaterings utvärderingar, distributioner och andra relaterade hanterings uppgifter. Den innehåller fördefinierade frågor som hjälper dig att komma igång.