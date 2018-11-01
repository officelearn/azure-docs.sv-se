---
title: Felsöka säkerhetskopiering med Azure-dator
description: Felsöka säkerhetskopiering och återställning av virtuella Azure-datorer
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: 8ef8241e9f0f6223b29fa29f7a5803f57f4d6203
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415006"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Felsöka säkerhetskopiering av virtuell Azure-dator
Du kan felsöka fel vid användning av Azure Backup med information som anges i tabellen nedan.

| Felinformation | Lösning |
| --- | --- |
| Det gick inte att utföra åtgärden eftersom den virtuella datorn inte finns längre. -Stoppa skydd av virtuell dator utan att ta bort säkerhetskopierade data. Mer information finns på http://go.microsoft.com/fwlink/?LinkId=808124 |Detta händer när den primära virtuella datorn tas bort, men säkerhetskopieringspolicyn fortsätter letar du efter en virtuell dator som säkerhetskopieras. Åtgärda det här felet: <ol><li> Återskapa den virtuella datorn med samma namn och samma resursgruppnamn [cloud service name]<br>(OR)</li><li> Sluta skydda virtuella datorer med eller utan att ta bort säkerhetskopierade data. [Mer information](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Ögonblicksbildsåtgärden misslyckades eftersom ingen nätverksanslutning på den virtuella datorn – se till att den virtuella datorn har nätverksåtkomst. Att det lyckas, antingen godkänd Azure datacenter IP-intervall eller konfigurera en proxyserver för nätverksåtkomst. Mer information finns i http://go.microsoft.com/fwlink/?LinkId=800034. Om du redan använder proxyserver, se till att proxyserverinställningarna är rätt konfigurerade | Inträffar när du neka utgående internet-anslutning på den virtuella datorn. Tillägget för ögonblicksbild av virtuell dator kräver Internetanslutning för att ta en ögonblicksbild av underliggande diskar. [Se avsnittet på att åtgärda ögonblicksbild fel som beror på blockerade nätverksåtkomst](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine). |
| VM-agenten kan inte kommunicera med Azure Backup-tjänsten. – Se till att den virtuella datorn är ansluten till nätverket och VM-agenten är senaste och körs. Mer information finns i artikeln http://go.microsoft.com/fwlink/?LinkId=800034. |Det här felet returneras om det uppstår ett problem med VM-agenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. [Läs mer](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup) ögonblicksbild problem om hur du felsöker virtuell dator.<br> Om den Virtuella datoragenten inte orsakar problem kan du starta om den virtuella datorn. Ett felaktigt tillstånd för virtuell dator kan orsaka problem och starta om den virtuella datorn återställer tillståndet. |
| Virtuell dator är i felläge för etablering – starta om den virtuella datorn och kontrollera att Virtuellt datorn körs eller stänga av. | Det här felet uppstår när någon av tillägg fel leder tillstånd för virtuell dator vara etableringsstatusen misslyckades. Gå till listan över tillägg och se om det finns ett tillägg för misslyckade, ta bort den och försök att starta om den virtuella datorn. Om alla tillägg är körs för närvarande kan du kontrollera om VM-agenttjänsten körs. Annars kan du starta om tjänsten VM agent. |
| Vmsnapshot-tillägget misslyckades för managed disks – försök säkerhetskopieringen igen. Om problemet kvarstår, följer du anvisningarna i ”http://go.microsoft.com/fwlink/?LinkId=800034”. Kontakta Microsoft-supporten om problemet fortfarande kvarstår | Det här felet uppstår när Backup-tjänsten inte kan utlösa en ögonblicksbild. [Läs mer](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtentionOperationFailed-vmsnapshot-extension-operation-failed) ögonblicksbild problem om hur du felsöker VM. |
| Det gick inte att kopiera ögonblicksbilden av den virtuella datorn, på grund av brist på utrymme i lagringskontot, se till att lagringskontot har tillräckligt med utrymme för data som finns på premium-lagringsdiskar som är kopplade till den virtuella datorn | När det gäller virtuella datorer i premium på säkerhetskopieringsstack för virtuell dator V1 kopiera vi ögonblicksbilden till storage-konto. Detta är att se till att av trafik, som fungerar på ögonblicksbild men inte begränsar antalet IOPS som är tillgängliga för programmet med premium-diskar. Microsoft rekommenderar att du endast allokera 50% (17,5 TB) för ett konto för det totala utrymmet så Azure Backup-tjänsten kan kopiera ögonblicksbilden till storage-konto och överför data från den här kopierade platsen i storage-konto till valvet. | 
| Det går inte att utföra åtgärden eftersom den Virtuella datoragenten inte svarar |Det här felet returneras om det uppstår ett problem med VM-agenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. Kontrollera Tjänststatus för VM-agenten i tjänster och om agenten ska visas i program på Kontrollpanelen för Windows-datorer. Försök att ta bort programmet från kontrollen panelen och installera om agenten som vi redan nämnt [nedan](#vm-agent). Utlös en ad hoc-säkerhetskopiering för att kontrollera efter att ha installerat agenten. |
| Recovery services-tillägg-åtgärden misslyckades. – Kontrollera att den senaste VM-agenten finns på den virtuella datorn och agent-tjänsten körs. Försök att säkerhetskopiera igen. Om säkerhetskopieringen misslyckas, kan du kontakta Microsoft support. |Det här felet returneras när VM-agenten är inaktuell. Se ”uppdaterar VM-Agent” avsnittet nedan för att uppdatera VM-agenten. |
| Virtuell dator finns inte. – Kontrollera att den virtuella datorn finns eller välj en annan virtuell dator. |Inträffar när den primära virtuella datorn tas bort, men säkerhetskopieringspolicyn fortsätter att leta efter en virtuell dator som säkerhetskopieras. Åtgärda det här felet: <ol><li> Återskapa den virtuella datorn med samma namn och samma resursgruppnamn [cloud service name]<br>(OR)<br></li><li>Sluta skydda den virtuella datorn utan att ta bort säkerhetskopierade data. [Mer information](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Det gick inte att köra. -En annan åtgärd pågår på det här objektet. Vänta tills den föregående åtgärden har slutförts och försök sedan igen. |Ett befintligt säkerhetskopieringsjobb körs och ett nytt jobb kan inte startas förrän det aktuella jobbet har slutförts. |
| Kopiera virtuella hårddiskar från Recovery Services vault timeout - försök igen om några minuter. Kontakta Microsoft-supporten om problemet kvarstår. | Inträffar om det finns ett tillfälligt fel på lagringssidan, eller om Backup-tjänsten inte har fått tillräckligt med storage-konto IOPS för att överföra data till valvet, inom den angivna tiden. Se till att följa den [bästa praxis när du konfigurerar dina virtuella datorer](backup-azure-vms-introduction.md#best-practices). Flytta den virtuella datorn till ett annat lagringskonto som inte lästs in och försök säkerhetskopieringen igen.|
| Säkerhetskopieringen misslyckades med ett internt fel – försök igen om några minuter. Om problemet kvarstår kontaktar du Microsoft Support |Du kan ta emot det här felet av två skäl: <ol><li> Det finns ett övergående problem vid VM-lagring. Kontrollera den [Azure statusen](https://azure.microsoft.com/status/) att se om det finns problem för beräkning, lagring och nätverk i regionen. När problemet är löst kan försöka att säkerhetskopiera igen. <li>Den ursprungliga virtuella datorn har tagits bort och återställningspunkten kan inte utföras. Hela säkerhetskopierade data för en borttagen virtuell dator, men ta bort backup fel: ta bort skyddet från den virtuella datorn och välja alternativet för att behålla data. Den här åtgärden stoppar schemalagda säkerhetskopieringsjobbet och återkommande felmeddelanden. |
| Det gick inte att installera Azure Recovery Services-tillägget på det valda objektet - är agenten för den virtuella datorn en förutsättning för Azure Recovery Services-tillägget. Installera Azure VM-agenten och starta om registreringsåtgärden |<ol> <li>Kontrollera om VM-agenten har installerats korrekt. <li>Se till att flaggan på VM-konfigurationen är korrekt.</ol> [Läs mer](#validating-vm-agent-installation) om hur du installerar VM-agenten och hur du verifierar installationen av VM-agent. |
| Tilläggsinstallationen misslyckades med felet ”COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator |Detta innebär vanligen att COM +-tjänsten inte körs. Kontakta Microsoft support för hjälp med att åtgärda problemet. |
| Ögonblicksbildsåtgärden misslyckades med VSS-åtgärdsfelet ”den här enheten är låst av BitLocker-diskkryptering. Du måste låsa upp den här enheten från Kontrollpanelen. |Inaktivera BitLocker för alla enheter på den virtuella datorn och notera om VSS-problemet har lösts |
| Virtuell dator är inte i ett tillstånd som tillåter säkerhetskopieringar. |<ul><li>Om den virtuella datorn är i ett tillfälligt tillstånd mellan **kör** och **stänga**, vänta tills tillstånd för att och sedan utlösa säkerhetskopieringsjobbet. <li> Om den virtuella datorn är en Linux-VM och använder Security Enhanced Linux-kernel-modul, utesluta sökvägen Linux-agenten (_/var/lib/waagent_) från säkerhetsprincip och se till att säkerhetskopieringstillägget installeras.  |
| Azure virtuell dator hittades inte. |Den här fel uppstår när den primära virtuella datorn tas bort, men säkerhetskopieringspolicyn fortsätter letar du efter den borttagna virtuella datorn. Åtgärda det här felet: <ol><li>Återskapa den virtuella datorn med samma namn och samma resursgruppnamn [cloud service name] <br>(OR) <li> Inaktivera skyddet för den här virtuella datorn så inte skapas säkerhetskopieringsjobben. </ol> |
| VM-agenten finns inte på den virtuella datorn – installera eventuella nödvändiga komponenter och VM-agenten och starta sedan om åtgärden. |[Läs mer](#vm-agent) VM agentinstallation och hur du verifierar installationen av VM-agent. |
| Ögonblicksbildsåtgärden misslyckades på grund av VSS-skrivarna är i felaktigt tillstånd |Du måste starta om VSS (Volume Shadow copy Service)-skrivare som är i felaktigt tillstånd. Om du vill göra detta, från en upphöjd kommandotolk, kör ```vssadmin list writers```. Utdata innehåller alla VSS-skrivare och deras tillstånd. Starta om VSS-skrivaren genom att köra följande kommandon från en upphöjd kommandotolk för varje VSS-skrivaren vars tillstånd är inte ”[1] Stable”:<br> ```net stop serviceName``` <br> ```net start serviceName```|
| Ögonblicksbildsåtgärden misslyckades på grund av parsning av konfigurationen |Detta inträffar på grund av ändrade behörigheter för MachineKeys-katalogen: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Kör kommandot nedan och kontrollera att behörigheterna för MachineKeys directory är standard-som:<br>_Icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Standardbehörigheterna är:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Om behörigheter på MachineKeys directory annorlunda än standard, följer du stegen nedan för att rätt behörighet, ta bort certifikatet och utlösa säkerhetskopieringen.<ol><li>Åtgärda behörighet på MachineKeys directory.<br>Använda Explorer säkerhetsegenskaper och avancerade säkerhetsinställningar för katalogen kan återställa behörigheter till standardvärdena. Ta bort alla användarobjekt (utom standard) från katalogen och kontrollera att den **alla** behörigheten har särskilt åtkomst för:<br>-Lista mappar / läsa data <br>-Läsa attribut <br>-Läsa utökade attribut <br>– Skapa filer / skriva data <br>-Skapa mappar / lägga till data<br>-Skriva attribut<br>-Skriva utökade attribut<br>-Läsa behörigheter<br><br><li>Ta bort alla certifikat där **utfärdat till** är den klassiska distributionsmodellen eller **Windows Azure CRP Certificate Generator**.<ul><li>[Öppna konsolen certifikat (lokal dator)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Under **personliga** > **certifikat**, ta bort alla certifikat där **utfärdat till** är den klassiska distributionsmodellen eller **CRP: N för Windows Azure Certificate Generator**.</ul><li>Utlös en VM-säkerhetskopiering. </ol>|
| Azure Backup-tjänsten har inte tillräcklig behörighet till Key Vault för säkerhetskopiering krypterade virtuella datorer. |Backup-tjänsten ska tillhandahållas dessa behörigheter i PowerShell med hjälp av anvisningarna i **Aktivera säkerhetskopiering** delen av [PowerShell-dokumentationen](backup-azure-vms-automation.md). |
|Installation av ögonblicksbild misslyckades med felet tillägg – COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator | Från en upphöjd kommandotolk, starta Windows-tjänsten ”COM + System Application”, till exempel _net start COMSysApp_. <br>Om tjänsten inte startar sedan:<ol><li> Kontrollera loggen på grund av tjänsten ”Distributed Transaction Coordinator” är ”Nätverkstjänst”. Om du inte ändrar du inloggningskonton till ”nätverkstjänst”, starta om tjänsten och försök sedan att starta ”COM + System Application” ”.<li>Om ”COM + System Application inte starta, Använd följande steg för att avinstallera/installera tjänsten” Distributed Transaction Coordinator ”:<br> -Stoppa MSDTC-tjänsten<br> – Öppna en kommandotolk (cmd) <br> -Köra kommandot ```msdtc -uninstall``` <br> -Köra kommandot ```msdtc -install``` <br> -Starta MSDTC-tjänsten<li>Starta windows-tjänsten COM + System Application. En gång ”COM + System Application startar, utlösa ett säkerhetskopieringsjobb från Azure-portalen.</ol> |
|  Ögonblicksbildsåtgärden misslyckades på grund av COM +-fel | Den rekommenderade åtgärden är att starta om windows-tjänst ”COM + System Application” (från en upphöjd kommandotolk - _net start COMSysApp_). Om problemet kvarstår startar du om den virtuella datorn. Om du startar om den virtuella datorn inte hjälper så, försök [tar bort tillägget VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) och Utlös säkerhetskopieringen manuellt. |
| Det gick inte att frysa en eller flera monteringspunkter på den virtuella datorn ska använda en konsekvent ögonblicksbild för filsystemet | Använd följande steg: <ol><li>Kontrollera tillståndet för filsystemet för alla monterade enheter med hjälp av _'tune2fs'_ kommando.<br> T.ex.: tune2fs -l/dev/sdb1 \| grep ”filsystem state” <li>Demontera enheterna för vilka filsystem tillstånd inte är ren med _'umount'_ kommando <li> Köra FileSystemConsistency kontroll på dessa enheter med hjälp av _'fsck'_ kommando <li> Montera enheterna igen och försök säkerhetskopieringen.</ol> |
| Ögonblicksbildsåtgärden misslyckades på grund av att skapa en säker nätverkskommunikationskanal | <ol><Li> Öppna Registereditorn genom att köra regedit.exe i en förhöjd behörighet. <li> Identifiera alla versioner av .NET Framework finns i systemet. De finns under hierarkin för registernyckeln ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft” <li> För varje .net Framework finns i registernyckeln, lägga till följande nyckel: <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Ögonblicksbildsåtgärden misslyckades på grund av fel i installationen av Visual C++ Redistributable för Visual Studio 2012 | Gå till C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion och installera vcredist2012_x64. Kontrollera att värdet för registernyckeln för att tillåta installationen av den här anges till rätt värde dvs. värdet för registernyckeln _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ är inställd på 3 och 4 inte. Om du ändå får problem med installationen, startar du om installationen genom att köra _MSIEXEC /UNREGISTER_ följt av _MSIEXEC /REGISTER_ från en upphöjd kommandotolk.  |


## <a name="jobs"></a>Jobb
| Felinformation | Lösning |
| --- | --- |
| Annulleringen finns inte stöd för den här jobbtypen: vänta tills jobbet har slutförts. |Ingen |
| Jobbet är inte i tillståndet avbrytbar – vänta så tills jobbet har slutförts. <br>ELLER<br> Det valda jobbet är inte i tillståndet avbrytbar – vänta tills jobbet är klart. |Jobbet är nästan klar med största sannolikhet. Vänta tills jobbet har slutförts.|
| Det går inte att avbryta jobbet eftersom det pågår inte – annullering stöds bara för jobb som pågår. Försök avbryter pågående jobb. |Detta inträffar på grund av ett övergående tillstånd. Vänta en stund och försök åtgärden Avbryt. |
| Det gick inte att avbryta jobbet - vänta tills jobbet är klart. |Ingen |

## <a name="restore"></a>Återställ
| Felinformation | Lösning |
| --- | --- |
| Återställningen misslyckades med molnet internt fel |<ol><li>Molntjänst som du försöker återställa har konfigurerats med DNS-inställningarna. Du kan kontrollera <br>$deployment = get-AzureDeployment - ServiceName ”ServiceName”-fack ”produktion” Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Om det finns konfigurerade-adress, innebär det att DNS-inställningarna är konfigurerade.<br> <li>Molntjänst som du försöker återställa som ska konfigureras med ReservedIP och befintliga virtuella datorer i molntjänst är i stoppat läge.<br>Du kan kontrollera en molnbaserad tjänst har reserverat IP genom att använda följande powershell-cmdlets:<br>$deployment = get-AzureDeployment - ServiceName ”servicename”-fack ”produktion” $dep. ReservedIPName <br><li>Du försöker återställa en virtuell dator med följande särskilda nätverkskonfigurationer i till samma molntjänst. <br>-Virtuella datorer under konfigurationen för belastningsutjämnaren (internt och externt)<br>-Virtuella datorer med flera reserverade IP-adresser<br>-Virtuella datorer med flera nätverkskort<br>Välj en ny molntjänst i Användargränssnittet eller se [återställa överväganden](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) för virtuella datorer med särskilda nätverkskonfigurationer.</ol> |
| Det valda DNS-namnet används redan – ange ett annat DNS-namn och försök igen. |DNS-namnet här refererar till molntjänstens namn (vanligtvis slutar med. cloudapp.net). Det här måste vara unikt. Om du får det här felet kan behöva du välja ett annat namn under återställningen. <br><br> Det här felet visas endast för användare av Azure-portalen. Återställningsåtgärden via PowerShell lyckas eftersom den endast återställer diskarna och inte skapa den virtuella datorn. Felet kommer riktas när den virtuella datorn uttryckligen har skapats av dig när disken återställningsåtgärden. |
| Den angivna virtuella nätverkskonfigurationen stämmer inte – ange en annan virtuell nätverkskonfiguration och försök igen. |Ingen |
| Den angivna Molntjänsten använder en reserverad IP-adress som inte matchar konfigurationen för den virtuella datorn håller på att återställas – ange en annan molntjänst som inte använder en reserverad IP-adress, eller välj en annan återställningspunkt att återställa från. |Ingen |
| Molntjänsten har nått gränsen för antalet indataslutpunkter - försök igen genom att ange en annan molntjänst eller genom att använda en befintlig slutpunkt. |Ingen |
| Recovery Services-valvet och målresurserna storage-konto finns i två olika regioner - Kontrollera att lagringen konto angetts i återställningsåtgärden, finns i samma Azure-region som Recovery Services-valvet. |Ingen |
| Storage-konto som angetts för återställningsåtgärden inte är stöd för – endast Basic/Standard storage-konton med lokalt redundant eller geo redundant replikeringsinställningar stöds. Välj ett lagringskonto |Ingen |
| Typ av Lagringskonto som angetts för återställningsåtgärden är inte online – Kontrollera att lagringskontot som angetts i återställningsåtgärden är online |Detta kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagringskonto. |
| Resursgrupp kvot har nåtts – ta bort några resursgrupper från Azure-portalen eller kontakta Azure-supporten om du vill höja gränsen. |Ingen |
| Det valda undernätet finns inte – Välj ett undernät som finns |Ingen |
| Säkerhetskopieringstjänsten har inte åtkomstbehörighet till resurser i din prenumeration. |Att lösa problemet, första återställa diskar med hjälp av anvisningarna i avsnittet **återställning säkerhetskopierade diskar** i [välja VM Återställ konfigurationen](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Använd PowerShell-åtgärderna som nämns i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) att skapa fullständig virtuell dator från återställda diskar. |

## <a name="backup-or-restore-taking-time"></a>Säkerhetskopieringen eller återställningen tar tid
Om du ser din backup(>12 hours) eller återställningen tar time(>6 hours):
* Förstå [faktorer som påverkar säkerhetskopieringstiden](backup-azure-vms-introduction.md#total-vm-backup-time) och [faktorer som påverkar återställningstiden tid](backup-azure-vms-introduction.md#total-restore-time).
* Se till att du följer [säkerhetskopiera metodtips](backup-azure-vms-introduction.md#best-practices).

## <a name="vm-agent"></a>VM-agenten
### <a name="setting-up-the-vm-agent"></a>Konfigurera VM-agenten
Vanligtvis finns VM-agenten redan i virtuella datorer som skapas från Azure-galleriet. Virtuella datorer som har migrerats från lokala Datacenter behöver dock inte VM-agenten installerad. VM-agenten måste installeras uttryckligen för sådana virtuella datorer.

För Windows-datorer:

* Ladda ned och installera [agentens MSI-fil](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
* För klassiska virtuella datorer, [uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) att indikera att agenten är installerad. Det här steget krävs inte för Resource Manager-datorer.

För virtuella Linux-datorer:

* Installera den senaste versionen av agenten från lagringsplatsen för distribution. Mer information om paketnamnet finns den [Linux-agenten databasen](https://github.com/Azure/WALinuxAgent).
* För klassiska virtuella datorer, [använder i blogginlägget för att uppdatera VM-egenskapen](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx), och verifiera agenten är installerad. Det här steget krävs inte för Resource Manager-datorer.

### <a name="updating-the-vm-agent"></a>Uppdatera VM-agenten
För Windows-datorer:

* Uppdatera VM-agenten genom att installera om den [binärfilerna för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Innan du uppdaterar agenten, kontrollera att ingen säkerhetskopiering utförs under uppdateringen av VM-agenten.

För virtuella Linux-datorer:

* Följ instruktionerna i artikeln om du vill uppdatera VM-agenten för Linux [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
**Du bör alltid använda distribution-databasen för att uppdatera agenten**. Hämta inte agenten kod från GitHub. Om den senaste agenten inte är tillgängligt för din distribution, kan du nå ut till stöd för distribution för anvisningar om att hämta den senaste agenten. Du kan också kontrollera senast [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) information i GitHub-lagringsplatsen.

### <a name="validating-vm-agent-installation"></a>Verifiera installationen av VM-agenten

Att kontrollera versionen för VM-agenten på Windows virtuella datorer:

1. Logga in på Azure-dator och gå till mappen *C:\WindowsAzure\Packages*. Du bör hitta filen WaAppAgent.exe.
2. Högerklicka på filen, gå till **Egenskaper** och välj fliken **Information**. Fältet produktversion ska vara 2.6.1198.718 eller högre

## <a name="troubleshoot-vm-snapshot-issues"></a>Felsöka problem med VM-ögonblicksbild
Säkerhetskopiering av virtuella datorer är beroende av utfärda kommandon för ögonblicksbilden till underliggande lagring. Inte ha åtkomst till lagring eller fördröjningar i en ögonblicksbild för körning av aktiviteten kan orsaka säkerhetskopieringsjobb misslyckas. Följande kan orsaka fel uppstår i ögonblicksbilden uppgiften.

1. Nätverksåtkomst till lagring är blockerad använder NSG-regler<br>
    Läs mer om hur du [aktivera nätverksåtkomst](backup-azure-arm-vms-prepare.md#establish-network-connectivity) till Storage med hjälp av antingen listan över tillåtna program med IP-adresser eller via en proxyserver.
2. Virtuella datorer med Sql Server säkerhetskopiering har konfigurerats kan orsaka fördröjning för uppgift för ögonblicksbild <br>
   Som standard skapar säkerhetskopiering en VSS fullständig säkerhetskopiering på virtuella Windows-datorer. Virtuella datorer som kör SQL Server, med SQL Server säkerhetskopiering konfigurerad, kan fördröjningar av ögonblicksbild. Om ögonblicksbild fördröjningar leda till Säkerhetskopieringsfel, anger du följande registernyckel.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

3. VM-status rapporteras felaktigt eftersom Virtuella datorn stängs av i RDP.  <br>
   Om du använde fjärrskrivbordet för att stänga av den virtuella datorn kan du kontrollera att virtuella datorns status på portalen är korrekt. Om statusen inte är korrekt, använder du den **avstängning** alternativet i VM-instrumentpanelen i portalen för att stänga av den virtuella datorn.
4. Om fler än fyra virtuella datorer delar samma molntjänst kan du sprida de virtuella datorerna över flera principer för säkerhetskopiering. Skicka säkerhetskopieringstider så inga fler än fyra VM-säkerhetskopior börjar på samma gång. Försök att avgränsa starttider i principerna med minst en timme.
5. Virtuella datorn körs på hög processor/minne.<br>
   Om den virtuella datorn körs på extra minne eller CPU usage(>90%), ögonblicksbild uppgiften är i kö, fördröjd och så småningom timeout. Om det händer kan du prova en säkerhetskopiering på begäran.

<br>

## <a name="networking"></a>Nätverk
Som alla tillägg behöver säkerhetskopiera anknytning har åtkomst till det offentliga internet ska fungera. Inte ha åtkomst till internet kan visa sig på olika sätt:

* Installationen av tillägget kan misslyckas
* Säkerhetskopieringsåtgärder (till exempel ögonblicksbild) kan misslyckas
* Visa status för säkerhetskopieringen kan misslyckas

Behöver för att lösa offentliga internet-adresser har har uppvisat [här](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Du måste kontrollera DNS-konfigurationen för det virtuella nätverket och se till att URI: er för Azure kan lösas.

När namnmatchningen utförs korrekt måste åtkomst till Azure IP-adresser också anges. Om du vill avblockera åtkomst till Azure-infrastrukturen, gör du något av följande:

1. Listan över godkända Azure-datacenter IP-intervall.
   * Hämta listan över [Azure datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) tillåtas.
   * Avblockera en IP-adresser med hjälp av den [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) cmdlet. Kör denna cmdlet i Azure-dator, i en upphöjd PowerShell-fönster (Kör som administratör).
   * Lägga till regler i NSG (om du har en på plats) för att tillåta åtkomst till den IP-adresser.
2. Skapa en sökväg för HTTP-trafik
   * Om du har några nätverksbegränsning på plats (en Nätverkssäkerhetsgrupp, till exempel) att distribuera en HTTP-proxyserver dirigerar trafik. Steg för att distribuera en HTTP-proxyserver kan hitta [här](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   * Lägga till regler i NSG (om du har en på plats) för att tillåta åtkomst till INTERNET från HTTP-Proxy.

> [!NOTE]
> DHCP måste vara aktiverat på gästen för säkerhetskopiering av Virtuella IaaS ska fungera.  Om du behöver en statisk privat IP-adress kan konfigurera du den med plattformen. DHCP-alternativ i den virtuella datorn måste vara aktiverat till vänster.
> Visa mer information om [ställa in en statisk IP för interna privata](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
