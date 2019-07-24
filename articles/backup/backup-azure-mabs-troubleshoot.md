---
title: Felsöka Azure Backup Server
description: Felsöka installation, registrering av Azure Backup Server och säkerhets kopiering och återställning av program arbets belastningar.
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 8e7ee506448f5ce0c8dc0b7f55dd9d66e73f110e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234788"
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Använd informationen i följande tabeller för att felsöka fel som uppstår när du använder Azure Backup Server.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför verifieringen nedan innan du börjar felsöka Microsoft Azure Backup Server (MABS):

- [Se till att Microsoft Azure Recovery Services (MARS) Agent är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns nätverksanslutning mellan MARS-agenten och Azure](https://aka.ms/AB-A4dp50)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Om det behövs måste du starta om och försöka igen
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](https://aka.ms/AB-AA4dwtt)
- Om registreringen misslyckades, se till att den server som du försöker installera Azure Backup Server inte redan har registrerats med ett annat valv
- Kontrollera om DPM-agenten redan finns om push-överföringen misslyckas. Om den gör det avinstallerar du agenten och provar att installera igen
- [Se till att inga andra processer eller antivirusprogram stör Azure Backup](https://aka.ms/AA4nyr4)<br>
- Kontrollera att SQL Agent-tjänsten körs och är inställd som automatisk på MAB-servern<br>


## <a name="invalid-vault-credentials-provided"></a>Ogiltiga autentiseringsuppgifter för valvet har angetts

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Registrera till ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är skadad eller har inte de senaste autentiseringsuppgifterna som är associerade med återställnings tjänsten. | Rekommenderad åtgärd: <br> <ul><li> Hämta den senaste inloggnings filen från valvet och försök igen. <br>ELLER</li> <li> Om föregående åtgärd inte fungerade kan du försöka att hämta autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv. <br>ELLER</li> <li> Försök att uppdatera datum-och tids inställningarna enligt beskrivningen i [den här bloggen](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>ELLER</li> <li> Kontrol lera om c:\Windows\Temp har fler än 65000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekten i Temp-mappen. <br>ELLER</li> <li> Kontrol lera status för certifikat. <br> a. Öppna **Hantera dator certifikat** (på kontroll panelen). <br> b. Expandera den **personliga** noden och dess underordnade Node- **certifikat**.<br> c.  Ta bort certifikatet **Windows Azure-verktyg**. <br> d. Gör om registreringen i Azure Backup-klienten. <br> ELLER </li> <li> Kontrol lera om någon grup princip finns på plats. </li></ul> |

## <a name="replica-is-inconsistent"></a>Repliken är inkonsekvent

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Backup | Repliken är inkonsekvent | Kontrol lera att alternativet automatisk konsekvens kontroll i guiden skydds grupp är aktiverat. Mer information om orsaker till replik inkonsekvens och relevanta förslag finns i artikel [repliken är inkonsekvent](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Kontrol lera att Windows Server Backup är installerat på den skyddade servern i händelse av system tillstånd/BMR säkerhets kopiering.</li><li> Kontrol lera om det finns problem med problem i DPM-lagringspoolen på DPM/Microsoft Azure Backup-servern och allokera lagrings utrymme efter behov.</li><li> Kontrol lera status för tjänsten Volume Shadow Copy på den skyddade servern. Om den är i ett inaktiverat tillstånd ställer du in den så att den startar manuellt. Starta tjänsten på-servern. Gå sedan tillbaka till DPM/Microsoft Azure Backup Server-konsolen och starta synkroniseringen med konsekvens kontroll jobbet.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Det gick inte att skapa en onlineåterställningspunkt

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Backup | Det gick inte att skapa en onlineåterställningspunkt | **Fel meddelande**: Windows Azure Backup-agenten kunde inte skapa en ögonblicks bild av den valda volymen. <br> **Lösning**: Försök att öka utrymmet på replik-och återställnings punkt volymen.<br> <br> **Fel meddelande**: Windows Azure Backup-agenten kan inte ansluta till OBEngine-tjänsten <br> **Lösning**: kontrol lera att OBEngine finns i listan över aktiva tjänster på datorn. Om OBEngine-tjänsten inte körs använder du kommandot "net start OBEngine" för att starta tjänsten OBEngine. <br> <br> **Fel meddelande**: Krypterings lösen frasen för den här servern har inte angetts. Konfigurera en krypterings lösen fras. <br> **Lösning**: Försök att konfigurera en krypterings lösen fras. Gör så här om det Miss lyckas: <br> <ol><li>Kontrol lera att arbets platsen finns. Det här är den plats som nämns i registrets **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure-Backup\Config**med namnet **ScratchLocation** ska finnas.</li><li> Försök att registrera igen med hjälp av den gamla lösen frasen om det finns en sådan. *När du konfigurerar en krypterings lösen fras sparar du den på en säker plats.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>De angivna autentiseringsuppgifterna för valvet skiljer sig från valvet som servern är registrerad på

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Återställ | **Felkod**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Fel meddelande**: De angivna autentiseringsuppgifterna för valvet skiljer sig från valvet som servern är registrerad på | **Orsak**: Det här problemet uppstår när du försöker återställa filer till den alternativa servern från den ursprungliga servern med hjälp av alternativet extern DPM-återställning och om servern som återställs och den ursprungliga servern från vilken data säkerhets kopie ras inte är kopplad till samma Recovery Service-valv.<br/> <br/>**Lösning** För att lösa det här problemet ser du till att både den ursprungliga och den alternativa servern är registrerad i samma valv.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Det gick inte att skapa jobb för onlineåterställningspunkt för VMware VM

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Backup | Det gick inte att skapa jobb för onlineåterställningspunkt för VMware VM. DPM påträffade ett fel från VMware vid försök att hämta ChangeTracking-information. ErrorCode-FileFaultFault (ID 33621) |  <ol><li> Återställ CTK på VMware för de virtuella datorerna.</li> <li>Kontrol lera att den oberoende disken inte finns på plats i VMware.</li> <li>Stoppa skyddet för de virtuella datorerna och skydda dem igen med knappen **Uppdatera** . </li><li>Kör en kopia av de virtuella datorerna.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Agent åtgärden misslyckades på grund av ett kommunikations fel med tjänsten DPM agent Coordinator på servern

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Push-överför agent (er) till skyddade servrar | Agent åtgärden misslyckades på grund av ett kommunikations fel med tjänsten DPM-Agentkoordinator på \<servername >. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar utför du följande steg**: <ul><li> Följ [dessa steg](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx)om du kopplar en dator från en obetrodd domän. <br> ELLER </li><li> Om du ansluter en dator från en betrodd domän kan du felsöka med hjälp av stegen som beskrivs i [den här bloggen](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>ELLER</li><li> Försök att inaktivera antivirus som fel söknings steg. Om det löser problemet ändrar du inställningarna för antivirus programmet enligt rekommendationerna i [den här artikeln](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera metadata för registret

| Åtgärd | Felinformation | Lösning: |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera metadata för registret. Det här uppdaterings felet kan leda till överförbrukning av lagrings förbrukning. För att undvika detta uppdaterar du register posten ReFS-trimning. | Justera register nyckeln **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ange värdet DWORD till 1. |
|Installation | Det gick inte att uppdatera metadata för registret. Det här uppdaterings felet kan leda till överförbrukning av lagrings förbrukning. Undvik detta genom att uppdatera register posten Volume SnapOptimization. | Skapa register nyckeln **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** med ett tomt sträng värde. |

## <a name="registration-and-agent-related-issues"></a>Registrerings-och Agent-relaterade problem

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Push-överför agent (er) till skyddade servrar | De angivna autentiseringsuppgifterna för servern är ogiltiga. | **Utför följande steg om den rekommenderade åtgärden som visas i produkten inte fungerar**: <br> Försök att installera skydds agenten manuellt på produktions servern enligt vad som anges i [den här artikeln](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup-agenten kunde inte ansluta till Azure Backups tjänsten (ID: 100050) | Azure Backup-agenten kunde inte ansluta till Azure Backups tjänsten. | **Utför följande steg om den rekommenderade åtgärden som visas i produkten inte fungerar**: <br>1. Kör följande kommando från en upphöjd prompt: **PsExec-i-s "C:\Program\Internet Explorer\iexplore.exe**. Fönstret Internet Explorer öppnas. <br/> 2. Gå till **verktyg** > **Internet alternativ** > anslutningarLAN > -**Inställningar**. <br/> 3. Ändra inställningarna för att använda en proxyserver. Ange sedan information om proxyservern.<br/> 4. Om datorn har begränsad Internet åtkomst kontrollerar du att brand Väggs inställningarna på datorn eller proxyservern tillåter dessa [URL: er](backup-configure-vault.md#verify-internet-access) och [IP-adress](backup-configure-vault.md#verify-internet-access).|
| Det gick inte att installera Azure Backup Agent | Det gick inte att installera Microsoft Azure Recovery Services. Alla ändringar som har gjorts i systemet av den Microsoft Azure Recovery Services installationen återställdes. (ID: 4024) | Installera Azure-agenten manuellt.


## <a name="configuring-protection-group"></a>Konfigurerar skydds grupp

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Konfigurera skydds grupper | DPM kunde inte räkna upp program komponenten på den skyddade datorn (skyddat dator namn). | Välj **Uppdatera** på skärmen konfigurera skydds gruppens användar gränssnitt på den relevanta data källan/komponent nivån. |
| Konfigurera skydds grupper | Det gick inte att konfigurera skydd | Om den skyddade servern är en SQL-Server kontrollerar du att sysadmin-rollens behörigheter har angetts till system kontot (NTAuthority\System) på den skyddade datorn enligt beskrivningen i [den här artikeln](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurera skydds grupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skydds gruppen. | Diskarna som läggs till i lagringspoolen [får inte innehålla en partition](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Ta bort alla befintliga volymer på diskarna. Lägg sedan till dem i lagringspoolen.|
| Princip ändring |Det gick inte att ändra säkerhets kopierings principen. Fel: Den aktuella åtgärden kunde inte utföras på grund av ett internt tjänst fel [0x29834]. Försök igen om en stund. Kontakta Microsoft-supporten om problemet kvarstår. | **Orsak**<br/>Det här felet uppstår under tre förhållanden: när säkerhets inställningarna är aktiverade kan du, när du försöker minska kvarhållningsintervallet under de lägsta värdena som angetts tidigare, och när du använder en version som inte stöds. (Versioner som inte stöds är de nedan Microsoft Azure Backup Server version 2.0.9052 och Azure Backup Server uppdatering 1.) <br/>**Rekommenderad åtgärd:**<br/> Om du vill fortsätta med principbaserad uppdateringar anger du kvarhållningsperioden ovanför den minsta kvarhållningsperioden som angetts. (Den minsta Retentions perioden är sju dagar i fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år.) <br><br>Alternativt är det en annan prioriterad metod att uppdatera säkerhets kopierings agenten och Azure Backup Server för att utnyttja alla säkerhets uppdateringar. |

## <a name="backup"></a>Backup

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Backup | Ett oväntat fel uppstod när jobbet kördes. Enheten är inte klar. | **Utför följande steg om den rekommenderade åtgärden som visas i produkten inte fungerar:** <br> <ul><li>Ange lagrings utrymmet för skugg kopian till obegränsade för objekten i skydds gruppen och kör sedan konsekvens kontrollen.<br></li> ELLER <li>Försök att ta bort den befintliga skydds gruppen och skapa flera nya grupper. Varje ny skydds grupp måste ha ett enskilt objekt.</li></ul> |
| Backup | Om du bara säkerhetskopierar system tillstånd kontrollerar du att det finns tillräckligt med ledigt utrymme på den skyddade datorn för att lagra säkerhets kopian av system tillstånd. | <ol><li>Kontrol lera att Windows Server Backup är installerat på den skyddade datorn.</li><li>Kontrol lera att det finns tillräckligt med utrymme på den skyddade datorn för system tillstånd. Det enklaste sättet att kontrol lera detta är att gå till den skyddade datorn, öppna Windows Server Backup, klicka igenom valen och sedan välja BMR. Användar gränssnittet visar sedan hur mycket utrymme som krävs. Öppna **WSB** > , > lokal > säkerhets kopieringschema > för säkerhets kopiering**Välj säkerhets kopierings konfiguration** **fullständig Server** (storleken visas). Använd den här storleken för verifiering.</li></ol>
| Backup | Säkerhets kopierings problem för BMR | Om storleken på BMR är stor flyttar du några programfiler till OS-enheten och försöker igen. |
| Backup | Alternativet att skydda en virtuell VMware-dator på en ny Microsoft Azure Backup Server visas inte som tillgänglig för att lägga till. | VMware-egenskaper pekas på en gammal, tillbakadragen instans av Microsoft Azure Backup Server. Gör så här för att lösa problemet:<br><ol><li>I VCenter (SC-VMM-motsvarighet) går du till fliken **Sammanfattning** och sedan till **anpassade attribut**.</li>  <li>Ta bort det gamla Microsoft Azure Backup Server namnet från **Dpmserver** -värdet.</li>  <li>Gå tillbaka till den nya Microsoft Azure Backup servern och ändra PG.  När du har valt knappen **Uppdatera** visas den virtuella datorn med en kryss ruta som tillgänglig för att lägga till i skyddet.</li></ol> |
| Backup | Fel vid åtkomst till filer/delade mappar | Försök att ändra Antivirus inställningarna enligt rekommendationer i TechNet-artikeln [köra antivirus program på DPM-servern](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Ändra lösen fras

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Ändra lösen fras |Den angivna säkerhets koden är felaktig. Ange rätt säkerhets kod för att slutföra åtgärden. |**Orsak**<br/> Det här felet uppstår när du anger en ogiltig eller utgången säkerhets-PIN-kod när du utför en kritisk åtgärd (till exempel ändra en lösen fras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig PIN-kod för att slutföra åtgärden. Logga in på Azure Portal och gå till Recovery Services-valvet för att få PIN-koden. Gå sedan till **Inställningar** > **Egenskaper** > **skapa säkerhets kod**. Använd den här PIN-koden för att ändra lösen frasen. |
| Ändra lösen fras |Åtgärden misslyckades. IDENTITET 120002 |**Orsak**<br/>Felet uppstår när säkerhets inställningar är aktiverade, eller när du försöker ändra lösen frasen när du använder en version som inte stöds.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösen frasen måste du först uppdatera säkerhets kopierings agenten till den lägsta versionen, som är 2.0.9052. Du måste också uppdatera Azure Backup Server till minst uppdatering 1 och sedan ange en giltig PIN-kod för säkerhet. Logga in på Azure Portal och gå till Recovery Services-valvet för att få PIN-koden. Gå sedan till **Inställningar** > **Egenskaper** > **skapa säkerhets kod**. Använd den här PIN-koden för att ändra lösen frasen. |


## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

| Åtgärd | Felinformation | Lösning: |
| --- | --- | --- |
| Konfigurera e-postaviseringar med ett Office 365-konto |Fel-ID: 2013| **Orsak**<br> Försöker använda Office 365-konto <br>**Rekommenderad åtgärd:**<ol><li> Det första du ser är att "Tillåt anonym vidarebefordran på en mottagnings anslutning" för DPM-servern har kon figurer ATS på Exchange. Mer information om hur du konfigurerar detta finns i [Tillåt anonymt relä på en Receive-anslutning](https://technet.microsoft.com/library/bb232021.aspx) på TechNet.</li> <li> Om du inte kan använda en intern SMTP-relä och behöver konfigurera med hjälp av din Office 365-Server kan du konfigurera IIS att vara ett relä. Konfigurera DPM-servern så att [den vidarebefordrar SMTP till O365 med IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **OVIKTIG** Se till att använda formatet User\@Domain.com och *inte* domain\user.<br><br><li>Punkt DPM använder det lokala server namnet som SMTP-server, port 587. Peka sedan på det e-postmeddelande som e-postmeddelandena ska komma från.<li> Användar namnet och lösen ordet på sidan DPM SMTP-installation ska vara ett domän konto i den domän där DPM finns. </li><br> **Obs!** När du ändrar SMTP-serveradressen gör du ändringen av de nya inställningarna, stänger rutan inställningar och öppnar den igen för att vara säker på att den motsvarar det nya värdet.  Att bara ändra och testa kanske inte alltid gör att de nya inställningarna börjar gälla, så testa det på det sättet som är bäst.<br><br>Du kan när som helst under den här processen rensa de här inställningarna genom att stänga DPM-konsolen och redigera följande register nycklar: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> ta bort SMTPPassword och SMTPUserName-nycklar**. Du kan lägga tillbaka dem i användar gränssnittet när du startar det igen.


## <a name="common-issues"></a>Vanliga problem

I det här avsnittet beskrivs vanliga fel som kan uppstå när du använder Azure Backup Server.


### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Säkerhetskopieringen misslyckades eftersom disksäkerhetskopieringsrepliken är ogiltig eller saknas. | Lös problemet genom att kontrol lera stegen nedan och försök igen: <br/> 1. Skapa en disk återställnings punkt<br/> 2. Kör konsekvens kontroll på data källan <br/> 3. Stoppa skyddet av data källan och konfigurera sedan om skyddet för den här data källan

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicks bilden av käll volymen misslyckades eftersom metadata på repliken är ogiltiga. | Skapa en disk återställnings punkt för data källan och försök att säkerhetskopiera online igen

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicks bilden av käll volymen misslyckades på grund av inkonsekvent DataSource-replik. | Kör en konsekvens kontroll på den här data källan och försök igen

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det gick inte att säkerhetskopiera eftersom disk säkerhets kopierings repliken inte kunde klonas.| Se till att alla tidigare disk säkerhets kopierings repliker (. vhdx) har demonterats och att ingen disk till disk säkerhets kopiering pågår under säkerhets kopiering online
