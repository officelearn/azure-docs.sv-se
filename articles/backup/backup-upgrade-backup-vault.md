---
title: Uppgradera säkerhetskopieringsvalv till Recovery Services-valv för Azure Backup.
description: Uppgradera säkerhetskopieringsvalv till Recovery Services-valv för att hämta nya funktioner som säkerhetskopiering för Resource manager virtuella datorer, förbättrad säkerhet, VMware VM-säkerhetskopiering och säkerhetskopiering av systemtillstånd för Windows-servrar
services: backup
author: raynew
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: b7671271e569802311884861265a7825404c9c75
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490354"
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Säkerhetskopieringsvalvet har uppgraderats till Recovery Services-valv
Den här artikeln innehåller en översikt över vilka Recovery Services-valvet innehåller, vanliga frågor och svar om hur du uppgraderar befintliga Backup-valv till Recovery Services-valvet och aktiviteter efter uppgradering. Recovery Services-valvet är Azure Resource Manager-motsvarigheten till ett säkerhetskopieringsvalv som innehåller dina säkerhetskopierade data. Data är om vanligtvis kopior av data eller konfigurationsinformation för virtuella datorer (VM), arbetsbelastningar, servrar eller arbetsstationer, lokalt eller i Azure.

## <a name="what-is-a-recovery-services-vault"></a>Vad är ett Recovery Services-valv?
Ett Recovery Services-valv är en onlinelagringsentitet i Azure som används för att lagra data, som säkerhetskopior, återställningspunkter och principer för säkerhetskopiering. Du kan använda Recovery Services-valv för att lagra säkerhetskopierade data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services-valv stöd för System Center DPM, Windows Server, Azure Backup Server och mer. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Jämförelse Recovery Services-valv och Backup-valv
Recovery Services-valv baseras på Azure Resource Manager-modellen av Azure Backup-valv bygger på Azure Service Manager-modellen. När du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet bevaras säkerhetskopierade data under och efter uppgraderingen. Recovery Services-valv innehåller funktioner som är inte tillgängligt för säkerhetskopieringsvalv, som:

