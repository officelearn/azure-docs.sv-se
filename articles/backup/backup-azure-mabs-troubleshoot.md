---
title: Felsöka Azure Backup Server
description: Felsöka installation, registrering av Azure Backup Server och säkerhetskopiering och återställning av programarbetsbelastningar.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: cc0cf7e91c1aacbc637d33ab1e5546cc54836b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673118"
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Använd informationen i följande tabeller för att felsöka fel som uppstår när du använder Azure Backup Server.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför valideringen nedan innan du börjar felsöka Microsoft Azure Backup Server (MABS):

- [Se till att MARS-agenten (Microsoft Azure Recovery Services) är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns nätverksanslutning mellan MARS-agenten och Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Om det behövs startar du om och försöker igen
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Om registreringen misslyckas kontrollerar du att servern där du försöker installera Azure Backup Server inte redan är registrerad i ett annat valv
- Kontrollera om DPM-agenten redan finns om push-överföringen misslyckas. Om den gör det avinstallerar du agenten och provar att installera igen
- [Se till att inga andra processer eller antivirusprogram stör Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Kontrollera att SQL Agent-tjänsten körs och är inställd som automatisk på MAB-servern<br>

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Registrera dig i ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är skadad eller har inte de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. | Rekommenderad åtgärd: <br> <ul><li> Hämta den senaste autentiseringsfilen från valvet och försök igen. <br>Jag vet inte vad du ska ta på dig.</li> <li> Om den föregående åtgärden inte fungerade kan du prova att hämta autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv. <br>Jag vet inte vad du ska ta på dig.</li> <li> Prova att uppdatera datum- och tidsinställningarna enligt beskrivningen i [den här artikeln](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided). <br>Jag vet inte vad du ska ta på dig.</li> <li> Kontrollera om c:\windows\temp har fler än 65000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekten i mappen Temp. <br>Jag vet inte vad du ska ta på dig.</li> <li> Kontrollera certifikatens status. <br> a. Öppna **Hantera datorcertifikat** (på Kontrollpanelen). <br> b. Expandera den **personliga** noden och dess underordnade **nodcertifikat**.<br> c.  Ta bort certifikatet **Windows Azure Tools**. <br> d. Försök igen registreringen i Azure Backup-klienten. <br> Jag vet inte vad du ska ta på dig. </li> <li> Kontrollera om någon grupprincip finns på plats. </li></ul> |

## <a name="replica-is-inconsistent"></a>Repliken är inkonsekvent

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Säkerhetskopiering | Repliken är inkonsekvent | Kontrollera att alternativet för automatisk konsekvenskontroll i guiden Skyddsgrupp är aktiverat. Mer information om orsakerna till replikkonsekvens och relevanta förslag finns i artikeln [Repliken är inkonsekvent](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10)).<br> <ol><li> Om det finns säkerhetskopiering av systemtillstånd/BMR kontrollerar du att Windows Server Backup är installerat på den skyddade servern.</li><li> Sök efter utrymmesrelaterade problem i DPM-lagringspoolen på DPM/Microsoft Azure Backup Server och allokera lagring efter behov.</li><li> Kontrollera tillståndet för tjänsten Volume Shadow Copy på den skyddade servern. Om den är inaktiverad ställer du in den så att den startar manuellt. Starta tjänsten på servern. Gå sedan tillbaka till DPM/Microsoft Azure Backup Server-konsolen och starta synkroniseringen med jobbet för konsekvenskontroll.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Det gick inte att skapa återställningspunkten online

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Säkerhetskopiering | Det gick inte att skapa återställningspunkten online | **Felmeddelande:** Windows Azure Backup Agent kunde inte skapa en ögonblicksbild av den valda volymen. <br> **Lösning:** Försök att öka utrymmet i replik- och återställningspunktsvolymen.<br> <br> **Felmeddelande:** Windows Azure Backup Agent kan inte ansluta till OBEngine-tjänsten <br> **Lösning**: kontrollera att OBEngine finns i listan över tjänster som körs på datorn. Om OBEngine-tjänsten inte körs använder du kommandot "net start OBEngine" för att starta OBEngine-tjänsten. <br> <br> **Felmeddelande:** Krypteringslösenfrasen för den här servern har inte angetts. Konfigurera en krypteringslösenfras. <br> **Lösning:** Försök konfigurera en krypteringslösenfras. Om det misslyckas gör du följande: <br> <ol><li>Kontrollera att scratch-platsen finns. Det här är den plats som nämns i registret **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, med namnet **ScratchLocation** bör finnas.</li><li> Om scratch-platsen finns kan du försöka registrera om med hjälp av den gamla lösenfrasen. *När du konfigurerar en krypteringslösenfras sparar du den på en säker plats.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>De ursprungliga och externa DPM-servrarna måste vara registrerade i samma valv

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Återställ | **Felkod**: CBPServerRegistreradVaultDontMatchMed Autentiseringsuppgifter för 100110 <br/> <br/>**Felmeddelande:** De ursprungliga och externa DPM-servrarna måste vara registrerade i samma valv | **Orsak:** Det här problemet uppstår när du försöker återställa filer till den alternativa servern från den ursprungliga servern med hjälp av alternativet Extern DPM-återställning och om servern som återställs och den ursprungliga servern från där data säkerhetskopieras inte är associerade med samma Återställningstjänstvalv.<br/> <br/>**Lösning** LÃ¶s problemet genom att se till att både den ursprungliga och den alternativa servern är registrerad i samma valv.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Online återställningspunkt skapa jobb för VMware VM misslyckas

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Säkerhetskopiering | Online återställningspunkt skapa jobb för VMware VM misslyckas. DPM påträffade ett fel från VMware när du försökte få ChangeTracking-information. ErrorCode - FileFaultFault (ID 33621) |  <ol><li> Återställ CTK på VMware för de berörda virtuella datorerna.</li> <li>Kontrollera att oberoende disk inte finns på plats på VMware.</li> <li>Stoppa skyddet för de berörda virtuella datorerna och rotera igen med knappen **Uppdatera.** </li><li>Kör en kopia för de berörda virtuella datorerna.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM-agentkoordinatorn på servern

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Att driva agenter till skyddade servrar | Agentåtgärden misslyckades på grund av ett kommunikationsfel \<med DPM-agentkoordinatorn på ServerName>. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar utför du följande steg:** <ul><li> Om du ansluter en dator från en domän som inte är betrodd följer du [dessa steg](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> Jag vet inte vad du ska ta på dig. </li><li> Om du ansluter en dator från en betrodd domän felsöker du med hjälp av stegen i [den här bloggen](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>Jag vet inte vad du ska ta på dig.</li><li> Prova att inaktivera antivirusprogram som ett felsökningssteg. Om problemet lösers ändrar du antivirusinställningarna enligt förslaget i [den här artikeln](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera metadata för registret

| Åtgärd | Felinformation | Lösning |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera registermetadata. Det här uppdateringsfelet kan leda till överanvändning av lagringsförbrukning. För att undvika den här uppdateringen av registerposten ReFS Triming. | Justera registernyckeln **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ange värdet Dword till 1. |
|Installation | Det gick inte att uppdatera registermetadata. Det här uppdateringsfelet kan leda till överanvändning av lagringsförbrukning. Undvik detta genom att uppdatera registerposten Volume SnapOptimization. | Skapa registernyckeln **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** med ett tomt strängvärde. |

## <a name="registration-and-agent-related-issues"></a>Registrerings- och agentrelaterade frågor

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Att driva agenter till skyddade servrar | De autentiseringsuppgifter som har angetts för servern är ogiltiga. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar gör du följande:** <br> Försök att installera skyddsagenten manuellt på produktionsservern enligt den [här artikeln](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| Azure Backup Agent kunde inte ansluta till Azure Backup-tjänsten (ID: 100050) | Azure Backup Agent kunde inte ansluta till Azure Backup-tjänsten. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar gör du följande:** <br>1. Kör följande kommando från en upphöjd prompt: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Då öppnas Internet Explorer-fönstret. <br/> 2. Gå till **Verktyg** > **Internet Alternativ** > **Anslutningar** > **LAN-inställningar**. <br/> 3. Ändra inställningarna för att använda en proxyserver. Ange sedan information om proxyservern.<br/> 4. Om din maskin har begränsad internetåtkomst, se till att brandväggsinställningarna på maskinen eller proxyn tillåter dessa [webbadresser](install-mars-agent.md#verify-internet-access) och [IP-adress](install-mars-agent.md#verify-internet-access).|
| Installationen av Azure Backup Agent misslyckades | Installationen av Microsoft Azure Recovery Services misslyckades. Alla ändringar som gjordes i systemet av Installationen av Microsoft Azure Recovery Services har återställts. (ID: 4024) | Installera Azure Agent manuellt.

## <a name="configuring-protection-group"></a>Konfigurera skyddsgrupp

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera skyddsgrupper | DPM kunde inte räkna upp programkomponenten på den skyddade datorn (skyddat datornamn). | Välj **Uppdatera** på skärmen konfigurera skyddsgruppsgränssnitt på relevant datakälla/komponentnivå. |
| Konfigurera skyddsgrupper | Kan inte konfigurera skydd | Om den skyddade servern är en SQL-server kontrollerar du att sysadmin-rollbehörigheterna har angetts till systemkontot (NTAuthority\System) på den skyddade datorn enligt beskrivningen i [den här artikeln](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12)).
| Konfigurera skyddsgrupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skyddsgruppen. | Diskarna som läggs till i lagringspoolen [bör inte innehålla en partition](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)). Ta bort alla befintliga volymer på diskarna. Lägg sedan till dem i lagringspoolen.|
| Ändring av politiken |Det gick inte att ändra säkerhetskopieringsprincipen. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen när en viss tid har gått. Kontakta Microsofts supportavdelning om problemet kvarstår. | **Orsaka:**<br/>Det här felet uppstår under tre förhållanden: när säkerhetsinställningar är aktiverade, när du försöker minska kvarhållningsområdet under de minimivärden som angetts tidigare och när du är på en version som inte stöds. (Versioner som inte stöds är de som finns under Uppdatering 2.0.0.052 och Uppdatering 1 av Azure Backup Server.) <br/>**Rekommenderad åtgärd:**<br/> Om du vill fortsätta med principrelaterade uppdateringar anger du kvarhållningsperioden över den angivna minsta kvarhållningsperioden. (Den minsta retentionstiden är sju dagar för varje dag, fyra veckor för varje vecka, tre veckor för månadsvis eller ett år för varje år.) <br><br>Alternativt är en annan prioriterad metod att uppdatera säkerhetskopieringsagenten och Azure Backup Server för att utnyttja alla säkerhetsuppdateringar. |

## <a name="backup"></a>Säkerhetskopiering

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Säkerhetskopiering | Ett oväntat fel uppstod när jobbet kördes. Enheten är inte klar. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar gör du följande:** <br> <ul><li>Ange att lagringsutrymmet för skuggkopiering ska vara obegränsat för objekten i skyddsgruppen och kör sedan konsekvenskontrollen.<br></li> Jag vet inte vad du ska ta på dig. <li>Prova att ta bort den befintliga skyddsgruppen och skapa flera nya grupper. Varje ny skyddsgrupp bör ha en enskild artikel i den.</li></ul> |
| Säkerhetskopiering | Om du säkerhetskopierar systemtillståndet kontrollerar du att det finns tillräckligt med ledigt utrymme på den skyddade datorn för att lagra säkerhetskopian av systemtillståndet. | <ol><li>Kontrollera att Windows Server Backup är installerat på den skyddade datorn.</li><li>Kontrollera att det finns tillräckligt med utrymme på den skyddade datorn för systemtillståndet. Det enklaste sättet att kontrollera detta är att gå till den skyddade datorn, öppna Windows Server Backup, klicka igenom valen och sedan välja BMR. Användargränssnittet talar sedan om hur mycket utrymme som krävs. Öppna **WSB** > Lokal**säkerhetskopieringsschemaVal** > **Local backup** > **Backup Configuration** > **Full server** (storleken visas). Använd den här storleken för verifiering.</li></ol>
| Säkerhetskopiering | Säkerhetskopiera fel för BMR | Om BMR-storleken är stor flyttar du vissa programfiler till OS-enheten och försöker igen. |
| Säkerhetskopiering | Alternativet att återreda en virtuell virtuell vmware-dator på en ny Microsoft Azure Backup Server visas inte som tillgängligt att lägga till. | VMware-egenskaper pekar på en gammal, pensionerad instans av Microsoft Azure Backup Server. Gör så här för att lösa problemet:<br><ol><li>I VCenter (MOTSVARANDE SC-VMM) går du till fliken **Sammanfattning** och går sedan till **Anpassade attribut**.</li>  <li>Ta bort det gamla Microsoft Azure Backup Server-namnet från **DPMServer-värdet.**</li>  <li>Gå tillbaka till den nya Microsoft Azure Backup Server och ändra PG.  När du har **markerat** knappen Uppdatera visas den virtuella datorn med en kryssruta som finns att lägga till i skyddet.</li></ol> |
| Säkerhetskopiering | Fel vid åtkomst till filer/delade mappar | Prova att ändra antivirusinställningarna som föreslås i den här artikeln [Kör antivirusprogram på DPM-servern](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757911(v=sc.12)).|

## <a name="change-passphrase"></a>Ändra lösenfras

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändra lösenfras |Säkerhets-PIN-koden som angavs är felaktig. Ange rätt säkerhets-PIN för att slutföra den här åtgärden. |**Orsaka:**<br/> Det här felet uppstår när du anger en ogiltig eller utgången säkerhets-PIN-kod medan du utför en kritisk åtgärd (till exempel ändra en lösenfras). <br/>**Rekommenderad åtgärd:**<br/> För att slutföra åtgärden måste du ange en giltig säkerhets-PIN-kod. Logga in på Azure-portalen och gå till Recovery Services-valvet för att hämta PIN-koden. Gå sedan till **Egenskaper för Inställningar** > **Properties** > **Generera säkerhets-PIN**. Använd den här PIN-koden för att ändra lösenfrasen. |
| Ändra lösenfras |Åtgärden misslyckades. ID: 120002 |**Orsaka:**<br/>Det här felet uppstår när säkerhetsinställningar är aktiverade eller när du försöker ändra lösenfrasen när du använder en version som inte stöds.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfrasen måste du först uppdatera säkerhetskopieringsagenten till den minsta versionen, vilket är 2.0.9052. Du måste också uppdatera Azure Backup Server till ett minimum av uppdatering 1 och ange sedan en giltig säkerhets-PIN-kod. För att hämta PIN-koden loggar du in på Azure-portalen och går till Recovery Services-valvet. Gå sedan till **Egenskaper för Inställningar** > **Properties** > **Generera säkerhets-PIN**. Använd den här PIN-koden för att ändra lösenfrasen. |

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera e-postmeddelanden med ett Office 365-konto |Fel-ID: 2013| **Orsaka:**<br> Försöker använda Office 365-konto <br>**Rekommenderad åtgärd:**<ol><li> Det första du kan se till är att "Tillåt anonym relay på en mottagningsanslutning" för din DPM-server är konfigurerad på Exchange. Mer information om hur du konfigurerar detta finns i [Tillåt anonymt relä på en mottagningskoppling](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Om du inte kan använda ett internt SMTP-relä och behöver konfigurera med hjälp av Office 365-servern kan du konfigurera IIS som relä. Konfigurera DPM-servern för att [vidarebefordra SMTP till O365 med IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Var noga med\@att använda användaren domain.com format och *inte* domän\användare.<br><br><li>Punkt DPM för att använda det lokala servernamnet som SMTP-server, port 587. Peka sedan på användarens e-post som e-postmeddelandena ska komma från.<li> Användarnamnet och lösenordet på installationssidan för DPM SMTP ska vara för ett domänkonto i domänen som DPM är aktiverat. </li><br> När du ändrar SMTP-serveradressen gör du ändringen av de nya inställningarna, stänger inställningsrutan och öppnar den igen för att vara säker på att den återspeglar det nya värdet.  Att bara ändra och testa kanske inte alltid gör att de nya inställningarna börjar gälla, så att testa det på det här sättet är en bra idé.<br><br>När som helst under den här processen kan du rensa dessa inställningar genom att stänga DPM-konsolen och redigera följande registernycklar: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword och SMTPUserName keys**. Du kan lägga till dem i användargränssnittet igen när du startar det igen.

## <a name="common-issues"></a>Vanliga problem

Det här avsnittet beskriver de vanliga fel som kan uppstå när du använder Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Säkerhetskopieringen misslyckades eftersom disksäkerhetskopieringsrepliken är ogiltig eller saknas. | Lös problemet genom att kontrollera stegen nedan och försöka igen: <br/> 1. Skapa en återställningspunkt för disk<br/> 2. Kör konsekvenskontroll på datakälla <br/> 3. Stoppa skyddet av datakällan och konfigurera sedan om skyddet för den här datakällan

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicksbilden av källvolymen misslyckades eftersom metadata på repliken är ogiltiga. | Skapa en diskåterställningspunkt för den här datakällan och försök igen säkerhetskopiering igen

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicksbilden av källvolymen misslyckades på grund av inkonsekvent dataresursreplik. | Kör en konsekvenskontroll på den här datakällan och försök igen

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det gick inte att säkerhetskopiera eftersom disksäkerhetskopieringsrepliken inte kunde klonas.| Kontrollera att alla tidigare replikfiler för disksäkerhetskopiering (.vhdx) har avmonterats och att ingen disk till disksäkerhetskopiering pågår under säkerhetskopiering online
