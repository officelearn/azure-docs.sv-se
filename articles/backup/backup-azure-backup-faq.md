---
title: Vanliga frågor och svar om Azure Backup | Microsoft Docs
description: 'Svar på vanliga frågor om: Azure Backup-funktioner inklusive Recovery Services-valvet, vad du kan säkerhetskopiera, hur det fungerar, kryptering och gränser. '
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: säkerhetskopiering och katastrofåterställning, säkerhetskopieringstjänst
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/11/2018
ms.author: markgal;arunak;trinadhk;sogup;
ms.openlocfilehash: 9226bef986a0fd2b6e8454cbd78b659feda401b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="questions-about-the-azure-backup-service"></a>Frågor om Azure Backup-tjänsten
Den här artikeln innehåller svar på vanliga frågor om Azure Backup-komponenter. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan ställa frågor om Azure Backup genom att klicka på **Kommentarer** (till höger). Kommentarerna visas längst ned i den här artikeln. Ett Livefyre-konto krävs för att lämna kommentarer. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Om du snabbt vill titta igenom avsnitten i denna artikel kan du använda länkarna till höger, under **Innehåll i artikeln**.


## <a name="recovery-services-vault"></a>Recovery Services-valv

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Finns det någon gräns för antalet valv som kan skapas i varje Azure-prenumeration? <br/>
Ja. Från och med januari 2018, kan du skapa upp till 25 Recovery Services-valv, per region för stöds av Azure Backup per prenumeration. Om du behöver fler valv skapar du ytterligare en prenumeration.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Finns det några begränsningar för hur många servrar/datorer som kan registreras mot varje valv? <br/>
Du kan registrera upp till 200 Azure virtuella datorer per valvet. Om du använder MAB Agent, kan du registrera upp till 50 MAB agenter per valvet. Och du kan registrera 50 MAB servrar/DPM-servrar ett valv.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Min organisation har ett valv. Hur kan jag isolera en servers data från en annan server när jag återställer data?<br/>
Alla servrar som är registrerade för samma valv kan återställa data som säkerhetskopierats av andra servrar *som använder samma lösenfras*. Om du vill isolera säkerhetskopierade data på en server från andra servrar i organisationen kan du använda en annan lösenfras för dessa servrar. HR-servrarna kan till exempel använda en krypteringslösenfras, redovisningsservrarna en annan och lagringsservrar en tredje.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Kan jag ”migrera” mina säkerhetskopierade data eller mitt säkerhetskopierade valv mellan prenumerationer? <br/>
Nej. Valvet skapas på prenumerationsnivå och kan inte tilldelas en annan prenumeration när det har skapats.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>Recovery Services-valv är baserade på resurshanteraren. Säkerhetskopieringsvalv stöds fortfarande? <br/>
Säkerhetskopieringsvalv har konverterats till Recovery Services-valv. Om du inte har konverterat säkerhetskopieringsvalvet till Recovery Services-valvet har säkerhetskopieringsvalvet konverterats till ett Recovery Services-valv för dig. 

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Kan jag migrera ett Backup-valv till ett Recovery Services-valv? <br/>
Alla säkerhetskopieringsvalv har konverterats till Recovery Services-valv. Om du inte har konverterat säkerhetskopieringsvalvet till Recovery Services-valvet har säkerhetskopieringsvalvet konverterats till ett Recovery Services-valv för dig.

## <a name="azure-backup-agent"></a>Azure Backup-agent
En detaljerad lista med frågor finns i [vanliga frågor och svar om säkerhetskopiering av Azure-filmappen](backup-azure-file-folder-backup-faq.md)

## <a name="azure-vm-backup"></a>Säkerhetskopiering av virtuell Azure-dator
En detaljerad lista med frågor finns i avsnittet [Vanliga frågor och svar om säkerhetskopiering av virtuella Azure-datorer](backup-azure-vm-backup-faq.md)

## <a name="back-up-vmware-servers"></a>Säkerhetskopiera VMware-servrar

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kan jag säkerhetskopiera VMware vCenter-servrar till Azure?

