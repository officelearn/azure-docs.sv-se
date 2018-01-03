---
title: "Uppgradera Backup-valvet till Recovery Services-valv för Azure Backup | Microsoft Docs"
description: "Uppgradera Backup-valvet till Recovery Services-valvet få nya funktioner som säkerhetskopiering av hanteraren för virtuella datorer, förbättrad säkerhet, VMware VM säkerhetskopiering och säkerhetskopian av systemtillstånd för Windows-servrar"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: 708983fc2c5264d1213bdb32b665dcccc5ca9df9
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Säkerhetskopieringsvalvet uppgraderas till Recovery Services-valvet
Den här artikeln innehåller en översikt över vilka Recovery Services-valvet tillhandahåller, vanliga frågor och svar om hur du uppgraderar en befintlig säkerhetskopia valvet Recovery Services-valvet och åtgärder efter uppgradering. Recovery Services-valvet motsvarar Azure Resource Manager ett säkerhetskopieringsvalv som innehåller dina säkerhetskopierade data. Data är om vanligtvis kopior av data eller konfigurationsinformation för virtuella datorer (VM), arbetsbelastningar, servrar eller arbetsstationer, lokalt eller i Azure.

## <a name="what-is-a-recovery-services-vault"></a>Vad är ett Recovery Services-valv?
Ett Recovery Services-valv är en onlinelagringsentitet i Azure som används för att lagra data, som säkerhetskopior, återställningspunkter och principer för säkerhetskopiering. Du kan använda Recovery Services-valv för att lagra säkerhetskopierade data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services valv stöd för System Center DPM, Windows Server, Azure Backup Server och mer. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Jämför Recovery Services-valv och säkerhetskopieringsvalv
Recovery Services-valv baseras på Azure Resource Manager-modell för Azure-säkerhetskopieringsvalv bygger på Azure Service Manager-modellen. När du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet bevaras säkerhetskopierade data under och efter uppgraderingen. Recovery Services-valv innehåller funktioner som är inte tillgängligt för säkerhetskopieringsvalv, som:

