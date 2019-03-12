---
title: Felsöka säkerhetskopiering med Azure virtual machines
description: Felsöka säkerhetskopiering och återställning av virtuella Azure-datorer
services: backup
author: srinathv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: srinathv
ms.openlocfilehash: f79a9048e50901424330224066cb84929d9126dc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530934"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Felsöka säkerhetskopiering av virtuell Azure-dator
Du kan felsöka fel vid användning av Azure Backup med informationen som visas i följande tabell:

| Felinformation | Lösning |
| ------ | --- |
| Backup kunde inte utföra åtgärden eftersom den virtuella datorn (VM) finns inte längre: <br>Sluta skydda den virtuella datorn utan att ta bort säkerhetskopierade data. Mer information finns i [sluta skydda virtuella datorer](https://go.microsoft.com/fwlink/?LinkId=808124). |Det här felet inträffar när den primära virtuella datorn tas bort, men fortfarande verkar principen för säkerhetskopiering för en virtuell dator att säkerhetskopiera. Om du vill åtgärda det här felet, gör du följande: <ol><li> Återskapa den virtuella datorn med samma namn och samma resursgruppnamn **molntjänstnamnet**,<br>**eller**</li><li> Sluta skydda den virtuella datorn med eller utan att ta bort säkerhetskopierade data. Mer information finns i [sluta skydda virtuella datorer](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| Azure VM-agenten (VM-Agent) inte kan kommunicera med Azure Backup-tjänsten: <br>Kontrollera att den virtuella datorn är ansluten till nätverket och VM-agenten är senast och körs. Mer information finns i [felsöka Azure Backup-fel: Problem med agenten eller tillägget](https://go.microsoft.com/fwlink/?LinkId=800034). |Felet kan inträffa om det uppstår ett problem med VM-agenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. Läs mer om [felsökning VM ögonblicksbild problem](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup). <br><br>Om den Virtuella Datoragenten inte orsakar problem kan du starta om den virtuella datorn. Ett felaktigt tillstånd för virtuell dator kan orsaka problem och starta om den virtuella datorn återställer tillståndet. |
| Den virtuella datorn är i felläge för etablering: <br>Starta om den virtuella datorn och kontrollera att Virtuellt datorn körs eller stänga av. | Det här felet uppstår när någon av datortillägg placerar den virtuella datorn i misslyckade Etableringsstatus. Gå till listan över tillägg, kontrollera om det finns ett tillägg för misslyckade, ta bort den och försök att starta om den virtuella datorn. Om alla tillägg är körs för närvarande kan du kontrollera om VM-agenttjänsten körs. Annars kan du starta om tjänsten VM-agenten. |
| Backup kunde inte kopiera ögonblicksbilden av den virtuella datorn på grund av brist på utrymme i lagringskontot: <br>Kontrollera att lagringskontot har ledigt utrymme som är lika med data som finns på premium-lagringsdiskar som är kopplade till den virtuella datorn. | För virtuella datorer i premium på säkerhetskopieringsstack för virtuell dator V1 kopierar vi ögonblicksbilden till lagringskontot. Det här steget säkerställer att av trafik, som fungerar på ögonblicksbilden men inte begränsar antalet IOPS som är tillgängliga för programmet med premium-diskar. <br><br>Vi rekommenderar att du allokerar endast 50 procent, 17,5 TB av det totala utrymmet för kontot. Azure Backup-tjänsten kan sedan kopiera ögonblicksbilden till storage-konto och överför data från den här kopierade platsen i storage-konto till valvet. |
| Backup kan inte utföra åtgärden eftersom den Virtuella Datoragenten inte svarar. |Felet kan inträffa om det uppstår ett problem med VM-agenten eller nätverksåtkomst till Azure-infrastrukturen är blockerad på något sätt. Kontrollera status för VM-Agent-tjänsten i tjänster och om agenten visas i program på Kontrollpanelen för Windows-datorer. <br><br>Försök ta bort programmet från Kontrollpanelen och installera agenten enligt beskrivningen i [VM-agenten](#vm-agent). Utlös en ad hoc-säkerhetskopiering för att kontrollera att den när du installera om agenten. |
| Recovery services-tilläggsåtgärden misslyckades: <br>Kontrollera att den senaste VM-agenten finns på den virtuella datorn och VM-agenttjänsten körs. Försök att utföra säkerhetskopieringsåtgärden igen. Om säkerhetskopieringen misslyckas, kontaktar du Microsoft Support. |Det här felet inträffar när VM-agenten är inaktuell. Avse felsöka Azure säkerhetskopiering av virtuella datorer att uppdatera VM-agenten. |
| Den virtuella datorn finns inte: <br>Kontrollera att den virtuella datorn finns eller välj en annan virtuell dator. |Det här felet uppstår när den primära virtuella datorn tas bort, men fortfarande verkar principen för säkerhetskopiering för en virtuell dator att säkerhetskopiera. Om du vill åtgärda det här felet, gör du följande: <ol><li> Återskapa den virtuella datorn med samma namn och samma resursgruppnamn **molntjänstnamnet**,<br>**eller**<br></li><li>Sluta skydda den virtuella datorn utan att ta bort säkerhetskopierade data. Mer information finns i [sluta skydda virtuella datorer](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| Kommandot körningen misslyckades: <br>En annan åtgärd pågår på det här objektet. Vänta tills den föregående åtgärden har slutförts. Försök sedan igen. |Ett befintligt säkerhetskopieringsjobb körs och ett nytt jobb kan inte startas förrän det aktuella jobbet har slutförts. |
| Kopiera virtuella hårddiskar från Recovery Services-valvet timeout: <br>Försök igen om några minuter. Kontakta Microsoft-supporten om problemet kvarstår. | Det här felet uppstår om det finns ett tillfälligt fel på lagringssidan, eller om Backup-tjänsten inte har fått tillräckligt med storage-konto IOPS för att överföra data till valvet, inom den angivna tiden. Se till att följa den [bästa praxis när du konfigurerar dina virtuella datorer](backup-azure-vms-introduction.md#best-practices). Flytta den virtuella datorn till ett annat lagringskonto som inte lästs in och försök säkerhetskopieringen igen.|
| Säkerhetskopieringen misslyckades med ett internt fel: <br>Försök igen om några minuter. Kontakta Microsoft-supporten om problemet kvarstår. |Du får det här felet av två skäl: <ul><li> Det finns ett övergående problem vid VM-lagring. Kontrollera den [status för Azure site](https://azure.microsoft.com/status/) att se om det finns beräkning, lagring eller nätverksproblem i regionen. När problemet är löst, försöka att säkerhetskopiera igen. <li> Den ursprungliga virtuella datorn har tagits bort och återställningspunkten kan inte utföras. Om du vill behålla säkerhetskopierade data för en borttagen virtuell dator men ta bort backup felen, ta bort skyddet från den virtuella datorn och välja alternativet för att behålla data. Den här åtgärden stoppar schemalagda säkerhetskopieringsjobbet och återkommande felmeddelanden. |
| Säkerhetskopieringen misslyckades att installera Azure Recovery Services-tillägget på det markerade objektet: <br>VM-agenten är en förutsättning för Azure Recovery Services-tillägget. Installera Azure VM-agenten och starta om registreringsåtgärden. |<ol> <li>Kontrollera om VM-agenten har installerats korrekt. <li>Se till att flaggan på VM-konfigurationen är korrekt.</ol> Läs mer om hur du installerar VM-agenten och hur du validerar VM-agentinstallation. |
| Tilläggsinstallationen misslyckades med fel **COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator**. |Detta fel innebär vanligtvis att tjänsten COM + inte är igång. Kontakta Microsoft Support om du behöver hjälp med att åtgärda problemet. |
| Ögonblicksbildsåtgärden misslyckades med Volume Shadow Copy Service (VSS)-åtgärdsfel **den här enheten är låst av BitLocker-kryptering. Du måste låsa upp den här enheten från Kontrollpanelen.** |Inaktivera BitLocker för alla enheter på den virtuella datorn och kontrollera om VSS-problemet har lösts. |
| Den virtuella datorn inte är i ett tillstånd som tillåter säkerhetskopieringar. |<ul><li>Om den virtuella datorn är i ett tillfälligt tillstånd mellan **kör** och **stänga**, vänta på att ändra tillståndet. Sedan Utlös säkerhetskopieringsjobbet. <li> Om den virtuella datorn är en Linux-VM och använder Security-Enhanced Linux-kernel-modul, utesluta Azure Linux Agent sökvägen **/var/lib/waagent** från säkerhetsprincip och se till att säkerhetskopieringstillägget installeras.  |
| Det gick inte att hitta en Azure virtuell dator. |Det här felet uppstår när den primära virtuella datorn tas bort, men säkerhetskopieringspolicyn söker fortfarande efter den borttagna virtuella datorn. Åtgärda det här felet enligt följande: <ol><li>Återskapa den virtuella datorn med samma namn och samma resursgruppnamn **molntjänstnamnet**, <br>**eller** <li> Inaktivera skyddet för den här virtuella datorn, så inte kommer att skapa säkerhetskopieringsjobben. </ol> |
| VM-agenten inte finns på den virtuella datorn: <br>Installera eventuella nödvändiga komponenter och VM-agenten. Starta sedan om åtgärden. |Läs mer om [VM-agentinstallation och hur du validerar VM-agentinstallation](#vm-agent). |
| Ögonblicksbildsåtgärden misslyckades eftersom VSS-skrivare som fanns i ett felaktigt tillstånd. |Starta om VSS-skrivare som är i ett felaktigt tillstånd. Från en upphöjd kommandotolk kör ```vssadmin list writers```. Utdata innehåller alla VSS-skrivare och deras tillstånd. För varje VSS-skrivaren med ett tillstånd som inte är **[1] stabil**, för att starta om VSS-skrivaren genom att köra följande kommandon från en upphöjd kommandotolk: <ol><li>```net stop serviceName``` <li> ```net start serviceName```</ol>|
| Ögonblicksbildsåtgärden misslyckades på grund av ett fel som parsning av konfigurationen. |Det här felet inträffar på grund av ändrade behörigheter på den **MachineKeys** directory: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br> Kör följande kommando och kontrollera att behörigheterna för den **MachineKeys** directory är standard som:<br>**Icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br><br>Standardbehörigheterna är följande: <ul><li>Alla: (R, W) <li>Inbyggda administratörer: (F)</ul> Om du ser behörigheter i den **MachineKeys** katalog som skiljer sig från standardvärdena, Följ dessa steg för att rätt behörighet, ta bort certifikatet och utlösa säkerhetskopieringen: <ol><li>Åtgärda behörighet på den **MachineKeys** directory. Genom att använda Explorer säkerhetsegenskaper och avancerade säkerhetsinställningar i katalogen kan återställa behörigheter till standardvärdena. Ta bort alla användarobjekt förutom standardvärden från katalogen och kontrollera att den **alla** behörigheten har särskilt åtkomst på följande sätt: <ul><li>Lista mappar/läsa data <li>Läsa attribut <li>Läsa utökade attribut <li>Skapa filer/skriva data <li>Skapa mappar/lägga till data<li>Skriva attribut<li>Skriva utökade attribut<li>Läsbehörighet </ul><li>Ta bort alla certifikat där **utfärdat till** är den klassiska distributionsmodellen eller **Windows Azure CRP Certificate Generator**:<ol><li>[Öppna certifikat på en lokal dator konsol](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).<li>Under **personliga** > **certifikat**, ta bort alla certifikat där **utfärdat till** är den klassiska distributionsmodellen eller **CRP: N för Windows Azure Certificate Generator**.</ol> <li>Utlös en VM-säkerhetskopiering. </ol>|
| Azure Backup-tjänsten har inte tillräcklig behörighet till Azure Key Vault för säkerhetskopiering av krypterade virtuella datorer. |Ange dessa behörigheter i PowerShell för Backup-tjänsten med hjälp av stegen i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md). |
|Det gick inte att installera tillägget för ögonblicksbild med fel **COM + kunde inte kommunicera med Microsoft Distributed Transaction Coordinator**. | Starta tjänsten Windows från en upphöjd kommandotolk **COM + System Application**. Ett exempel är **net start COMSysApp**. Om tjänsten inte startar, gör du följande:<ol><li> Kontrollera att tjänsten inloggningskonto **Distributed Transaction Coordinator** är **nätverkstjänst**. Om den inte ändra kontot som **nätverkstjänst** och starta om tjänsten. Försök att starta **COM + System Application**.<li>Om **COM + System Application** inte starta, vidta följande steg för att avinstallera och installera tjänsten **Distributed Transaction Coordinator**: <ol><li>Stoppa MSDTC-tjänsten. <li>Öppna en kommandotolk **cmd**. <li>Kör kommandot ```msdtc -uninstall```. <li>Kör kommandot ```msdtc -install```. <li>Starta MSDTC-tjänsten. </ol> <li>Starta tjänsten Windows **COM + System Application**. Efter den **COM + System Application** startar, utlöser en säkerhetskopiering från Azure-portalen.</ol> |
|  Ögonblicksbildsåtgärden misslyckades på grund av ett COM +-fel. | Vi rekommenderar att du startar om tjänsten Windows **COM + System Application** från en upphöjd kommandotolk **net start COMSysApp**. Om problemet kvarstår startar du om den virtuella datorn. Om du startar om den virtuella datorn inte hjälper så, försök [tar bort tillägget VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout) och Utlös säkerhetskopieringen manuellt. |
| Säkerhetskopieringen misslyckades att frysa en eller flera monteringspunkter på den virtuella datorn ska använda en konsekvent ögonblicksbild för fil-system. | Vidta följande steg: <ul><li>Kontrollera filen systemtillståndet för alla monterade enheter med hjälp av den **'tune2fs'** kommando. Ett exempel är **tune2fs -l/dev/sdb1 \** .| GREP **filsystem**. <li>Demontera enheterna som filen systemtillståndet inte ren med hjälp av den **'umount'** kommando. <li> Kör en konsekvenskontroll för fil-system på dessa enheter med hjälp av den **'fsck'** kommando. <li> Montera enheterna igen och försök säkerhetskopieringen.</ol> |
| Ögonblicksbildsåtgärden misslyckades på grund av det gick inte att skapa en säker nätverkskommunikationskanal. | <ol><li> Öppna Registereditorn genom att köra **regedit.exe** en förhöjd behörighet. <li> Identifiera alla versioner av .NET Framework finns i systemet. De är närvarande under hierarkin för registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Lägg till följande nyckel för varje .NET Framework finns i registernyckeln: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| Ögonblicksbildsåtgärden misslyckades på grund av det gick inte att installera Visual C++ Redistributable för Visual Studio 2012. | Gå till C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion och installera vcredist2012_x64. Kontrollera att registernyckelvärdet som gör att installationen av den här anges till rätt värde. Det vill säga värdet för registernyckeln **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** är inställd på **3** och inte **4**. <br><br>Om du fortfarande har problem med installationen startar du om installationstjänsten genom att köra **MSIEXEC /UNREGISTER** följt av **MSIEXEC /REGISTER** från en upphöjd kommandotolk.  |


## <a name="jobs"></a>Jobb
| Felinformation | Lösning |
| --- | --- |
| Annulleringen stöds inte för den här jobbtypen: <br>Vänta tills jobbet har slutförts. |Ingen |
| Det finns inte i tillståndet avbrytbar jobbet: <br>Vänta tills jobbet har slutförts. <br>**eller**<br> Det valda jobbet finns inte i ett avbrytbar tillstånd: <br>Vänta tills jobbet är slutfört. |Det är troligt att jobbet är nästan klar. Vänta tills jobbet har slutförts.|
| Backup kan inte avbryta jobbet eftersom den inte håller på att: <br>Annulleringen stöds endast för pågående jobb. Försök att avbryta ett pågående jobb. |Det här felet inträffar på grund av ett övergående tillstånd. Vänta en minut och försök sedan åtgärden Avbryt. |
| Det gick inte att avbryta jobbet säkerhetskopiering: <br>Vänta tills jobbet har slutförts. |Ingen |

## <a name="restore"></a>Återställ

| Felinformation | Lösning |
| --- | --- |
| Återställningen misslyckades med ett internt fel i molnet. |<ol><li>Molntjänsten som du försöker återställa har konfigurerats med DNS-inställningarna. Du kan kontrollera: <br>**$deployment = get-AzureDeployment - ServiceName ”ServiceName”-fack ”produktion” Get-AzureDns - DnsSettings $deployment. DnsSettings**.<br>Om **adress** konfigureras, och sedan DNS-inställningarna har konfigurerats.<br> <li>Molntjänsten som du försöker återställa har konfigurerats med **ReservedIP**, och befintliga virtuella datorer i Molntjänsten som är i ett stoppat tillstånd. Du kan kontrollera att en tjänst i molnet har reserverat en IP-adress med hjälp av följande PowerShell-cmdlets: **$deployment = Get-AzureDeployment - ServiceName ”servicename”-fack ”produktion” $dep. ReservedIPName**. <br><li>Du försöker återställa en virtuell dator med följande särskilda nätverkskonfigurationer i samma molntjänst: <ul><li>Virtuella datorer under konfigurationen för belastningsutjämnaren, interna och externa.<li>Virtuella datorer med flera reserverade IP-adresser. <li>Virtuella datorer med flera nätverkskort. </ul><li>Välj en ny molntjänst i Användargränssnittet eller se [återställa överväganden](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) för virtuella datorer med särskilda nätverkskonfigurationer.</ol> |
| Det valda DNS-namnet används redan: <br>Ange ett annat DNS-namn och försök igen. |Den här DNS-namn som refererar till molntjänstens namn, vanligtvis slutar med **. cloudapp.net**. Det här namnet måste vara unikt. Om du får det här felet kan behöva du välja ett annat namn under återställningen. <br><br> Det här felet visas endast för användare av Azure-portalen. Återställningsåtgärden via PowerShell lyckas eftersom den återställer endast diskar och skapar inte den virtuella datorn. Felet kommer riktas när den virtuella datorn uttryckligen har skapats av dig när disken återställningsåtgärden. |
| Den angivna virtuella nätverkskonfigurationen är inte korrekt: <br>Ange ett annat virtuellt nätverk-konfiguration och försök igen. |Ingen |
| Den angivna Molntjänsten använder en reserverad IP-adress som inte matchar konfigurationen för den virtuella datorn håller på att återställas: <br>Ange en annan molntjänst som inte använder en reserverad IP. Eller välj en annan återställningspunkt att återställa från. |Ingen |
| Molntjänsten har nått sin gräns för antalet indataslutpunkter: <br>Försök igen genom att ange en annan molntjänst eller genom att använda en befintlig slutpunkt. |Ingen |
| Det Recovery Services-valvet och målresurserna lagringskontot finns i två olika regioner: <br>Kontrollera att lagringskontot som angetts i återställningsåtgärden finns i samma Azure-region som Recovery Services-valvet. |Ingen |
| Storage-konto som angetts för återställningsåtgärden stöds inte: <br>Endast Basic eller Standard storage-konton med inställningar för lokalt redundant eller geo-redundant replikering stöds. Välj ett lagringskonto. |Ingen |
| Typ av lagringskonto som angetts för återställningsåtgärden är inte online: <br>Kontrollera att lagringskontot som angetts i återställningsåtgärden är online. |Det här felet kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagringskonto. |
| Resursgruppens kvot har uppnåtts: <br>Ta bort några resursgrupper i Azure Portal eller kontakta Azure-supporten om du vill höja gränsen. |Ingen |
| Det valda undernätet finns inte: <br>Välj ett undernät som finns. |Ingen |
| Backup-tjänsten har inte behörighet att komma åt resurser i din prenumeration. |För att lösa det här felet kan återställa diskar med hjälp av stegen i [återställa säkerhetskopierade diskar](backup-azure-arm-restore-vms.md#create-new-restore-disks). Därefter använder du PowerShell steg i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Säkerhetskopieringen eller återställningen tar tid
Om din säkerhetskopia tar mer än 12 timmar, eller Återställ tar mer än 6 timmar, granska [bästa praxis](backup-azure-vms-introduction.md#best-practices) och [prestandaöverväganden](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM-agenten
### <a name="set-up-the-vm-agent"></a>Konfigurera VM-agenten
Vanligtvis finns VM-agenten redan i virtuella datorer som skapas från Azure-galleriet. Men virtuella datorer som har migrerats från lokala Datacenter kan inte VM-agenten installerad. VM-agenten måste installeras uttryckligen för dessa virtuella datorer.

#### <a name="windows-vms"></a>Virtuella Windows-datorer

* Ladda ned och installera [agentens MSI-fil](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsbehörighet för att slutföra installationen.
* För virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen [uppdatera VM-egenskapen](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) att indikera att agenten är installerad. Det här steget krävs inte för Azure Resource Manager-datorer.

#### <a name="linux-vms"></a>Virtuella Linux-datorer

* Installera den senaste versionen av agenten från lagringsplatsen för distribution. Mer information om paketnamnet finns den [Linuxagenten databasen](https://github.com/Azure/WALinuxAgent).
* För virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen [använder den här bloggen](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) och uppdatera VM-egenskapen som du kan kontrollera att agenten är installerad. Det här steget krävs inte för Resource Manager-datorer.

### <a name="update-the-vm-agent"></a>Uppdatera VM-agenten
#### <a name="windows-vms"></a>Virtuella Windows-datorer

* Uppdatera VM-agenten genom att installera om den [binärfilerna för VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Innan du uppdaterar agenten, kontrollera att ingen säkerhetskopiering utförs under uppdateringen av VM-agenten.

#### <a name="linux-vms"></a>Virtuella Linux-datorer

* Om du vill uppdatera VM-agenten för Linux, följer du anvisningarna i artikeln [uppdaterar VM-agenten för Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Använd alltid distribution databasen för att uppdatera agenten.

    Hämta agent-kod från GitHub. Om den senaste agenten inte är tillgängliga för din distribution, kontaktar du distributionsplatsen stöd för instruktionerna för att hämta den senaste agenten. Du kan också kontrollera senast [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) information i GitHub-lagringsplatsen.

### <a name="validate-vm-agent-installation"></a>Verifiera installationen av VM-agenten

Kontrollera versionen för VM-agenten på Windows virtuella datorer:

1. Logga in på Azure-dator och gå till mappen **C:\WindowsAzure\Packages**. Du bör hitta den **WaAppAgent.exe** fil.
2. Högerklicka på filen och gå till **egenskaper**. Välj sedan den **information** fliken. Den **produktversion** fältet ska vara 2.6.1198.718 eller högre.

## <a name="troubleshoot-vm-snapshot-issues"></a>Felsöka problem med VM-ögonblicksbild
Säkerhetskopiering av virtuella datorer är beroende av utfärda kommandon för ögonblicksbilden till underliggande lagring. Inte ha åtkomst till lagring eller fördröjningar i en ögonblicksbild-aktivitet körs kan det orsaka säkerhetskopieringsjobb misslyckas. Följande villkor kan orsaka ögonblicksbild aktivitet, fel:

- **Nätverksåtkomst till lagring blockeras med hjälp av NSG**. Läs mer om hur du [upprätta nätverksåtkomst](backup-azure-arm-vms-prepare.md#establish-network-connectivity) till Storage med hjälp av antingen vitlistning av IP-adresser eller via en proxyserver.
- **Virtuella datorer med SQL Server säkerhetskopiering har konfigurerats kan orsaka fördröjning för uppgift för ögonblicksbild**. Som standard skapar säkerhetskopiering en VSS fullständig säkerhetskopiering på virtuella Windows-datorer. Virtuella datorer som kör SQL Server med SQL Server säkerhetskopiering har konfigurerats, kan fördröjningar ögonblicksbild. Om ögonblicksbild fördröjningar leda till Säkerhetskopieringsfel, anger du följande registernyckel:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **VM-status rapporteras felaktigt eftersom Virtuellt datorn stängs av i RDP**. Om du använde fjärrskrivbordet för att stänga av den virtuella datorn, kontrollerar du att virtuella datorns status på portalen är korrekt. Om statusen inte är korrekt, använder du den **avstängning** alternativet i VM-instrumentpanelen i portalen för att stänga av den virtuella datorn.
- **Om fler än fyra virtuella datorer dela samma molntjänst, sprida de virtuella datorerna över flera principer för säkerhetskopiering**. Skicka säkerhetskopieringstider, så inga fler än fyra VM-säkerhetskopior börjar på samma gång. Försök att avgränsa starttider i principerna med minst en timme.
- **Den virtuella datorn körs vid hög CPU eller minne**. Om den virtuella datorn körs vid hög minne eller CPU-användning, mer än 90 procent, är din uppgift för ögonblicksbild i kö och fördröjd. Så småningom tidsgränsen uppnås. Om det här problemet inträffar kan försöka en säkerhetskopiering på begäran.

## <a name="networking"></a>Nätverk
Som alla tillägg behöver tillägg för säkerhetskopiering åtkomst till det offentliga internet ska fungera. Inte ha åtkomst till internet kan visa sig på olika sätt:

* Installera tillägget kan misslyckas.
* Säkerhetskopieringsåtgärder som ögonblicksbild kan misslyckas.
* Visa status för säkerhetskopieringen kan misslyckas.

Lösa offentliga internet-adresser diskuteras i [den här Support för Azure-bloggen](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Kontrollera DNS-konfigurationen för det virtuella nätverket och kontrollera att URI: er för Azure kan lösas.

När namnmatchning utförs korrekt måste åtkomst till Azure IP-adresser också anges. Om du vill avblockera åtkomst till Azure-infrastrukturen, gör du något av följande:

- Tillåt IP-intervallen för Azure-datacenter:
   1. Hämta listan över [Azure datacenter IP-adresser](https://www.microsoft.com/download/details.aspx?id=41653) tillåtas.
   1. Avblockera IP-adresser med hjälp av den [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) cmdlet. Kör denna cmdlet i Azure-dator, i ett upphöjt PowerShell-fönster. Kör som administratör.
   1. Lägga till regler i NSG, om du har en på plats, för att tillåta åtkomst till IP-adresser.
- Skapa en sökväg för HTTP-trafik:
   1. Om du har några nätverksbegränsning på plats kan du distribuera en HTTP-proxyserver för att dirigera trafiken. Ett exempel är en nätverkssäkerhetsgrupp. Se hur du distribuerar en HTTP-proxyserver i [nätverksanslutningen](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Lägga till regler i NSG, om du har en på plats, för att tillåta åtkomst till internet från HTTP-proxy.

> [!NOTE]
> DHCP måste vara aktiverat på gästen för IaaS VM-säkerhetskopiering för att fungera. Om du behöver en statisk privat IP-adress kan du konfigurera det via Azure-portalen eller PowerShell. Kontrollera att DHCP-alternativ i den virtuella datorn är aktiverad.
> Få mer information om hur du ställer in en statisk IP-adress via PowerShell:
> - [Lägga till en statiska interna IP-adress till en befintlig virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Ändra allokeringsmetod för en privat IP-adress som tilldelats ett nätverksgränssnitt](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