- **Förbättrade funktioner för att säkra säkerhetskopieringsdata**: Med Recovery Services-valv får Azure Backup säkerhetsfunktioner för att skydda säkerhetskopior i molnet. Dessa säkerhetsfunktioner se till att du kan skydda dina säkerhetskopior och återställa data på ett säkert sätt från säkerhetskopior i molnet även om produktions- och backup-servrar har komprometterats. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning av din IT-hybridmiljöer**: Med Recovery Services-valv kan du övervaka inte bara din [Azure IaaS-VM](backup-azure-manage-vms.md) utan även din [lokala tillgångar](backup-azure-manage-windows-server.md#manage-backup-items) från en central portal. [Läs mer](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Rollbaserad åtkomstkontroll (RBAC)**: RBAC ger detaljerad hantering av åtkomstkontroll i Azure. [Azure erbjuder olika inbyggda roller](../role-based-access-control/built-in-roles.md), och Azure Backup har tre [inbyggda roller för att hantera återställningspunkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC, vilket begränsar säkerhetskopiering utan att återställa åtkomst till en definierad uppsättning användarroller. [Läs mer](backup-rbac-rs-vault.md)

- **Skydda alla konfigurationer av virtuella datorer i Azure**: Recovery Services-valv skydda Resource Manager-baserade virtuella datorer, inklusive Premium Disks Managed Disks och krypterade virtuella datorer. Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet ger dig möjlighet att uppgradera din Service Manager-baserade virtuella datorer till Resource Manager-baserade virtuella datorer. När du uppgraderar valvet, kan du behålla dina återställningspunkter för Service Manager-baserad virtuell dator och konfigurera skydd för uppgraderade (Resource Manager-aktiverade) virtuella datorer. [Läs mer](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Omedelbar återställning för virtuella IaaS-datorer**: I Recovery Services-valv kan återställa du filer och mappar från en IaaS-VM utan att återställa hela VM, vilket gör att återställningen går snabbare. Omedelbar återställning för virtuella IaaS-datorer är tillgänglig för både Windows och Linux-datorer. [Läs mer](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Om du har objekt som har registrerats till ett säkerhetskopieringsvalv med MARS-agenten tidigare än 2.0.9083.0, [hämta den senaste MARS-agenten]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) versionen och dra fördelarna med alla funktioner i Recovery Services-valvet. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Hantera dina Recovery Services-valv
Följande skärmbilder visar en ny Recovery Services-valvet som uppgraderats från Backup-valv i Azure-portalen. Uppgraderade valvet kommer att finnas i en standard-resursgrupp med namnet ”standard-RecoveryServices-ResourceGroup-geo”. Exempel: Om din Backup-valvet fanns i västra USA, placeras den i en standard RG med namnet standard-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> För CPS-Standard kunder, resursgruppens namn inte har ändrats efter uppgradering av valvet och är densamma som den var före uppgraderingen.

Den första skärmen visar instrumentpanelen för valvet som visar viktiga entiteter för valvet.
![exempel på Recovery Services-valv som uppgraderats från ett säkerhetskopieringsvalv](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Den andra skärmen visar hjälp länkarna som är tillgängliga som hjälper dig att komma igång med Recovery Services-valvet.

![hjälplänkar i Snabbstart-bladet](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Aktiviteter efter uppgradering
Recovery Services-valv stöder konfiguration av återskrivningscachens tidszonsinformation i säkerhetskopieringsprincipen. När valvet har uppgraderats, gå till principer för säkerhetskopiering från inställningsmenyn och uppdatera informationen om tidszonen för var och en av principerna som konfigureras i valvet. Den här skärmen visar redan Säkerhetskopieringsschemat tiden som angetts som per lokala tidszon som används när du har skapat principen. 

## <a name="enhanced-security"></a>Förbättrad säkerhet
När ett säkerhetskopieringsvalv uppgraderas till ett Recovery Services-valv, aktiveras automatiskt säkerhetsinställningarna för det valvet. När säkerhetsinställningarna finns på vissa åtgärder, till exempel tar bort säkerhetskopior eller om du ändrar en lösenfras måste en [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN-kod. Mer information om förbättrad säkerhet finns i artikeln [säkerhetsfunktioner som skyddar hybridsäkerhetskopieringar](backup-azure-security-feature.md). När Förbättrad säkerhet är aktiverat, sparas data upp till 14 dagar efter punkt återställningsinformation har tagits bort från valvet. Kunderna debiteras för lagring av den här säkerhetsdata. Security datakvarhållning gäller återställningspunkter som vidtas för Azure Backup agent, Azure Backup Server och System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Samla in data i valvet
När du uppgradera till ett Recovery Services-valv Konfigurera rapporter för Azure Backup (för virtuella IaaS-datorer och Microsoft Azure Recovery Services agent) och använda Power BI för att komma åt rapporten. Mer information om datainsamling finns i artikeln [konfigurera Azure Backup-rapporter](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Påverkar uppgraderingsplanen min pågående säkerhetskopiering?**</br>
Nej. Säkerhetskopiorna pågående fortsätter utan avbrott under och efter uppgraderingen.

**Vad betyder detta uppgradering för Mina befintliga verktyg?**</br>
Resource Manager-distributionsmodellen så att de fortsätter att fungera efter uppgraderingen måste du uppdatera dina befintliga automatiseringstjänster och verktyg. Finns i PowerShell-cmdlet: ar referenser för den [Resource Manager-distributionsmodellen](backup-client-automation.md).

**Kan jag återställa efter uppgraderingen?**</br>
Nej. Återställning stöds inte när resurserna har uppgraderats.

**Kan jag visa mina klassiska efter uppgradering valv?**</br>
Nej. Du kan inte visa eller hantera ditt klassiska valv efter uppgradering. Du kommer endast att kunna använda den nya Azure-portalen för alla hanteringsåtgärder på valvet.

**Varför kan jag inte se servrar som skyddas av MARS-agenten i mitt uppgraderade valv?**</br>
Du måste installera den senaste MARS-agenten om du vill se alla servrar som skyddas av MARS-agenten i valvet. Du kan hämta den senaste versionen av agenten från [här]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Jag kan inte se princip för säkerhetskopiering för servrar som skyddas av MARS-agenten efter uppgraderingen**</br>
Valvets säkerhetskopieringsprincip kan vara inaktuell och kan därför inte synkroniseras till det uppgraderade valvet. Uppdatera principen för att se till att du fortsätter att se dina principer på det uppgraderade valvet.
Uppdatera principen genom att gå till MARS-agenten och uppdatera konfigurerade principen för säkerhetskopiering.

**Varför kan inte uppdatera min säkerhetskopieringspolicyn efter uppgraderingen?**</br>
Detta händer när du är på en gamla backup-agenten och välj minsta kvarhållningsperioden vara mindre än det minsta tillåtna värdet. När ett säkerhetskopieringsvalv uppgraderas till ett Recovery Services-valv, aktiveras automatiskt säkerhetsinställningarna för det valvet. För att säkerställa att det finns alltid ett giltigt antal återställningspunkter, finns det vissa minsta kvarhållningsperioden som måste upprätthållas enligt säkerhetsfunktionen. Mer information finns [här](backup-azure-security-feature.md).
Dessutom måste du uppdatera din Azure Backup-agenter till senaste versionen så fördelarna med de senaste funktionerna i Azure Backup.

**Jag har uppdaterat min agent, men jag kan fortfarande inte kan se alla objekt som synkroniseras t.o.m. dagar efter uppgraderingen**</br>
Kontrollera om du har registrerat samma dator för flera valv. Se till att du tittar på samma valv som MARS-agenten har registrerats. Om du vill veta vilka valvet MARS-agenten är registrerad, öppnar du Windows-registret och kontrollera värdet för nyckeln ServiceResourceName under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config registrerad valvet att MARS-agenten kommer att visas det. Om nyckeln ServiceResourceName inte visas i systemet, kontakta oss med värdet för tangenterna ResourceId och MachineId under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config och vi hjälper dig att lösa problemet.

**Varför kan jag inte se informationen om jobb för mina resurser efter uppgraderingen?**</br>
Övervakning av säkerhetskopior (MARS-agenten och IaaS) är en ny funktion som du får när du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valvet. Övervakningsinformation tar upp till 12 timmar att synkronisera med tjänsten.

**Hur gör jag för att rapportera ett problem?**</br>
Observera att åtgärds-ID anges i felet om en del av valvuppgraderingen misslyckas. Microsoft Support fungerar proaktivt för att lösa problemet. Du kan kontakta Support eller maila rsvaultupgrade@service.microsoft.com med ditt prenumerations-ID, valvnamnet och åtgärds-ID. Vi försöker lösa problemet så snabbt som möjligt. Försök inte igen såvida inte uttryckligen har angett att göra detta av Microsoft.

## <a name="next-steps"></a>Nästa steg
Använd följande artiklar om:</br>
[Säkerhetskopiera en IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera en Windows-Server](backup-configure-vault.md)
