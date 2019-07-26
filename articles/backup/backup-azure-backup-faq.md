---
title: Vanliga frågor och svar om Azure Backup
description: 'Svar på vanliga frågor om: Azure Backup funktioner, inklusive Recovery Services-valv, vad det kan säkerhetskopiera, hur det fungerar, kryptering och begränsningar. '
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: dacurwin
ms.openlocfilehash: c60b2bfae0d974d454c03b7eba655cbdacab5943
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466679"
---
# <a name="azure-backup---frequently-asked-questions"></a>Vanliga frågor och svar om Azure Backup
I den här artikeln besvaras vanliga frågor om tjänsten Azure Backup.

## <a name="recovery-services-vault"></a>Recovery Services-valv

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Finns det någon gräns för antalet valv som kan skapas i varje Azure-prenumeration?
Ja. Du kan skapa upp till 500 Recovery Services-valv, per region som stöds för Azure Backup per prenumeration. Om du behöver fler valv skapar du ytterligare en prenumeration.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Finns det några begränsningar för hur många servrar/datorer som kan registreras mot varje valv?
Du kan registrera upp till 1000 virtuella Azure-datorer per valv. Om du använder Microsoft Azure Backup-agenten kan du registrera upp till 50 MONOKLONALa agenter per valv. Och du kan registrera 50 monoklonal-servrar/DPM-servrar i ett valv.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Hur kan jag isolera data från olika servrar i valvet vid återställning av data om min organisation har ett valv?
Server data som du vill återställa tillsammans bör använda samma lösen fras när du konfigurerar säkerhets kopiering. Om du vill isolera återställning till en bestämd Server eller servrar använder du bara en lösen fras för den servern eller servrarna. HR-servrarna kan till exempel använda en krypteringslösenfras, redovisningsservrarna en annan och lagringsservrar en tredje.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kan jag flytta mitt valv mellan prenumerationer?
Ja. Om du vill flytta ett Recovery Services valv läser du den här [artikeln](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Kan jag flytta säkerhetskopierade data till ett annat valv?
Nej. Säkerhets kopierings data som lagras i ett valv kan inte flyttas till ett annat valv.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Kan jag ändra från GRS till LRS efter en säkerhets kopiering?
Nej. Ett Recovery Services-valv kan bara ändra lagrings alternativen innan säkerhets kopiorna har lagrats.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kan jag göra en återställning på objekt nivå (ILR) för virtuella datorer som har säkerhetskopierats till ett Recovery Services valv?
- ILR stöds för virtuella Azure-datorer som säkerhets kopie ras av virtuella Azure-datorer. Mer information finns i [artikeln](backup-azure-restore-files-from-vm.md)
- ILR stöds inte för online-återställnings punkter för lokala virtuella datorer som backas upp av Azure Backup Server eller System Center DPM.


## <a name="azure-backup-agent"></a>Azure Backup-agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Var kan jag hitta vanliga frågor om Azure Backup Agent för säkerhets kopiering av virtuella Azure-datorer?

- Läs följande [vanliga frågor och svar](backup-azure-vm-backup-faq.md)om agenten som körs på virtuella Azure-datorer.
- Läs [vanliga frågor och svar](backup-azure-file-folder-backup-faq.md)för den agent som används för att säkerhetskopiera Azure File Folders.


## <a name="general-backup"></a>Allmän säkerhets kopiering

### <a name="are-there-limits-on-backup-scheduling"></a>Finns det begränsningar för säkerhets kopierings schemaläggningen?
Ja.
- Du kan säkerhetskopiera Windows Server-eller Windows-datorer upp till tre gånger per dag. Du kan ange schemaläggnings principen till dagliga eller vecko Visa scheman.
- Du kan säkerhetskopiera DPM upp till två gånger per dag. Du kan ange schemaläggnings principen till varje dag, varje vecka, varje månad och varje år.
- Du säkerhetskopierar virtuella Azure-datorer en gång om dagen.

### <a name="what-operating-systems-are-supported-for-backup"></a>Vilka operativ system stöds för säkerhets kopiering?
Azure Backup stöder dessa operativ system för säkerhets kopiering av filer och mappar och appar som skyddas av Azure Backup Server och DPM.

**OS** | **SKU** | **Detaljer**
--- | --- | ---
Arbets Station | |
Windows 10 64-bit | Enterprise, Pro, Home | Datorerna ska köra de senaste Service Pack och uppdateringar.
Windows 8,1 64-bit | Enterprise, Pro | Datorerna ska köra de senaste Service Pack och uppdateringar.
Windows 8 64-bit | Enterprise, Pro | Datorerna ska köra de senaste Service Pack och uppdateringar.
Windows 7 64-bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Datorerna ska köra de senaste Service Pack och uppdateringar.
Server | |
Windows Server 2019 64-bitars | Standard, Datacenter, Essentials | Med de senaste Service Pack/-uppdateringarna.
Windows Server 2016 64-bitars | Standard, Datacenter, Essentials | Med de senaste Service Pack/-uppdateringarna.
Windows Server 2012 R2 64-bit | Standard, Datacenter, Foundation | Med de senaste Service Pack/-uppdateringarna.
Windows Server 2012 64-bitars | Datacenter, Foundation, Standard | Med de senaste Service Pack/-uppdateringarna.
Windows Storage Server 2016 64 bitar | Standard, Workgroup | Med de senaste Service Pack/-uppdateringarna.
Windows Storage Server 2012 R2 64 bit | Standard, arbets grupp, viktigt | Med de senaste Service Pack/-uppdateringarna.
Windows Storage Server 2012 64 bitar | Standard, Workgroup | Med de senaste Service Pack/-uppdateringarna.
Windows Server 2008 R2 SP1 64 bitar | Standard, Enterprise, Datacenter, Foundation | Med de senaste uppdateringarna.
Windows Server 2008 64-bitars | Standard, Enterprise, Datacenter | Med de senaste uppdateringarna.

För Azure VM Linux-säkerhetskopieringar stöder Azure Backup [listan över distributioner som har godkänts av Azure](../virtual-machines/linux/endorsed-distros.md), förutom Core OS Linux och 32-bitars operativ system. Andra distributioner av en egen Linux-distribution kan fungera så länge VM-agenten är tillgänglig på den virtuella datorn och stöd för python finns.


### <a name="are-there-size-limits-for-data-backup"></a>Finns det storleks gränser för säkerhets kopiering av data?
Storleks gränserna är följande:

OS/dator | Storleks gräns för data Källa
--- | ---
Windows 8 eller senare | 54 400 GB
Windows 7 |1 700 GB
Windows Server 2012 eller senare | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1 700 GB
Azure VM | 16 data diskar<br/><br/> Data disk upp till 4095 GB

### <a name="how-is-the-data-source-size-determined"></a>Hur bestäms storleken på data källan?
Följande tabell beskriver hur datakällans storlek bestäms.

**Data Källa** | **Detaljer**
--- | ---
Volym |Mängden data som säkerhets kopie ras från en virtuell volym som säkerhets kopie ras.
SQL Server-databas |Storlek på den individuella SQL Database-storlek som säkerhets kopie ras.
SharePoint | Summan av innehålls-och konfigurations databaserna i en SharePoint-servergrupp som säkerhets kopie ras.
Exchange |Summan av alla Exchange-databaser på en Exchange-Server som säkerhets kopie ras.
BMR/system tillstånd |Varje enskild kopia av BMR eller system tillstånd på datorn som säkerhets kopie ras.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Finns det en gräns för hur mycket data som säkerhets kopie ras med ett Recovery Services valv?
Det finns ingen gräns för mängden data som du kan säkerhetskopiera med ett Recovery Services-valv.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Varför är storleken på data som överförs till Recovery Services valvet mindre än de data som valts för säkerhets kopiering?
Data som säkerhets kopie ras från Azure Backup Agent, DPM och Azure Backup Server komprimeras och krypteras innan de överförs. När komprimering och kryptering används är data i valvet 30-40% mindre.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kan jag ta bort enskilda filer från en återställnings punkt i valvet?
Nej, Azure Backup har inte stöd för att ta bort eller rensa enskilda objekt från lagrade säkerhets kopior.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Om jag avbryter ett säkerhets kopierings jobb när det har startat, så tas överförda säkerhets kopierings data bort?
Nej. Alla data som överfördes till valvet innan säkerhets Kopieringen avbröts finns kvar i valvet.

- Azure Backup använder en kontrollpunktsmekanism för att då och då lägga till kontrollpunkter till säkerhetskopierade data under säkerhetskopieringen.
- Eftersom det finns kontrollpunkter i säkerhetskopian kan nästa säkerhetskopiering validera filernas integritet.
- Nästa säkerhetskopieringsjobb är en inkrementell säkerhetskopiering mot tidigare säkerhetskopierade data. Vid inkrementella säkerhetskopieringar överförs bara nya eller ändrade data, vilket innebär att bandbredden utnyttjas bättre.

Om du avbryter ett säkerhetskopieringsjobb för en virtuella Azure-dator ignoreras alla överförda data. Nästa säkerhetskopieringsjobb överför inkrementella data från det senaste lyckade säkerhetskopieringsjobbet.

## <a name="retention-and-recovery"></a>Kvarhållning och återställning

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Är bevarande principerna för DPM-och Windows-datorer utan DPM samma?
Ja, de har båda dagliga, vecko Visa, månatliga och årliga bevarande principer.

### <a name="can-i-customize-retention-policies"></a>Kan jag anpassa bevarande principer?
Ja, du har anpassat principer. Du kan till exempel konfigurera vecko Visa och dagliga krav för kvarhållning, men inte varje månad och månad.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Kan jag använda olika tider för säkerhets kopierings-och bevarande principer?
Nej. Bevarandeprinciper kan bara användas med säkerhetskopieringspunkter. De här avbildningarna visar till exempel en bevarande princip för säkerhets kopior som tas på 12am och. 18:00.

![Schemalägga säkerhetskopiering och kvarhållning](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Tar det längre tid att återställa en äldre data punkt om en säkerhets kopia behålls under en längre tid? <br/>
Nej. Tiden för att återställa den äldsta eller den senaste punkten är densamma. Varje återställningspunkt beter sig som en fullständig punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Om varje återställningspunkt fungerar som en fullständig punkt, påverkas i så fall den totalt fakturerbara lagringen av säkerhetskopior?
Typiska produkter för långsiktiga kvarhållningspunkter lagrar säkerhetskopierade data som fullständiga punkter.

- De fullständiga punkterna är *ineffektiva* ur lagringssynvinkel men är enklare och snabbare att återställa.
- Stegvisa kopior är lagrings *effektiva* men kräver att du återställer en data kedja som påverkar återställnings tiden

Azure Backup-lagringsarkitekturen ger dig det bästa av två världar genom att lagra data för snabb återställning till låga lagringskostnader. Detta säkerställer att din inkommande och utgående bandbredd används effektivt. Mängden data lagring och tiden som krävs för att återställa data behålls till ett minimum. Läs mer om [stegvisa säkerhets kopieringar](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Finns det någon gräns för antalet återställningspunkter som kan skapas?
Du kan skapa upp till 9999 återställningspunkter per skyddad instans. En skyddad instans är en dator, Server (fysisk eller virtuell) eller arbets belastning som säkerhetskopierar till Azure.

- Läs mer om [säkerhets kopiering och kvarhållning](./backup-overview.md#backup-and-retention).


### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hur många gånger kan jag återställa data som har säkerhetskopierats till Azure?
Det finns ingen gräns för antalet återställningar från Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Betalar jag för den utgående trafiken från Azure när jag återställer data?
Nej. Återställningen är kostnads fri och du debiteras inte för utgående trafik.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Vad händer när jag ändrar säkerhets kopierings policyn?
När en ny princip tillämpas, följs schema och kvarhållning av den nya principen.

- Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen.
- Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.

## <a name="encryption"></a>Kryptering

### <a name="is-the-data-sent-to-azure-encrypted"></a>Krypteras informationen som skickas till Azure?
Ja. Data krypteras på den lokala datorn med hjälp av AES256. Data skickas via en säker HTTPS-anslutning. De data som överförs i molnet skyddas av HTTPS-länken mellan lagrings-och återställnings tjänsten. iSCSI-protokollet skyddar de data som överförs mellan återställnings tjänsten och användar datorn. Säker tunnel trafik används för att skydda iSCSI-kanalen.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Är säkerhetskopierade data i Azure också krypterade?
Ja. Datan i Azure är krypterad – rest.

- Vid lokal säkerhets kopiering tillhandahålls kryptering vid vila med hjälp av den lösen fras som du anger när du säkerhetskopierar till Azure.
- För virtuella Azure-datorer krypteras data i vila med hjälp av Kryptering för lagringstjänst (SSE).

Microsoft dekrypterar aldrig dina säkerhetskopierade data.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Vilken är den minsta längden på kryptering av nyckeln som används för att kryptera säkerhetskopierade data?
Krypteringsnyckeln ska innehålla minst 16 tecken när du använder Azure-säkerhetskopieringsagenten. För virtuella Azure-datorer finns det ingen begränsning av längden på de nycklar som används av Azure KeyVault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Vad händer om jag tappar bort krypteringsnyckeln? Kan jag återställa data? Kan Microsoft återställa data?
Den nyckel som används för att kryptera säkerhetskopierade data finns bara på din webbplats. Microsoft sparar ingen kopia i Azure och har inte åtkomst till nyckeln. Om du har placerat nyckeln kan inte Microsoft återställa säkerhets kopierings data.

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

- [Vanliga frågor](backup-azure-vm-backup-faq.md) om säkerhets kopiering av virtuella Azure-datorer.
- [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup Agent