Ja. Du kan använda Azure Backup Server för att säkerhetskopiera VMware vCenter och ESXi till Azure. Information om VMware-versionen som stöds finns i artikeln om [Azure Backup Server-skyddsmatrisen](backup-mabs-protection-matrix.md). Stegvisa instruktioner finns i artikeln om att [använda Azure Backup Server för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md).


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Azure Backup Server och System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Kan jag använda Azure Backup Server för att skapa en BMR-säkerhetskopia (Bare Metal Recovery) för en fysisk server? <br/>
Ja.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Kan jag registrera min DPM-server för flera valv? <br/>
Nej. En DPM- eller MABS-server kan bara registreras för ett enda valv.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>Vilken version av System Center Data Protection Manager stöds? <br/>
Vi rekommenderar att du installerar den [senaste](http://aka.ms/azurebackup_agent) Azure Backup-agenten med den senaste samlade uppdateringen (UR) för System Center Data Protection Manager (DPM). Från och med augusti 2016 är Samlad uppdatering 11 den senaste uppdateringen.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>Jag har installerat Azure Backup-agenten för att skydda mina filer och mappar. Kan jag nu installera System Center DPM för användning med Azure Backup-agenten om jag vill skydda lokala program/VM-arbetsbelastningar i Azure? <br/>
Om du vill använda Azure Backup med System Center Data Protection Manager (DPM) installerar du först DPM och sedan Azure Backup-agenten. Genom att installera Azure Backup-komponenterna i den här ordningen säkerställer du att Azure Backup-agenten fungerar med DPM. Installera inte Azure Backup-agenten innan du installerar DPM. Detta varken rekommenderas eller stöds.


## <a name="how-azure-backup-works"></a>Så här fungerar Azure Backup
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Tas säkerhetskopierade data som redan har överförts bort om jag avbryter ett säkerhetskopieringsjobb som redan har startat? <br/>
Nej. Alla data som har överförts till valvet innan säkerhetskopieringen avbröts finns kvar i valvet. Azure Backup använder en kontrollpunktsmekanism för att då och då lägga till kontrollpunkter till säkerhetskopierade data under säkerhetskopieringen. Eftersom det finns kontrollpunkter i säkerhetskopian kan nästa säkerhetskopiering validera filernas integritet. Nästa säkerhetskopieringsjobb är en inkrementell säkerhetskopiering mot tidigare säkerhetskopierade data. Vid inkrementella säkerhetskopieringar överförs bara nya eller ändrade data, vilket innebär att bandbredden utnyttjas bättre.

Om du avbryter ett säkerhetskopieringsjobb för en virtuella Azure-dator ignoreras alla överförda data. Nästa säkerhetskopieringsjobb överför inkrementella data från det senaste lyckade säkerhetskopieringsjobbet.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Finns det någon gräns för när eller hur många gånger ett säkerhetskopieringsjobb kan schemaläggas?<br/>
Ja. Du kan köra säkerhetskopieringsjobb på Windows Server eller Windows-arbetsstationer upp till tre gånger per dag. Du kan köra säkerhetskopieringsjobb på System Center DPM upp till två gånger per dag. Du kan köra ett säkerhetskopieringsjobb för virtuella IaaS-datorer en gång om dagen. Använd schemaläggningsprincip för Windows Server eller Windows-arbetsstation för att ange dagliga och veckovisa scheman. Du kan ange dagliga, veckovisa, månatliga och årliga scheman med System Center DPM.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Varför är storleken på data som överförs till Recovery Services-valvet mindre än de data jag säkerhetskopierar?<br/>
 Alla de data som säkerhetskopieras från Azure Backup Agent, SCDPM eller Azure Backup Server komprimeras och krypteras innan de överförs. När komprimering och kryptering används, är 30-40% mindre data i Recovery Services-valvet.

## <a name="what-can-i-back-up"></a>Vad kan jag säkerhetskopiera
### <a name="which-operating-systems-does-azure-backup-support-br"></a>Vilka operativsystem som stöder Azure Backup? <br/>
Azure Backup stöder följande lista över operativsystem för säkerhetskopiering av filer och mappar, och arbetsbelastningsprogram som skyddas med Azure Backup Server och System Center Data Protection Manager (DPM).

| Operativsystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows 8 och senaste Service Pack |64-bitars |Enterprise, Pro |
| Windows 7 och senaste Service Pack |64-bitars |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 och senaste Service Pack |64-bitars |Enterprise, Pro |
| Windows 10 |64-bitars |Enterprise, Pro, Home |
| Windows Server 2016 |64-bitars |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Datacenter, Foundation |
| Windows Server 2012 och senaste Service Pack |64-bitars |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 och senaste Service Pack |64-bitars |Standard, Workgroup | 
| Windows Storage Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Workgroup |
| Windows Storage Server 2012 och senaste Service Pack |64-bitars |Standard, Workgroup |
| Windows Server 2012 R2 och senaste Service Pack |64-bitars |Essential |
| Windows Server 2008 R2 SP1 |64-bitars |Standard, Enterprise, Datacenter, Foundation |

**Säkerhetskopiering av virtuell Azure-dator:**

* **Linux**: Azure Backup stöder [en lista över distributioner som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md) med undantag för Core OS Linux.  Andra Bring-Your-Own-Linux-distributioner kan också fungera så länge som den virtuella datoragenten är tillgänglig på den virtuella datorn och stöd för Python finns.
* **Windows Server**: versioner som är äldre än Windows Server 2008 R2 stöds inte.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Finns det någon storleksgräns för en datakälla som säkerhetskopieras? <br/>
Azure-säkerhetskopiering tillämpar en maximal storlek för en datakälla, men gränserna för datakällan är stor. Från och med augusti 2015 är den största storleken för en datakälla för operativsystem som stöds:

| Nr | Operativsystem | Största storlek på datakälla |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 eller senare |54 400 GB |
| 2 |Windows 8 eller senare |54 400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1 700 GB |
| 4 |Windows 7 |1 700 GB |

Följande tabell beskriver hur datakällans storlek bestäms.

| Datakälla | Information |
|:---:|:--- |
| Volym |Mängden data som säkerhetskopieras från en enskild volym på en server eller klientdator |
| Virtuell Hyper-V-dator |Summan av data på alla virtuella hårddiskar på den virtuella datorn som säkerhetskopieras |
| Microsoft SQL Server-databas |Storleken på en enskild SQL-databas som säkerhetskopieras |
| Microsoft SharePoint |Summan av innehållet och konfigurationsdatabaserna i en SharePoint-servergrupp som säkerhetskopieras |
| Microsoft Exchange |Summan av alla Exchange-databaser på en Exchange-server som säkerhetskopieras |
| BMR/systemtillstånd |Varje enskild kopia av BMR eller systemtillstånd på datorn som säkerhetskopieras |

Varje virtuell dator kan ha upp till 16 datadiskar för Azure IaaS-VM säkerhetskopiering, och varje datadisk kan vara upp till 4095 GB.

### <a name="is-there-a-limit-on-the-amount-of-data-held-in-a-recovery-services-vault"></a>Finns det en gräns på mängden data som lagras i ett Recovery Services-valv?
Det finns ingen gräns på mängden data som du kan säkerhetskopiera en återställningstjänster valvet.

## <a name="retention-policy-and-recovery-points"></a>Bevarandeprincip och återställningspunkter
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Finns det någon skillnad mellan bevarandeprincipen för DPM och Windows Server/Windows-klienten (dvs. på Windows Server utan DPM)?<br/>
Ingen, både DPM och Windows Server/Windows-klienten har dagliga, veckovisa, månatliga och årliga bevarandeprinciper.

### <a name="can-i-configure-my-retention-policies-selectively--that-is-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Kan jag konfigurera min bevarandeprinciper selektivt – det vill säga, konfigurera varje vecka varje dag men inte varje år och månad?<br/>
Ja, bevarandestrukturen i Azure Backup är mycket flexibel och du kan definiera bevarandeprincipen efter dina behov.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Kan jag ”schemalägga en säkerhetskopiering” kl. 18:00 och ange bevarandeprinciper vid en annan tidpunkt?<br/>
Nej. Bevarandeprinciper kan bara användas med säkerhetskopieringspunkter. I följande bild har bevarandeprincipen angetts för säkerhetskopieringar som körs kl. 12:00 och 18:00. <br/>

![Schemalägg säkerhetskopiering och kvarhållning](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Tar det längre tid att återställa en äldre datapunkt om en säkerhetskopia bevaras under lång tid? <br/>
Nej. Det tar lika lång tid att återställa den äldsta och den senaste punkten. Varje återställningspunkt beter sig som en fullständig punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Om varje återställningspunkt fungerar som en fullständig punkt, påverkas i så fall den totalt fakturerbara lagringen av säkerhetskopior?<br/>
Typiska produkter för långsiktiga kvarhållningspunkter lagrar säkerhetskopierade data som fullständiga punkter. De fullständiga punkterna är *ineffektiva* ur lagringssynvinkel men är enklare och snabbare att återställa. Inkrementella kopior är *lagringseffektiva* men kräver att du återställer en kedja med data som påverkar återställningstiden. Azure Backup-lagringsarkitekturen ger dig det bästa av två världar genom att lagra data för snabb återställning till låga lagringskostnader. Den här datalagringsmetoden säkerställer att den in- och utgående bandbredden används effektivt. Både mängden datalagring och tiden som krävs för att återställa data hålls nere på ett minimum. Läs mer om vad som gör [inkrementell säkerhetskopiering](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) effektiv.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Finns det någon gräns för antalet återställningspunkter som kan skapas?<br/>
Du kan skapa upp till 9999 återställningspunkter per skyddad instans. En skyddad instans är en dator eller server (fysisk eller virtuell) eller en arbetsbelastning som konfigurerats för att säkerhetskopiera data till Azure. Mer information finns i [Säkerhetskopiering och kvarhållning](./backup-introduction-to-azure-backup.md#backup-and-retention) och [Vad är en skyddad instans?](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Hur många återställningar kan jag göra av data som säkerhetskopierats till Azure?<br/>
Det finns ingen gräns för antalet återställningar från Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Betalar jag för den utgående trafiken från Azure när jag återställer data? <br/>
Nej. Återställningarna är gratis och du debiteras inte för den utgående trafiken.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Vad händer när jag ändrar min säkerhetskopieringsprincip?
När en ny princip används följs schema och lagring av den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.

## <a name="azure-backup-encryption"></a>Azure Backup-kryptering
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Krypteras informationen som skickas till Azure? <br/>
Ja. Data krypteras på den lokala server-/klient-/SCDPM-datorn med hjälp av AES256 och informationen skickas via en säker HTTPS-anslutning.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Är säkerhetskopierade data i Azure också krypterade?<br/>
Ja. Data som skickas till Azure förblir krypterade (i vila). Microsoft dekrypterar aldrig dina säkerhetskopierade data. När du säkerhetskopierar en virtuell Azure-dator använder Azure Backup kryptering för den virtuella datorn. Om den virtuella datorn till exempel har krypterats med Azure Disk Encryption, eller en annan krypteringsteknik, använder Azure Backup den krypteringen för att skydda dina data.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Vilken är den minsta längden på krypteringsnyckeln som används för att kryptera säkerhetskopierade data? <br/>
Krypteringsnyckeln ska innehålla minst 16 tecken när du använder Azure-säkerhetskopieringsagenten. För virtuella Azure-datorer finns det ingen begränsning av längden på de nycklar som används av Azure KeyVault. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Vad händer om jag tappar bort krypteringsnyckeln? Kan jag (eller Microsoft) återställa mina data? <br/>
Nyckeln som används för att kryptera säkerhetskopierade data finns bara hos kunden. Microsoft sparar ingen kopia i Azure och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan inte Microsoft återställa dina säkerhetskopierade data.
