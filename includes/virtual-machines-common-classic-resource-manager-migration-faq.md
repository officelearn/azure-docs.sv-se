---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: 74496cd3d4cd01be326baae870b075eb923983af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581141"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Vanliga frågor och svar om migrering från klassiskt läge till Azure Resource Manager

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Påverkar den här migreringsplanen några befintliga tjänster eller program som körs på virtuella Azure-datorer? 

Nej. De virtuella datorerna (klassiskt läge) är tjänster med fullständigt stöd och allmänt tillgängliga. Du kan fortsätta att använda de här resurserna för att utöka storleken på Microsoft Azure.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Vad händer med mina virtuella datorer om jag inte planerar för migrering inom den närmaste framtiden? 

Vi kommer inte att avveckla de befintliga klassiska API:erna och resursmodellerna. Vi vill att migreringen ska gå lätt och smidigt med tanke på de avancerade funktionerna i Resource Manager-distributionsmodellen. Vi rekommenderar att du läser igenom [några av förbättringarna](../articles/azure-resource-manager/resource-manager-deployment-model.md) som är en del av IaaS i Resource Manager.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Vad innebär den här migreringsplanen för mina befintliga verktyg? 

Det är mycket viktigt att du uppdaterar dina verktyg till Resource Manager-distributionsmodellen.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Hur länge kommer hanteringsplanet vara avstängt? 

Det beror på antalet resurser som ska migreras. För mindre distributioner (ett tiotal virtuella datorer) bör hela migreringen ta högst en timme. Vid stora distributioner (hundratals virtuella datorer) kan migreringen ta några timmar.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Kan jag återställa när mina migreringsresurser har checkats in till Resource Manager? 

Du kan avbryta migreringen så länge resurserna är i förberedelsefasen. Återställning stöds inte när resurserna har migrerats via incheckningsåtgärden.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Kan jag återställa migreringen om incheckningen misslyckas? 

Du kan inte avbryta migreringen om incheckningen misslyckas. Alla migreringsåtgärder, inklusive incheckning, är idempotenta. Vi rekommenderar därför att du gör om åtgärden om en stund. Om felet fortfarande visas kan du skapa ett supportärende eller göra ett foruminlägg med taggen ClassicIaaSMigration på vårt [forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Måste jag köpa en annan ExpressRoute-krets om jag måste använda IaaS med Resource Manager? 

Nej. Vi har nyligen gjort det möjligt att [flytta ExpressRoute-kretsar från klassiskt läge till Resource Manager-distributionsmodellen](../articles/expressroute/expressroute-move.md). Du behöver inte köpa någon ny ExpressRoute-krets om du redan har en.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Vad händer om jag har konfigurerat principer för rollbaserad åtkomstkontroll för mina klassiska IaaS-resurser? 

Under migreringen omformas resurserna från klassiskt läge till Resource Manager-läge. Vi rekommenderar att du planerar de RBAC-principuppdateringar som behövs efter migreringen.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Jag har säkerhetskopierat Mina klassiska virtuella datorer i ett valv. Kan jag migrera mina virtuella datorer från klassiskt läge till Resource Manager-läge och skydda dem i ett Recovery Services-valv?

<a name="vault">När</a> du flyttar en virtuell dator från klassisk till Resource Manager-läge, säkerhetskopieringar före migreringen kommer inte att migrera till ny migrerad Resource Manager-VM. Om du vill behålla dina säkerhetskopior av klassiska virtuella datorer följer du de här stegen före migreringen. 

1. I Recovery Services-valv går du till den **skyddade objekt** fliken och markera den virtuella datorn. 
2. Klicka på Avbryt skyddet. Lämna alternativet *Ta bort associerade säkerhetskopieringsdata* **avmarkerat**.

> [!NOTE]
> Du kommer att debiteras en kostnad för säkerhetskopiering instans tills du behålla data. Säkerhetskopior rensas Kvarhållningsintervall. Senaste säkerhetskopia sparas dock alltid tills du uttryckligen tar bort säkerhetskopierade data. Det är bäst att kontrollera din Kvarhållningsintervall på den virtuella datorn och utlösaren ”ta bort säkerhetskopieringsdata” för det skyddade objektet i valvet när kvarhållningsintervallet är över. 
>
>

Att migrera den virtuella datorn till Resource Manager-läge 

1. Ta bort tillägget för säkerhetskopiering/ögonblicksbild från den virtuella datorn.
2. Migrera den virtuella datorn från klassiskt läge till Resource Manager-läge. Kontrollera att lagrings- och nätverksinformationen för den virtuella datorn också migreras till Resource Manager-läge.

Dessutom, om du vill säkerhetskopiera den migrerade virtuella datorn går du till bladet för hantering av virtuell dator till [Aktivera säkerhetskopiering](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Kan jag verifiera prenumeration eller resurser för att se om de kan migreras? 

Ja. I alternativet för migrering som stöds av plattformen är det första steget vid migreringsförberedelse att verifiera om resurserna kan migreras. Om resurserna inte godkänns för migrering får du meddelanden om orsaken till detta.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Vad händer om jag får ett kvotfel när IaaS-resurserna förbereds för migrering? 

Vi rekommenderar att du avbryter migreringen och loggar en supportbegäran om att öka kvoten i den region där du migrerar de virtuella datorerna. När din begäran om ökad kvot har godkänts kan du fortsätta migreringen igen.

## <a name="how-do-i-report-an-issue"></a>Hur gör jag för att rapportera ett problem? 

Gör ett inlägg med dina problem och frågor om migreringen på vårt [forum för virtuella datorer](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows) med nyckelordet ClassicIaaSMigration. Vi rekommenderar att du lägger upp alla dina frågor på det här forumet. Om du har ett supportavtal är du även välkommen att logga ett supportärende.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Vad gör jag om jag inte gillar namnen på resurserna som plattformen väljer under migreringen? 

Alla resursnamn som du uttryckligen angett i den klassiska distributionsmodellen behålls under migreringen. I vissa fall skapas nya resurser. Exempelvis skapas ett nätverksgränssnitt för varje virtuell dator. För närvarande har vi inte möjlighet att styra över namnen på de nya resurser som skapas under migreringen. Logga dina röster för den här funktionen i [Azures feedbackforum](http://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Kan jag migrera ExpressRoute-kretsar som används över prenumerationer med auktoriseringslänkar? 

ExpressRoute-kretsar som använder auktoriseringslänkar över flera prenumerationer migreras inte automatiskt utan driftavbrott. Det finns information om hur de kan migreras manuellt. Anvisningar och mer information finns i [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-migration-classic-resource-manager.md) (Migrera ExpressRoute-kretsar och tillhörande virtuella nätverk från klassisk till Resource Manager-distributionsmodell).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Jag har fått meddelandet *”Virtuella datorn rapporterar övergripande agentstatus inte redo. Den virtuella datorn kan därför inte migreras. Se till att den Virtuella Datoragenten rapporterar övergripande agentstatus redo ”* eller *” den virtuella datorn innehåller tillägg vars Status inte har rapporteras från den virtuella datorn. Därför kan kan den här virtuella datorn inte migreras ”.*

Det här meddelandet visas när den virtuella datorn saknar utgående anslutning till Internet. Den virtuella datoragenten använder utgående anslutning till Azure Storage-konto för att uppdatera agentstatusen var femte minut.
