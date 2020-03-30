---
title: ta med fil
description: ta med fil
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 57469bef7014010164234638f3d059ac96b125cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383944"
---
## <a name="what-is-the-time-required-for-migration"></a>Hur länge krävs för migrering?

Planering och genomförande av migrering beror i hög grad på arkitekturens komplexitet och kan ta några månader.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Vad är definitionen av en ny kund på IaaS virtuella datorer (klassisk)?

Kunder som inte hade IaaS virtuella datorer (klassisk) i sina prenumerationer i februari månad 2020 (en månad innan utfasningen startade) betraktas som nya kunder. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Vad är definitionen av en befintlig kund på IaaS Virtual Machines (klassisk)?

Kund som hade aktiva eller stoppade men tilldelat IaaS virtuella datorer (Classic) i sina prenumerationer i februari månad 2020 betraktas som en befintlig kund. Endast dessa kunder får fram till den 1 mars 2023 för att migrera sina virtuella datorer från Azure Service Manager till Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Varför får jag ett felmeddelande om "NewClassicVMCreationNotAllowedForSubscription"?

Som en del av pensionsprocessen är IaaS VM (klassisk) inte längre tillgängligt för nya kunder. Vi identifierade dig som nya kunder och därför var din verksamhet inte auktoriserad. Vi rekommenderar starkt att du använder [virtuella Azure-datorer med ARM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Om du inte kan använda virtuella Azure-datorer med ARM kontaktar du supporten för vitlistning av prenumerationer.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Påverkar den här migreringsplanen några befintliga tjänster eller program som körs på virtuella Azure-datorer? 

Inte förrän den 1 mars 2023 för virtuella IaaS-datorer (klassisk). IaaS virtuella datorer (klassiska) stöds fullt ut tjänster i allmän tillgänglighet. Du kan fortsätta att använda de här resurserna för att utöka storleken på Microsoft Azure. Den 1 mars 2023 kommer dessa virtuella datorer att vara helt pensionerade och alla aktiva eller tilldelade virtuella datorer stoppas & deallocated. Det kommer inte att påverka andra klassiska resurser som Cloud Services (Classic), Storage Accounts (Classic), etc.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Vad händer med mina virtuella datorer om jag inte planerar för migrering inom den närmaste framtiden? 

Den 1 mars 2023 kommer IaaS virtuella datorer (Classic) att vara helt pensionerade och alla aktiva eller tilldelade virtuella datorer stoppas & deallocated. För att förhindra påverkan på verksamheten rekommenderar vi starkt att vi börjar planera din migrering idag och slutföra den före den 1 mars 2023. Vi inaktuella inte befintliga klassiska API:er, molntjänster och resursmodell. Vi vill att migreringen ska gå lätt och smidigt med tanke på de avancerade funktionerna i Resource Manager-distributionsmodellen. Vi rekommenderar att du börjar planera att migrera dessa resurser till Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Vad innebär den här migreringsplanen för mina befintliga verktyg? 

Det är mycket viktigt att du uppdaterar dina verktyg till Resource Manager-distributionsmodellen.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Hur länge kommer hanteringsplanet vara avstängt? 

Det beror på antalet resurser som ska migreras. För mindre distributioner (ett tiotal virtuella datorer) bör hela migreringen ta högst en timme. Vid stora distributioner (hundratals virtuella datorer) kan migreringen ta några timmar.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Kan jag återställa när mina migreringsresurser har checkats in till Resource Manager? 

Du kan avbryta migreringen så länge resurserna är i förberedelsefasen. Återställning stöds inte när resurserna har migrerats via incheckningsåtgärden.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Kan jag återställa migreringen om incheckningen misslyckas? 

Du kan inte avbryta migreringen om incheckningen misslyckas. Alla migreringsåtgärder, inklusive incheckning, är idempotenta. Vi rekommenderar därför att du gör om åtgärden om en stund. Om du fortfarande stöter på ett fel skapar du en supportbiljett.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Måste jag köpa en annan ExpressRoute-krets om jag måste använda IaaS med Resource Manager? 

Nej. Vi har nyligen gjort det möjligt att [flytta ExpressRoute-kretsar från klassiskt läge till Resource Manager-distributionsmodellen](../articles/expressroute/expressroute-move.md). Du behöver inte köpa någon ny ExpressRoute-krets om du redan har en.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Vad händer om jag har konfigurerat principer för rollbaserad åtkomstkontroll för mina klassiska IaaS-resurser? 

Under migreringen omformas resurserna från klassiskt läge till Resource Manager-läge. Vi rekommenderar att du planerar de RBAC-principuppdateringar som behövs efter migreringen.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Jag säkerhetskopierade mina klassiska virtuella datorer i ett valv. Kan jag migrera mina virtuella datorer från klassiskt läge till Resource Manager-läge och skydda dem i ett Recovery Services-valv?

När du flyttar en virtuell dator från klassiskt till Resource Manager-läge migreras inte säkerhetskopior som gjorts före migreringen till nymigrerad vm i Resource Manager. Men om du vill behålla dina säkerhetskopior av klassiska virtuella datorer följer du dessa steg före migreringen. 

1. Gå till fliken **Skyddade objekt i valvet** för återställningstjänster och välj den virtuella datorn. 
2. Klicka på Stoppa skydd. Lämna alternativet *Ta bort associerade säkerhetskopieringsdata***avmarkerat**.

> [!NOTE]
> Du debiteras kostnaden för säkerhetskopieringsinstans tills du behåller data. Säkerhetskopior kommer att beskäras enligt kvarhållningsintervallet. Den senaste säkerhetskopian sparas dock alltid tills du uttryckligen tar bort säkerhetskopierade data. Det rekommenderas att kontrollera din kvarhållningsintervall för den virtuella datorn och utlösa "Ta bort säkerhetskopierade data" på det skyddade objektet i valvet när kvarhållningsområdet är över. 
>
>

Om du vill migrera den virtuella datorn till Resource Manager-läge 

1. Ta bort tillägget för säkerhetskopiering/ögonblicksbild från den virtuella datorn.
2. Migrera den virtuella datorn från klassiskt läge till Resource Manager-läge. Kontrollera att lagrings- och nätverksinformationen för den virtuella datorn också migreras till Resource Manager-läge.

Om du vill säkerhetskopiera den migrerade virtuella datorn går du dessutom till hanteringsbladet för virtuella datorer för att [aktivera säkerhetskopiering](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Kan jag verifiera prenumeration eller resurser för att se om de kan migreras? 

Ja. I alternativet för migrering som stöds av plattformen är det första steget vid migreringsförberedelse att verifiera om resurserna kan migreras. Om resurserna inte godkänns för migrering får du meddelanden om orsaken till detta.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Vad händer om jag får ett kvotfel när IaaS-resurserna förbereds för migrering? 

Vi rekommenderar att du avbryter migreringen och loggar en supportbegäran om att öka kvoten i den region där du migrerar de virtuella datorerna. När din begäran om ökad kvot har godkänts kan du fortsätta migreringen igen.

## <a name="how-do-i-report-an-issue"></a>Hur gör jag för att rapportera ett problem? 

Gör ett inlägg med dina problem och frågor om migreringen på vårt [forum för virtuella datorer](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows) med nyckelordet ClassicIaaSMigration. Vi rekommenderar att du lägger upp alla dina frågor på det här forumet. Om du har ett supportavtal är du även välkommen att logga ett supportärende.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Vad gör jag om jag inte gillar namnen på resurserna som plattformen väljer under migreringen? 

Alla resursnamn som du uttryckligen angett i den klassiska distributionsmodellen behålls under migreringen. I vissa fall skapas nya resurser. Exempelvis skapas ett nätverksgränssnitt för varje virtuell dator. För närvarande har vi inte möjlighet att styra över namnen på de nya resurser som skapas under migreringen. Logga dina röster för den här funktionen i [Azures feedbackforum](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Kan jag migrera ExpressRoute-kretsar som används över prenumerationer med auktoriseringslänkar? 

ExpressRoute-kretsar som använder auktoriseringslänkar över flera prenumerationer migreras inte automatiskt utan driftavbrott. Det finns information om hur de kan migreras manuellt. Anvisningar och mer information finns i [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../articles/expressroute/expressroute-migration-classic-resource-manager.md) (Migrera ExpressRoute-kretsar och tillhörande virtuella nätverk från klassisk till Resource Manager-distributionsmodell).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Jag fick meddelandet *"VM rapporterar den övergripande agentstatus som inte redo. Därför kan den virtuella datorn inte migreras. Kontrollera att VM-agenten rapporterar övergripande agentstatus som klar* eller *"VM innehåller tillägg vars status inte rapporteras från den virtuella datorn. Därför kan den här virtuella datorn inte migreras."*

Det här meddelandet visas när den virtuella datorn saknar utgående anslutning till Internet. Den virtuella datoragenten använder utgående anslutning till Azure Storage-konto för att uppdatera agentstatusen var femte minut.