- **Förbättrade funktioner för att säkra säkerhetskopierade data**: med Recovery Services-valv, Azure Backup tillhandahåller säkerhetsfunktioner för att skydda molnet säkerhetskopieringar. Dessa funktioner Se till att du kan skydda dina säkerhetskopieringar och på ett säkert sätt återställa data från molnet säkerhetskopior, även om produktions- och backup-servrar som har angripits. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning av din IT-miljö för hybrid**: med Recovery Services-valv kan du övervaka inte bara din [Azure IaaS-VM](backup-azure-manage-vms.md) utan även din [lokala tillgångar](backup-azure-manage-windows-server.md#manage-backup-items) från en central portalen. [Läs mer](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Rollbaserad åtkomstkontroll (RBAC)**: RBAC ger detaljerade management åtkomstkontroll i Azure. [Azure tillhandahåller olika inbyggda roller](../active-directory/role-based-access-built-in-roles.md), och Azure Backup har tre [inbyggda roller för att hantera återställningspunkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC som begränsar säkerhetskopia och återställa åtkomst till en definierad uppsättning användarroller. [Läs mer](backup-rbac-rs-vault.md)

- **Skydda alla konfigurationer av virtuella datorer i Azure**: Recovery Services-valv skydda Resource Manager-baserade virtuella datorer inklusive Premiumdiskar, hanterade diskar och krypterade virtuella datorer. Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet ger dig möjlighet att uppgradera din Service Manager-baserade virtuella datorer till Resource Manager-baserade virtuella datorer. Du kan behålla dina återställningspunkter för Service Manager-baserade Virtuella och konfigurera skydd för uppgraderade (Resource Manager-aktiverat) virtuella datorer under uppgraderingen valvet. [Läs mer](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Omedelbar återställning för IaaS-VM**: med Recovery Services-valv kan du återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställning. Omedelbar återställning för IaaS-VM är tillgänglig för både Windows- och Linux virtuella datorer. [Läs mer](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Om du har registrerat sig för ett säkerhetskopieringsvalv med MARS-agenten tidigare än 2.0.9083.0, objekt [ladda ned den senaste MARS-agenten]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) version som ska ta fördelarna med alla funktioner i Recovery Services-valvet. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Hantera Recovery Services-valv
Följande skärmar visas ett nytt Recovery Services-valv som uppgraderats från säkerhetskopieringsvalvet i Azure-portalen. Uppgraderade valvet kommer att finnas i en standard-resursgrupp med namnet ”standard-RecoveryServices-ResourceGroup-geo”. Exempel: Om din Backup-valvet fanns i USA, västra den kommer tas i standard RG med namnet standard-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> CPS Standard kunder, resursgruppens namn inte har ändrats efter uppgraderingen valvet och är densamma som den var före uppgraderingen.

Den första skärmen visar valvet instrumentpanelen som visar viktiga entiteter för valvet.
![exempel på Recovery Services-valv som uppgraderats från ett säkerhetskopieringsvalv](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Den andra skärmen visar hjälp länkarna som är tillgängliga som hjälper dig att komma igång med Recovery Services-valvet.

![länkar i bladet Snabbstart](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Åtgärder efter uppgradering
Recovery Services-ventilen har stöd för att ange Tidszonsinformationen i princip för säkerhetskopiering. När valvet har uppgraderats, gå till principer för säkerhetskopiering från valvet inställningsmenyn och uppdatera informationen om tidszonen för var och en av de principer som konfigurerats i valvet. Den här skärmen visas redan Säkerhetskopieringsschemat tiden som angetts som per lokal tidszon som används när du har skapat principen. 

## <a name="enhanced-security"></a>Förbättrad säkerhet
När ett säkerhetskopieringsvalv uppgraderas till Recovery Services-valvet, aktiveras automatiskt säkerhetsinställningarna för det valvet. När säkerhetsinställningarna finns på vissa åtgärder, till exempel ta bort säkerhetskopior eller ändra en lösenfras kräva en [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) PIN-kod. Mer information om förbättrad säkerhet finns i artikeln [säkerhetsfunktioner för att skydda hybrid säkerhetskopieringar](backup-azure-security-feature.md). När Förbättrad säkerhet är aktiverad bevaras data upp till 14 dagar efter punkt återställningsinformation har tagits bort från valvet. Kunder som debiteras för lagring av den här säkerhetsdata. Säkerhet datalagring gäller återställningspunkter för Azure Backup-agenten, Azure Backup Server och System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Samla in data på ditt valv
När du uppgraderar till Recovery Services-valvet Konfigurera rapporter för Azure Backup (för IaaS-VM och Microsoft Azure Recovery Services agent) och använda Power BI åtkomst till rapporter. Mer information om insamling av data finns i artikeln [konfigurera Azure Backup rapporterar](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Påverkar uppgraderingsplanen mina pågående säkerhetskopior?**</br>
Nej. Säkerhetskopiorna pågående fortsätta utan avbrott under och efter uppgraderingen.

**Vad betyder det här uppgradering för Mina befintliga verktygsuppsättning?**</br>
Du måste uppdatera ditt befintliga automation eller verktygsuppsättning till Resource Manager-distributionsmodellen så att den fortsätter att fungera efter uppgraderingen. Finns i PowerShell-cmdlets referenser för den [Resource Manager-distributionsmodellen](backup-client-automation.md).

**Kan jag återställa efter uppgraderingen?**</br>
Nej. Återställning stöds inte när resurserna har uppgraderats.

**Kan jag visa min klassiska valvet efter uppgradering?**</br>
Nej. Du kan inte visa eller hantera din klassiska valvet efter uppgradering. Du kommer bara att kunna använda den nya Azure-portalen för alla hanteringsåtgärder på valvet.

**Varför ser inte servrar som skyddas av MARS-agenten i min uppgraderade valvet?**</br>
Du måste installera den senaste MARS-agenten om du vill se alla servrar som skyddas av MARS-agenten i ditt valv. Du kan hämta den senaste versionen av agenten från [här]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Du kan inte se princip för säkerhetskopiering för servrar som skyddas av MARS-agenten efter uppgraderingen**</br>
Valvets säkerhetskopieringsprincip kan vara inaktuell och därför gick inte att synkronisera till uppgraderade valvet. Uppdatera principen för att säkerställa fortsätter att visas dina principer i uppgraderade valvet.
Uppdatera principen genom att gå till MARS-agenten och uppdatera konfigurerade principen för säkerhetskopiering.

**Varför kan inte uppdatera princip för säkerhetskopiering efter uppgraderingen?**</br>
Detta händer när du är på en gamla backup-agenten och välj minsta kvarhållningsperioden vara mindre än det minsta tillåtna värdet. När ett säkerhetskopieringsvalv uppgraderas till Recovery Services-valvet, aktiveras automatiskt säkerhetsinställningarna för det valvet. För att säkerställa att det finns alltid ett giltigt antal återställningspunkter, finns det vissa period minst som måste upprätthållas enligt säkerhetsfunktionen. Mer information finns i [här](backup-azure-security-feature.md).
Du måste dessutom uppdatera Azure Backup-agenterna till senaste versionen för att ta fördelarna med de senaste funktionerna i Azure Backup.

**Jag har uppdaterat min agent, men visas fortfarande inte alla objekt som synkroniseras t.o.m. dagar efter uppgraderingen**</br>
Kontrollera om du har registrerat samma dator till flera valv. Se till att du tittar på samma valvet som MARS-agenten har registrerats. Om du vill ta reda på vilka valvet MARS-agenten är registrerad, öppna Windows-registret och kontrollera värdet för nyckeln ServiceResourceName HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config registrerad valvet som MARS-agenten ska visas Det finns. Om nyckeln ServiceResourceName inte visas i systemet, nå ut till oss med värdet för tangenterna ResourceId och MachineId under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config och hjälper vi dig att lösa problemet.

**Varför visas inte jobb-information för mina resurser efter uppgraderingen?**</br>
Övervakning för säkerhetskopiering (MARS-agenten och IaaS) är en ny funktion som du får när du uppgraderar din Backup-valvet till Recovery Services-valvet. Övervakning information tar upp till 12 timmar att synkronisera med tjänsten.

**Hur rapporterar problem?**</br>
Observera åtgärds-ID som anges i felet om en del av valvet uppgraderingen misslyckas. Microsoft-supporten fungerar proaktivt för att lösa problemet. Du kan nå ut till Support eller skicka e-post på rsvaultupgrade@service.microsoft.com med prenumerations-ID, valvnamnet och åtgärds-ID. Vi kommer att försöka lösa problemet så snabbt som möjligt. Försök inte igen såvida inte uttryckligen uppmanas att göra det från Microsoft.

## <a name="next-steps"></a>Nästa steg
Använd följande artiklar för:</br>
[Säkerhetskopiera en IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup-Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera Windows Server](backup-configure-vault.md)
