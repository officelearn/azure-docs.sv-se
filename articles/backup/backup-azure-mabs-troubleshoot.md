---
title: Felsöka Azure Backup Server
description: Felsöka installationen, registrering av Azure Backup Server, och säkerhetskopiering och återställning av arbetsbelastningar för program.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: kasinh
ms.openlocfilehash: 22507a1b89c6a7d6867e9b669e1a2e70106a4e41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228294"
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Använd informationen i följande tabeller för att felsöka fel som uppstår när du använder Azure Backup Server.

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Registrera till ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten. | Rekommenderad åtgärd: <br> <ul><li> Ladda ned den senaste filen med autentiseringsuppgifter från valvet och försök igen. <br>(OR)</li> <li> Om föregående åtgärd inte fungerade, försök att hämta autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv. <br>(OR)</li> <li> Försök att uppdatera datum- och tidsinställningar som beskrivs i [den här bloggen](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OR)</li> <li> Kontrollera om c:\windows\temp har mer än 65000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekt i Temp-mappen. <br>(OR)</li> <li> Kontrollera status för certifikat. <br> a. Öppna **hantera datorcertifikat** (i Kontrollpanelen). <br> b. Expandera den **personliga** nod och dess underordnade nod **certifikat**.<br> c.  Ta bort certifikatet **Windows Azure Tools**. <br> d. Försök registrering i Azure Backup-klienten. <br> (OR) </li> <li> Kontrollera om någon Grupprincip är på plats. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replikeringen är inkonsekvent

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Replikeringen är inkonsekvent | Kontrollera att alternativet Automatisk konsekvenskontroll kontroll i guiden skydd är aktiverat. Mer information om orsakerna till repliken inkonsekvens och relevanta förslag finns i Microsoft TechNet-artikeln [repliken är inkonsekvent](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Kontrollera att Windows Server Backup är installerad på den skyddade servern vid systemtillstånd/BMR-säkerhetskopiering.</li><li> Sök efter utrymme-relaterade problem i DPM-lagringspoolen på DPM-/ Microsoft Azure Backup Server och allokera lagringsutrymme som krävs.</li><li> Kontrollera tillståndet för tjänsten Volume Shadow Copy på den skyddade servern. Om det är i ett inaktiverat tillstånd, konfigurera den att starta manuellt. Starta tjänsten på servern. Sedan går du tillbaka till konsolen för DPM-/ Microsoft Azure Backup Server och starta synkroniseringen med konsekvenskontrolljobbet.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Det gick inte att skapa onlineåterställningspunkt

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Det gick inte att skapa onlineåterställningspunkt | **Felmeddelande**: Windows Azure Backup-agenten kunde inte skapa en ögonblicksbild av den valda volymen. <br> **Lösning**: Prova att öka utrymmet i replik och återställningspunktvolym.<br> <br> **Felmeddelande**: Windows Azure Backup-agenten kan inte ansluta till OBEngine-tjänsten <br> **Lösning**: Kontrollera att OBEngine finns i listan över tjänster körs på datorn. Om OBEngine-tjänsten inte körs, kan du använda kommandot ”net start OBEngine” för att starta tjänsten obengine. <br> <br> **Felmeddelande**: Krypteringslösenfrasen för den här servern har inte angetts. Konfigurera en krypteringslösenfras. <br> **Lösning**: Försök att konfigurera en krypteringslösenfras. Om det misslyckas gör du följande: <br> <ol><li>Kontrollera att den tillfälliga platsen finns. Detta är den plats som nämns i registret **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, med namnet **ScratchLocation** ska finnas.</li><li> Om den tillfälliga platsen finns, kan du försöka registrera genom att använda den gamla lösenfrasen. *När du konfigurerar en krypteringslösenfrasen kan du spara den på en säker plats.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>De angivna valvautentiseringsuppgifterna skiljer sig från det valv som servern är registrerad

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Återställ | **Felkod**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Felmeddelande**: De angivna valvautentiseringsuppgifterna skiljer sig från det valv som servern är registrerad | **Orsak**: Det här problemet uppstår när du försöker återställa filer till en annan server från den ursprungliga servern med hjälp av extern DPM återställningsalternativ och om servern som återställs och den ursprungliga servern från där data är säkerhetskopieras inte är associerade med samma Recovery Service-valv.<br/> <br/>**Lösning** att lösa problemet genom att kontrollera både den ursprungliga och alternativ servern som är registrerade på samma valv.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Det gick inte att online jobben att skapa återställningspunkter för VMware VM

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Det gick inte att online jobben att skapa återställningspunkter för VMware VM. Det uppstod ett fel från VMware vid försök att hämta information om ChangeTracking. Felkod - FileFaultFault (ID 33621) |  <ol><li> Återställa CTK på VMware för de berörda virtuella datorerna.</li> <li>Kontrollera oberoende disken inte är på plats på VMware.</li> <li>Stoppa skyddet för de berörda virtuella datorerna och skydda igen med den **uppdatera** knappen. </li><li>Kör en kopia för de berörda virtuella datorerna.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM agent coordinator-tjänsten på servern

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Push-överföra agenter till skyddade servrar | Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM-agentkoordinatortjänsten på \<ServerName >. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar, utför följande steg**: <ul><li> Om du kopplar en dator från en ej betrodd domän, följa [här](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OR) </li><li> Om du kopplar en dator från en betrodd domän, felsöka genom att följa stegen i [den här bloggen](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OR)</li><li> Prova att inaktivera antivirusprogram som en felsökningsåtgärd. Om det löser problemet kan du ändra antivirus inställningarna enligt förslaget i [i den här artikeln](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera metadata för registret

| Åtgärd | Felinformation | Lösning |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera metadata för registret. Den här misslyckade uppdateringen kan leda till overusage av användningen av lagringsutrymme. Undvik detta genom att uppdatera ReFS Trimming-registerposten. | Justera registernyckeln **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ange värdet för Dword 1. |
|Installation | Det gick inte att uppdatera metadata för registret. Den här misslyckade uppdateringen kan leda till overusage av användningen av lagringsutrymme. Undvik detta genom att uppdatera Volume SnapOptimization-registerposten. | Skapa registernyckeln **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** med ett tomt strängvärde. |

## <a name="registration-and-agent-related-issues"></a>Registrering och agent-relaterade problem

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Push-överföra agenter till skyddade servrar | De autentiseringsuppgifter som har angetts för servern är ogiltig. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du utföra följande steg**: <br> Försök att installera skyddsagenten manuellt på produktionsservern som anges i [i den här artikeln](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup-agenten kunde inte ansluta till Azure Backup-tjänsten (ID: 100050) | Azure Backup-agenten kunde inte ansluta till Azure Backup-tjänsten. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du utföra följande steg**: <br>1. Kör följande kommando från en upphöjd kommandotolk: **psexec -i -s ”c:\Program c:\Program\Internet Explorer\iexplore.exe**. Internet Explorer-fönstret öppnas. <br/> 2. Gå till **verktyg** > **Internetalternativ** > **anslutningar** > **LAN-inställningar**. <br/> 3. Kontrollera proxyinställningarna för system-kontot. Ange Proxy-IP och port. <br/> 4. Stäng Internet Explorer.|
| Azure Backup Agent-installationen misslyckades | Microsoft Azure Recovery Services-installationen misslyckades. Alla ändringar som gjorts i systemet genom att Microsoft Azure Recovery Services-installationen återställdes. (ID: 4024) | Installera Azure-agenten manuellt.


## <a name="configuring-protection-group"></a>Konfigurera skyddsgrupp

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera skyddsgrupper | DPM kunde inte räkna upp PROGRAMKOMPONENTEN på den skyddade datorn (skyddat datornamn). | Välj **uppdatera** på skärmen Konfigurera skydd grupp Användargränssnittet på relevanta datasource/komponentnivå. |
| Konfigurera skyddsgrupper | Det gick inte att konfigurera skydd | Om den skyddade servern är en SQLServer, kontrollerar du att sysadmin-rollbehörighet har skickats till system-kontot (NTAuthority\System) på den skyddade datorn enligt beskrivningen i [i den här artikeln](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurera skyddsgrupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skyddsgruppen. | De diskar som läggs till i lagringspoolen [får inte innehålla en partition](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Ta bort alla befintliga volymer på diskarna. Lägg sedan till dem i lagringspoolen.|
| Ändring av |Det gick inte att ändra säkerhetskopieringsprincipen. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen efter en stund har överskridits. Kontakta Microsoft-supporten om problemet kvarstår. | **Orsak:**<br/>Det här felet uppstår under tre villkor: när säkerhetsinställningar har aktiverats vid försök att minska kvarhållningsintervallet under de lägsta värdena som angetts tidigare och när du har en version. (Stöds inte versioner är de under Microsoft Azure Backup Server version 2.0.9052 och Azure Backup Server uppdatering 1.) <br/>**Rekommenderad åtgärd:**<br/> Fortsätt med princip-relaterade uppdateringar genom att ange kvarhållningsperioden ovan minsta Kvarhållningsintervall angiven tidsperiod. (Lägsta kvarhållningsperioden är sju dagar för varje dag, fyra veckor för veckovisa, tre veckor för varje månad eller ett år för varje år.) <br><br>Du kan också rekommenderas en annan metod är att uppdatera backup-agenten och Azure Backup Server för att utnyttja alla säkerhetsuppdateringar. |

## <a name="backup"></a>Backup

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Ett oväntat fel inträffade medan jobbet kördes. Enheten är inte klar. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du utföra följande steg:** <br> <ul><li>Ange området lagringsutrymmet för skuggkopian till obegränsad på objekten i skyddsgruppen och kör sedan konsekvenskontrollen.<br></li> (OR) <li>Försök att ta bort det befintliga skyddet grupp och skapa flera nya grupper. Varje ny skyddsgrupp bör ha ett enskilt objekt i den.</li></ul> |
| Backup | Om du säkerhetskopierar endast systemtillstånd kontrollerar du att det finns tillräckligt med ledigt utrymme på den skyddade datorn för att lagra säkerhetskopian av systemtillståndet. | <ol><li>Kontrollera att Windows Server Backup är installerad på den skyddade datorn.</li><li>Kontrollera att det finns tillräckligt med utrymme på den skyddade datorn för systemets tillstånd. Det enklaste sättet att verifiera det här är att gå till den skyddade datorn, öppna Windows Server Backup, klicka dig igenom valen och väljer sedan BMR. Användargränssnittet sedan visar hur mycket utrymme som krävs. Öppna **WSB** > **lokal säkerhetskopia** > **schema för säkerhetskopiering** > **Välj Säkerhetskopieringskonfiguration**  >  **Fullständig server** (storlek visas). Använd den här storleken för verifiering.</li></ol>
| Backup | Säkerhetskopieringsfel för BMR | Om BMR är stor, flytta några programfiler till OS-enhet och försök igen. |
| Backup | Alternativet för att skydda igen en VMware VM på en ny Microsoft Azure Backup Server visas inte som är tillgängliga att lägga till. | Egenskaper för VMware riktas mot en gammal, Tillbakadragna instans av Microsoft Azure Backup Server. Gör så här för att lösa problemet:<br><ol><li>I VCenter (SC-VMM-motsvarighet), går du till den **sammanfattning** fliken och sedan till **anpassade attribut**.</li>  <li>Ta bort det gamla Microsoft Azure Backup Server-namnet från den **DPMServer** värde.</li>  <li>Gå tillbaka till den nya Microsoft Azure Backup-servern och ändra sida  När du har valt den **uppdatera** knapp, den virtuella datorn visas med en kryssruta som kan läggas till skydd.</li></ol> |
| Backup | Fel vid åtkomst till filer/delade mappar | Försök att ändra antivirus inställningarna enligt förslaget i TechNet-artikeln [köra antivirusprogram på DPM-servern](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Ändra lösenfras

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändra lösenfras |Säkerhetskod som angavs är felaktig. Ange rätt säkerhet PIN-kod för att slutföra åtgärden. |**Orsak:**<br/> Det här felet uppstår när du anger ett ogiltigt eller har upphört att gälla säkerhetskod när du utför en kritisk åtgärd (t.ex ändra en lösenfras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig säkerhetskod för att slutföra åtgärden. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valvet. Gå till **inställningar** > **egenskaper** > **skapa SÄKERHETSKOD**. Använd den här PIN-kod för att ändra lösenfrasen. |
| Ändra lösenfras |Åtgärden kunde inte utföras. ID: 120002 |**Orsak:**<br/>Det här felet uppstår när säkerhetsinställningar har aktiverats, eller när du försöker ändra lösenfrasen när du använder en version.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfrasen måste du först uppdatera backup-agenten till den lägsta versionen, vilket är 2.0.9052. Du måste också att uppdatera Azure Backup Server till minst update 1 och sedan ange en giltig säkerhetskod. PIN-koden får logga in på Azure portal och gå till Recovery Services-valvet. Gå till **inställningar** > **egenskaper** > **skapa SÄKERHETSKOD**. Använd den här PIN-kod för att ändra lösenfrasen. |


## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera e-postmeddelanden med en Office 365-konto |Fel-ID: 2013| **Orsak:**<br> Försök att använda Office 365-konto <br>**Rekommenderad åtgärd:**<ol><li> Det första du ska kontrollera är att ”tillåta anonym Relay på några få Connector” för DPM-server har ställts in på Exchange. Mer information om hur du konfigurerar detta finns i [Tillåt anonym Relay på en koppling som tar emot](https://technet.microsoft.com/library/bb232021.aspx) på TechNet.</li> <li> Om du inte använder ett internt SMTP-relä och ställa in med hjälp av Office 365-server, kan du konfigurera IIS att vara ett relä. Konfigurera DPM-servern ska [vidarebefordrar SMTP till O365 med hjälp av IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **VIKTIGT:** Se till att använda användaren\@domain.com format och *inte* domän\användare.<br><br><li>Kommer DPM att använda det lokala servernamnet som SMTP-servern port 587. Peka den till användarens e-postadressen som e-postmeddelanden ska hämtas från.<li> Användarnamn och lösenord på installationssidan DPM SMTP ska vara ett domänkonto i den domän som DPM finns på. </li><br> **Obs!** När du ändrar SMTP-serveradress kan göra ändringar i de nya inställningarna, stänga rutan inställningar och öppna den för att vara säker på att den återspeglar det nya värdet.  Bara ändra och testning kanske inte alltid orsakar de nya inställningarna ska börja gälla, så att testa det på så sätt är en bra idé.<br><br>När som helst under den här processen kan du rensa inställningarna genom att stänga DPM-konsolen och redigera följande registernycklar: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> ta bort SMTPPassword och SMTPUserName**. Du kan lägga till dem tillbaka till Användargränssnittet när du startar den igen.
