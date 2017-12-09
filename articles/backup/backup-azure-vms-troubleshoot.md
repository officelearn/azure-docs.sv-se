---
title: "Felsöka säkerhetskopiering med Azure-dator | Microsoft Docs"
description: "Felsökning av säkerhetskopiering och återställning av virtuella Azure-datorer"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: trinadhk;markgal;jpallavi;
ms.openlocfilehash: 5c4ea3e3714f6a3989a260937c2c67815a6dd6f7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Felsöka säkerhetskopiering av virtuell Azure-dator
> [!div class="op_single_selector"]
> * [Recovery services-valvet](backup-azure-vms-troubleshoot.md)
> * [Säkerhetskopieringsvalvet](backup-azure-vms-troubleshoot-classic.md)
>
>

Du kan felsöka fel påträffades när med Azure Backup med information som visas i tabellen nedan.

## <a name="backup"></a>Säkerhetskopiering

### <a name="error-the-specified-disk-configuration-is-not-supported"></a>Fel: Den angivna diskkonfigurationen stöds inte

> [!NOTE]
> Vi har en privat förhandsgranskning för att stödja säkerhetskopieringar för virtuella datorer med > 1TB ohanterad diskar. Information finns i [privat förhandsgranskning för stora diskstöd för säkerhetskopiering av VM](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

För närvarande Azure Backup stöder inte diskstorlekar [större än 1 023 GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Om du har diskar som är större än 1 TB [bifoga nya diskar](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) som är mindre än 1 TB <br>
- Kopiera sedan data från disk som är större än 1TB i nyskapade diskarna på mindre än 1 TB. <br>
- Se till att alla data har kopierats och ta bort diskarna som är större än 1TB
- Starta säkerhetskopieringen.

| information om fel | Lösning |
| --- | --- |
| Det gick inte att utföra åtgärden eftersom den virtuella datorn inte finns längre. -Stoppa skydd av virtuell dator utan att ta bort säkerhetskopierade data. Mer information på http://go.microsoft.com/fwlink/?LinkId=808124 |Detta händer när den primära virtuella datorn tas bort, men säkerhetskopieringsprincipen fortsätter söker en virtuell dator för att säkerhetskopiera. Åtgärda det här felet: <ol><li> Skapa den virtuella datorn med samma namn och samma resursgruppens namn [cloud service name]<br>(ELLER)</li><li> Sluta skydda virtuella datorer med eller utan att ta bort säkerhetskopierade data. [Mer information](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Ögonblicksbild misslyckades på grund av ingen nätverksanslutning på den virtuella datorn – se till att virtuella datorn har åtkomst till nätverket. För ögonblicksbilden ska lyckas, antingen godkända Azure-datacenter IP-adressintervall eller konfigurera en proxyserver för nätverksåtkomst. Mer information finns i http://go.microsoft.com/fwlink/?LinkId=800034. Om du redan använder proxyserver, kontrollera att proxyserverinställningarna har konfigurerats korrekt | Det här felet returneras när du neka utgående internet-anslutning på den virtuella datorn. Internetanslutning krävs för snapshot-tillägg för virtuell dator att ta en ögonblicksbild av underliggande diskar för den virtuella datorn. [Lär dig mer](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine) på hur du löser ögonblicksbild fel på grund av blockerade nätverksåtkomst. |
| VM-agenten kan inte kommunicera med Azure Backup-tjänsten. -Kontrollera att den virtuella datorn är ansluten till nätverket och den Virtuella datoragenten är senaste och körs. Mer information finns i http://go.microsoft.com/fwlink/?LinkId=800034 |Det här felet returneras om det uppstår ett problem med den Virtuella Datoragenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. [Lär dig mer](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) om felsökning av VM ögonblicksbild problem.<br> Om den Virtuella datoragenten inte orsakar problem, och starta sedan om den virtuella datorn. Ett felaktigt tillstånd för virtuell dator kan ibland orsaka problem och starta om den virtuella datorn återställs den här ”dåligt tillstånd”. |
| Den virtuella datorn är i felläge för etablering – starta om den virtuella datorn och kontrollera att den virtuella datorn är igång eller avställning för säkerhetskopiering | Detta inträffar när en av tillägget fel leder tillstånd för virtuell dator är i feltillstånd etablering. Gå till tilläggslista och om det finns ett misslyckade tillägg, ta bort den och försök att starta om den virtuella datorn. Om alla tillägg körs, kan du kontrollera om VM-agenttjänsten körs. Om inte, starta om tjänsten för VM-agent. | 
| VMSnapshot tillägget misslyckades för hanterade diskar - försök sedan att utföra säkerhetskopieringen. Följ anvisningarna på 'http://go.microsoft.com/fwlink/?LinkId=800034' om problemet upprepas. Kontakta Microsoft support om det misslyckas ytterligare | Det här felet när Azure Backup-tjänsten inte kan utlösa en ögonblicksbild. [Lär dig mer](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) om felsökning VM ögonblicksbild problem. |
| Det gick inte att kopiera ögonblicksbilden av den virtuella datorn på grund av otillräckligt utrymme i storage-konto - Se till att lagringskontot har ledigt utrymme som är likvärdiga med data som finns på premium storage diskar som är kopplade till den virtuella datorn | Vid premium VMs kopiera vi ögonblicksbilden till storage-konto. Detta är att se till att säkerhetskopiering hanteringstrafik som arbetar på ögonblicksbild, inte antalet IOPS som är tillgängliga för det program som använder premiumdiskar. Microsoft rekommenderar att du allokerar högst 50% av det totala lagringsutrymmet konto så Azure Backup-tjänsten kan kopiera ögonblicksbilden till storage-konto och överför data från denna kopierade plats i lagringskontot till valvet. | 
| Det gick inte att utföra åtgärden eftersom den Virtuella datoragenten inte svarar |Det här felet returneras om det uppstår ett problem med den Virtuella Datoragenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. För virtuella Windows-datorer, kontrollera Tjänststatus för VM-agenten i services och om agenten ska visas i program på Kontrollpanelen. Försök att ta bort programmet från kontrollen panelen och installera om agenten som tidigare nämnts [under](#vm-agent). När du har installerat agenten, Utlös en ad hoc-säkerhetskopiering för att verifiera. |
| Recovery services-tillägget åtgärden misslyckades. -Kontrollera att den senaste virtuella datorns agent finns på den virtuella datorn och agent-tjänsten körs. Försök utföra säkerhetskopieringen igen och kontakta Microsoft support om det misslyckas. |Det här felet returneras när VM-agenten är inaktuell. Läs ”uppdatering av VM-Agent” avsnittet nedan för att uppdatera den Virtuella datoragenten. |
| Virtuell dator finns inte. -Kontrollera att den virtuella datorn finns eller välj en annan virtuell dator. |Detta händer när den primära virtuella datorn tas bort men säkerhetskopieringsprincipen fortsätter att söka efter en virtuell dator att utföra säkerhetskopiering. Åtgärda det här felet: <ol><li> Skapa den virtuella datorn med samma namn och samma resursgruppens namn [cloud service name]<br>(ELLER)<br></li><li>Sluta skydda den virtuella datorn utan att ta bort säkerhetskopierade data. [Mer information](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Det gick inte att köra. -En annan åtgärd pågår på det här objektet. Vänta tills den föregående åtgärden har slutförts och försök sedan igen |En befintlig säkerhetskopia på den virtuella datorn körs och går inte att starta ett nytt jobb medan befintliga jobbet körs. |
| Kopiera virtuella hårddiskar från säkerhetskopieringsvalvet timeout - försök igen om några minuter. Kontakta Microsoft-supporten om problemet kvarstår. | Detta händer om det är ett tillfälligt fel på sidan för lagring eller om säkerhetskopieringstjänsten inte får tillräckligt IOPS från storage-konto som är värd för den virtuella datorn för att överföra data inom tidsgränsen till valvet. Kontrollera att du följt [metodtips](backup-azure-vms-introduction.md#best-practices) när du konfigurerar säkerhetskopiering. Försök att flytta en virtuell dator till olika lagringsenheter säkerhetskopieringen av kontot som inte är inläst och försök igen.|
| Det gick inte att säkerhetskopiera med ett internt fel - försök igen om några minuter. Kontakta Microsoft Support om problemet kvarstår |Du kan få felet 2 skäl: <ol><li> Det finns ett övergående problem med att komma åt VM-lagring. Kontrollera [Azure Status](https://azure.microsoft.com/en-us/status/) om det finns några pågående problem som rör beräkna, lagring, eller nätverk i regionen. Försök sedan säkerhetskopiera igen när problemet är löst. <li>Den ursprungliga virtuella datorn har tagits bort och därför återställningspunkten kan inte utföras. Hela säkerhetskopierade data för en borttagen virtuell dator, men ta bort säkerhetskopiering felen: ta bort skyddet från den virtuella datorn och välja alternativet att behålla data. Den här åtgärden stoppar schemalagda säkerhetskopieringsjobbet och återkommande felmeddelanden. |
| Det gick inte att installera Azure Recovery Services-tillägget för det markerade objektet - är den virtuella datorns agent en förutsättning för Azure Recovery Services-tillägget. Installera Azure VM-agenten och starta om åtgärden registrering |<ol> <li>Kontrollera om VM-agenten har installerats korrekt. <li>Kontrollera flaggan på VM-konfigurationen är korrekt.</ol> [Läs mer](#validating-vm-agent-installation) om hur du installerar den Virtuella datoragenten och hur du verifierar installationen av VM. |
| Installation av webbprogramtillägg misslyckades med felet ”COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator |Detta innebär vanligen att COM +-tjänsten inte körs. Kontakta Microsoft support för hjälp med att åtgärda problemet. |
| Snapshot-åtgärden misslyckades med fel i VSS ”den här enheten är låst av BitLocker-diskkryptering. Du måste låsa upp enheten från Kontrollpanelen. |Inaktivera BitLocker för alla enheter på den virtuella datorn och notera om VSS problemet är löst |
| Virtuella datorn är inte i ett tillstånd som gör säkerhetskopior. |<ul><li>Kontrollera om VM är i ett övergående tillstånd mellan körs och stängs ned. Om det är väntar tillstånd för virtuell dator att vara en av dem och utlösa säkerhetskopiera igen. <li> Om den virtuella datorn är en Linux VM och använder [Security Enhanced Linux] kernel-modul, måste du utesluta sökvägen Linux-agenten (_/var/lib/waagent_) från säkerhet princip för att se till att reservanknytning installeras.  |
| Virtuell Azure-dator inte att hitta. |Detta händer när den primära virtuella datorn tas bort men säkerhetskopieringsprincipen fortsätter att söka efter en virtuell dator att utföra säkerhetskopiera. Åtgärda det här felet: <ol><li>Skapa den virtuella datorn med samma namn och samma resursgruppens namn [cloud service name] <br>(ELLER) <li> Inaktivera skyddet för den här virtuella datorn så att säkerhetskopiera jobb inte skapas. </ol> |
| Virtuella datorns agent är inte installerat på den virtuella datorn – installera alla nödvändiga och VM-agenten och starta sedan om åtgärden. |[Läs mer](#vm-agent) om VM agentinstallation och hur du verifierar installationen av virtuell dator. |
| Snapshot-åtgärden misslyckades på grund av VSS-skrivare i ett felaktigt tillstånd |Du måste starta om VSS (Volume Shadow copy Service)-skrivare som är i ett felaktigt tillstånd. Om du vill göra detta, från en upphöjd kommandotolk, kör _vssadmin listan skrivare_. Utdata innehåller alla VSS-skrivare och deras tillstånd. För varje vars status är inte ”[1] stabil” VSS-skrivare måste du starta om VSS-skrivaren genom att köra följande kommandon från en upphöjd kommandotolk:<br> _net stop serviceName_ <br> _net start serviceName_|
| Snapshot-åtgärden misslyckades på grund av ett fel som tolkning av konfigurationen |Detta inträffar på grund av ändrade behörigheter på katalogen MachineKeys: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Kör nedan kommandot och kontrollera att behörigheterna för MachineKeys directory är standard-som:<br>_Icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Standardbehörigheterna är:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Om du ser behörigheter på MachineKeys directory annat än standard, Följ nedanstående steg för att rätt behörighet, ta bort certifikatet och att säkerhetskopiera.<ol><li>Åtgärda behörighet på MachineKeys directory.<br>Med hjälp av egenskaper för Explorer-säkerhet och avancerade säkerhetsinställningar för katalogen återställa behörigheter till standardvärden tar du bort alla användarobjekt som extra (än standard) från katalogen och se till att 'Alla' behörigheter hade särskild behörighet för:<br>-Lista mappar / läsa data <br>-Läsa attribut <br>-Läs utökade attribut <br>-Skapa filer / skriva data <br>-Skapa mappar / lägga till data<br>-Skriva attribut<br>-Skriva utökade attribut<br>-Läsbehörighet<br><br><li>Ta bort alla certifikat med fältet utfärdat till = ”Windows Azure Service Management för tillägg” eller ”Windows Azure CRP-certifikatet Generator”.<ul><li>[Öppna konsolen certifikat (lokal dator)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Ta bort alla certifikat (-> certifikat under Personal) med fältet utfärdat till = ”Windows Azure Service Management för tillägg” eller ”Windows Azure CRP-certifikatet Generator”.</ul><li>Utlösaren säkerhetskopiering. </ol>|
| Verifieringen misslyckades eftersom den virtuella datorn är krypterad med BEK enbart. Säkerhetskopieringar kan bara aktiveras för virtuella datorer som har krypterats med både BEK och KEK. |Virtuell dator ska krypteras med både BitLocker-krypteringsnyckeln och nyckeln för kryptering. Efter att ska säkerhetskopieringen aktiveras. |
| Azure Backup-tjänsten har inte tillräcklig behörighet för att Key Vault för säkerhetskopiering av krypterade virtuella datorer. |Säkerhetskopieringstjänsten ska tillhandahållas behörigheterna i PowerShell med hjälp av anvisningarna i **Aktivera säkerhetskopiering** avsnitt i [PowerShell dokumentationen](backup-azure-vms-automation.md). |
|Installation av ögonblicksbild misslyckades med felet tillägg – COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator | Försök att starta windows-tjänst ”COM + System-program” (från en upphöjd kommandotolk - _net start COMSysApp_). <br>Följ nedanstående steg om det misslyckas under start:<ol><li> Kontrollera att inloggningskontot för tjänsten ”Distributed Transaction Coordinator” är ”Nätverkstjänst”. Om det inte ändra den till ”nätverkstjänst”, starta om tjänsten och försök att starta tjänsten ”COM + System-program” ”.<li>Om det fortfarande inte att starta, avinstallera och installera tjänsten ”Distributed Transaction Coordinator” genom att följa nedanstående steg:<br> -Stoppa MSDTC-tjänsten<br> -Öppna en kommandotolk (cmd) <br> -Köra kommandot ”msdtc-avinstallera” <br> -Köra kommandot ”msdtc-installera” <br> -Börja MSDTC-tjänsten<li>Starta windows-tjänst ”COM + System-program” och när den startas startar Säkerhetskopiering från portalen.</ol> |
|  Snapshot-åtgärden misslyckades på grund av COM +-fel | Den rekommenderade åtgärden är att starta om windows-tjänst ”COM + System-program” (från en upphöjd kommandotolk - _net start COMSysApp_). Om problemet kvarstår startar du om den virtuella datorn. Om det inte hjälper att starta om den virtuella datorn försöker [tar bort tillägget VMSnapshot](https://docs.microsoft.com/en-us/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) och att säkerhetskopiera manuellt. |
| Det gick inte att låsa en eller flera-monteringspunkter på den virtuella datorn att vidta en programkonsekvent ögonblicksbild för filsystemet | Använd följande steg: <ol><li>Kontrollera tillståndet för filsystemet för alla monterade enheter med hjälp av _'tune2fs'_ kommando.<br> Exempel: tune2fs -l/dev/sdb1 \| GREP ”Filesystem tillstånd” <li>Demontera enheter för vilka filesystem tillstånd inte är ren med _'umount'_ kommando <li> Kör FileSystemConsistency finns på dessa enheter med hjälp av _'fsck'_ kommando <li> Montera enheterna igen och försök säkerhetskopiering.</ol> |
| Snapshot-åtgärden misslyckades på grund av fel i att skapa säkra nätverk kommunikationskanalen | <ol><Li> Öppna Registereditorn genom att köra regedit.exe en förhöjd behörighet. <li> Identifiera alla versioner av. NetFramework finns i systemet. De finns under hierarkin för registernyckeln ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft” <li> För varje. NetFramework finns i registernyckeln, Lägg till följande nyckel: <br> ”SchUseStrongCrypto” = DWORD: 00000001 </ol>|
| Snapshot-åtgärden misslyckades på grund av fel i installationen av Visual C++ Redistributable för Visual Studio 2012 | Navigera till C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion och installera vcredist2012_x64. Kontrollera att värdet för registernyckeln för att tillåta installationen av den här tjänsten anges till rätt värde dvs. värdet för registernyckeln _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ är inställd på 3 och 4 inte. Om du fortfarande inför problem med installation, starta om installationen genom att köra _MSIEXEC /UNREGISTER_ följt av _MSIEXEC /REGISTER_ från en upphöjd kommandotolk.  |


## <a name="jobs"></a>Jobb
| information om fel | Lösning |
| --- | --- |
| Annullering stöds inte för den här jobbtypen - vänta tills jobbet har slutförts. |Ingen |
| Jobbet är inte i tillståndet cancelable - vänta tills jobbet har slutförts. <br>ELLER<br> Det valda jobbet är inte i tillståndet cancelable - vänta tills jobbet ska slutföras. |Med största sannolikhet slutförs nästan jobbet. Vänta tills jobbet har slutförts.|
| Det går inte att avbryta jobbet eftersom det pågår inte – annullering stöds bara för jobb som pågår. Försök avbryter på ett pågående jobb. |Detta inträffar på grund av ett övergående tillstånd. Vänta en stund och försök igen Avbryt. |
| Det gick inte att avbryta jobbet - vänta tills jobbet har slutförts. |Ingen |

## <a name="restore"></a>Återställ
| information om fel | Lösning |
| --- | --- |
| Det gick inte att återställa med molnet internt fel |<ol><li>Molntjänst som du försöker återställa har konfigurerats med DNS-inställningarna. Du kan kontrollera <br>$deployment = get-AzureDeployment - ServiceName ”ServiceName”-fack ”produktion” Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Om adressen konfigurerad, innebär det att DNS-inställningarna har konfigurerats.<br> <li>Molntjänst som du försöker återställa har konfigurerats med ReservedIP och befintliga virtuella datorer i Molntjänsten har stoppats.<br>Du kan kontrollera en molnbaserad tjänst har reserverade IP-Adressen med hjälp av följande powershell-cmdlets:<br>$deployment = get-AzureDeployment - ServiceName ”servicename”-fack ”produktion” $dep. ReservedIPName <br><li>Du försöker återställa en virtuell dator med följande särskilda nätverkskonfigurationer i samma molntjänst. <br>-Virtuella datorer under konfigurationen av belastningsutjämnaren (interna och externa)<br>-Virtuella datorer med flera reserverade IP:<br>-Virtuella datorer med flera nätverkskort<br>Välj en ny molntjänst i Användargränssnittet eller se [återställa överväganden](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) för virtuella datorer med särskilda nätverkskonfigurationer.</ol> |
| Det valda DNS-namnet är upptaget – ange ett annat DNS-namn och försök igen. |DNS-namnet här refererar till molntjänstnamnet (vanligtvis slutar med. cloudapp.net). Detta måste vara unika. Om du får det här felet måste du välja ett annat VM-namn under återställningen. <br><br> Det här felet visas endast på användare i Azure-portalen. Återställningsåtgärden via PowerShell lyckas eftersom det endast återställer diskarna och inte skapa den virtuella datorn. Felet kommer riktas när den virtuella datorn skapas av du uttryckligen när disken återställningsåtgärden. |
| Den angivna virtuella nätverkskonfigurationen är inte korrekt – ange ett annat virtuellt nätverk-konfigurationen och försök igen. |Ingen |
| Den angivna Molntjänsten använder en reserverad IP-adress, som inte överensstämmer med konfigurationen av den virtuella datorn återställs – ange en annan molntjänst som inte använder reserverade IP: N, eller välj en annan återställningspunkt att återställa från. |Ingen |
| Molntjänsten har nått gränsen för antalet inkommande slutpunkter, försök igen genom att ange en annan molntjänst eller genom att använda en befintlig slutpunkt. |Ingen |
| Backup-valvet och mål för storage-konto finns i två olika regioner och kontrollera att lagringskontot som angavs i restore-åtgärden är i samma Azure-region som säkerhetskopieringsvalvet. |Ingen |
| Storage-konto som angetts för återställningen inte är stöds - endast grundläggande/Standard storage-konton med lokalt redundant eller inställningarna för geo-redundant replikering stöds. Välj ett lagringskonto som stöds |Ingen |
| Typ av Lagringskonto som angetts för återställningen är inte online – se till att lagringskontot som angavs i restore-åtgärden är online |Detta kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagringskonto. |
| Resursgruppen kvoten har nåtts – ta bort vissa resursgrupper från Azure-portalen eller kontakta Azure-supporten om du vill öka gränserna. |Ingen |
| Valda undernätet finns inte - Välj ett undernät som finns |Ingen |
| Säkerhetskopieringstjänsten har inte åtkomstbehörighet till resurser i din prenumeration. |Lös detta genom första återställa diskar med hjälp av anvisningarna i avsnittet **återställning säkerhetskopierade diskar** i [välja VM Återställ konfiguration](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Sedan kan använda PowerShell-åtgärder som nämns i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) att skapa fullständiga virtuell dator från återställda diskar. |

## <a name="backup-or-restore-taking-time"></a>Säkerhetskopieringen eller återställningen tar tid
Om du ser ditt backup(>12 hours) eller återställa tar time(>6 hours):
* Förstå [faktorer bidrar till säkerhetskopieringstiden](backup-azure-vms-introduction.md#total-vm-backup-time) och [faktorer bidrar om du vill återställa tid](backup-azure-vms-introduction.md#total-restore-time).
* Se till att du följer [säkerhetskopiera metodtips](backup-azure-vms-introduction.md#best-practices). 

## <a name="vm-agent"></a>VM-Agent
### <a name="setting-up-the-vm-agent"></a>Konfigurera VM-Agent
Normalt finns VM-agenten redan i virtuella datorer som har skapats från Azure-galleriet. Virtuella datorer som har migrerats från lokala Datacenter skulle inte ha VM-agenten installerad. För sådana virtuella datorer måste den Virtuella Datoragenten installeras explicit.

För virtuella Windows-datorer:

* Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
* För klassiska virtuella datorer, [uppdatera egenskapen VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) att indikera att agenten är installerad. Det här steget krävs inte för hanteraren för virtuella datorer.

För Linux virtuella datorer:

* Installera senaste från databasen för distribution. Vi **rekommenderar** agent installeras bara igenom distribution-databasen. Information om paketnamn, referera till [databasen för Linux-agent](https://github.com/Azure/WALinuxAgent) 
* För klassiska virtuella datorer kan [uppdatera egenskapen VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) att indikera att agenten är installerad. Det här steget krävs inte för hanteraren för virtuella datorer.

### <a name="updating-the-vm-agent"></a>Uppdatera VM-agenten
För virtuella Windows-datorer:

* Det är enkelt att uppdatera VM-agenten. Du installerar bara om [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste dock se till att inga Säkerhetskopieringsåtgärden körs medan den Virtuella Datoragenten uppdateras.

För Linux virtuella datorer:

* Följ instruktionerna på [uppdatering Linux VM-agenten](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Vi **rekommenderar** uppdatering agenten endast via distribution databasen. Vi rekommenderar inte hämta koden för agenten från direkt github och uppdatera den. Om senaste agenten inte är tillgänglig för din distribution du nå distributionsplatsen stöd för instruktioner om hur du installerar senaste agenten. Du kan kontrollera senaste [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) informationen i github-lagringsplatsen.

### <a name="validating-vm-agent-installation"></a>Verifiera installation av VM-Agent
Hur du kontrollerar agentversionen VM på virtuella Windows-datorer:

1. Logga in på den virtuella Azure-datorn och navigera till mappen *C:\WindowsAzure\Packages*. Du bör hitta filen WaAppAgent.exe.
2. Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet produktversionen får vara 2.6.1198.718 eller högre

## <a name="troubleshoot-vm-snapshot-issues"></a>Felsökning av problem med VM ögonblicksbild
Säkerhetskopiering är beroende av utfärdande ögonblicksbild kommandon för underliggande lagringsutrymmet. Att ha åtkomst till lagring eller fördröjningar i en ögonblicksbild för körning av aktiviteten inte kan orsaka säkerhetskopieringsjobb misslyckas. Följande kan orsaka ögonblicksbild aktivitet, fel.

1. Nätverksåtkomst till lagring blockeras med hjälp av NSG<br>
    Lär dig mer om hur du [aktivera nätverksåtkomst](backup-azure-vms-prepare.md#network-connectivity) lagring med hjälp av antingen Vitlistning av IP-adresser eller via en proxyserver.
2. Virtuella datorer med Sql Server säkerhetskopiering har konfigurerats kan orsaka ögonblicksbild uppgiften fördröjning <br>
   Standard VM säkerhetskopieringsproblem VSS fullständig säkerhetskopiering på virtuella Windows-datorer. Detta kan orsaka försening ögonblicksbild körning på virtuella datorer som kör Sql-servrar och om säkerhetskopiering av Sql Server är konfigurerad. Ange följande registernyckel om det uppstår fel vid säkerhetskopiering på grund av problem med ögonblicksbilder.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. VM-statusen Rapportera felaktigt eftersom VM stängs av i RDP.  <br>
   Om du har stängt den virtuella datorn i RDP Kontrollera tillbaka i portalen att VM-statusen avspeglas korrekt. Om inte, Stäng VM-portalen med alternativet ”avslutning' i VM-instrumentpanelen.
4. Om fler än fyra VMs delar samma tjänst i molnet, kan du konfigurera flera principer för säkerhetskopiering för att mellanlagra säkerhetskopiering tiderna så inga fler än fyra VM-säkerhetskopieringar startas samtidigt. Försök att sprida säkerhetskopiering starttider timmen mellanrum mellan principer.
5. Virtuell dator körs på hög CPU/minne.<br>
   Om den virtuella datorn körs på hög CPU usage(>90%) eller minne, ögonblicksbilden i kö, fördröjd och kommer så småningom hämtar timeout. Försök säkerhetskopiering på begäran i sådana situationer.

<br>

## <a name="networking"></a>Nätverk
Tillägg för säkerhetskopiering behöver åtkomst till det offentliga internet ska fungera som alla tillägg. Inte har åtkomst till internet kan visa sig på olika sätt:

* Tillägget installationen kan misslyckas
* Säkerhetskopieringsåtgärder (t.ex. disk snapshot) kan misslyckas
* Visa status för säkerhetskopieringen kan misslyckas

Behovet av att lösa offentliga internet-adresser har tagits Ledad [här](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Du måste kontrollera DNS-konfigurationer för VNET och kontrollera att Azure-URI: er kan lösas.

När namnmatchningen utförs korrekt måste åtkomst till Azure IP-adresser också anges. Om du vill avblockera åtkomst till Azure-infrastrukturen, gör du något av följande:

1. Godkända Azure-datacentret IP-intervall.
   * Hämta en lista över [Azure-datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) vara godkända.
   * Avblockera en IP-adresser med hjälp av den [ny NetRoute](https://technet.microsoft.com/library/hh826148.aspx) cmdlet. Kör denna cmdlet i Azure-dator i ett upphöjt PowerShell-fönster (Kör som administratör).
   * Lägg till regler NSG: N (om du har en plats) för att tillåta åtkomst till IP-adresser.
2. Skapa en sökväg för HTTP-trafik
   * Om du har några nätverksbegränsning på plats (en Nätverkssäkerhetsgrupp, till exempel) att distribuera en HTTP-proxyserver för att dirigera trafiken. Steg för att distribuera en HTTP-proxyserver kan hitta [här](backup-azure-vms-prepare.md#network-connectivity).
   * Lägg till regler NSG: N (om du har en plats) för att tillåta åtkomst till INTERNET från HTTP-Proxy.

> [!NOTE]
> DHCP måste vara aktiverat på gästen för IaaS säkerhetskopiering ska fungera.  Om du behöver en statisk privat IP-adress kan konfigurera du det via plattformen. DHCP-alternativet inuti den virtuella datorn ska aktiveras vänster.
> Visa mer information om [ange en statisk IP för interna privata](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
