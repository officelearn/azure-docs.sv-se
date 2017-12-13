---
title: "Felsöka Azure Backup-Server | Microsoft Docs"
description: "Felsöka installationen, registrering av Azure Backup-Server, och säkerhetskopiering och återställning av arbetsbelastningar för program."
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: e9517672138a4ea7577af1295dea13771733983e
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Använd informationen i följande tabeller för att felsöka som uppstår när du använder Azure Backup Server.

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter som har angetts 

Lös problemet så [felsökningen](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM agent coordinator-tjänsten på servern 

Lös problemet så [felsökningen](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues). 

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera registret metadata

Lös problemet så [felsökningen](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#installation-issues).




## <a name="installation-issues"></a>Problem med installation

| Åtgärd | Felinformation | Lösning |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera registret metadata. Den här uppdateringen kan komma att overusage av användningen av lagringsutrymme. Undvik detta genom att uppdatera registerposten ReFS trimning. | Justera registernyckeln **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ange värdet för Dword 1. |
|Installation | Det gick inte att uppdatera registret metadata. Den här uppdateringen kan komma att overusage av användningen av lagringsutrymme. Undvik detta genom att uppdatera registerposten SnapOptimization volym. | Skapa registernyckeln **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** med ett tomt värde. |
## <a name="registration-and-agent-related-issues"></a>Registrering och agent-relaterade problem
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Registrera till ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten. | Åtgärda det här felet: <ul><li> Hämta senaste autentiseringsuppgifter från valvet och försök igen. <br>(ELLER)</li> <li> Om den föregående åtgärden inte fungerade, försök att hämta autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv. <br>(ELLER)</li> <li> Försök att uppdatera datum- och tidsinställningar som beskrivs i [bloggen](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(ELLER)</li> <li> Kontrollera om c:\windows\temp har mer än 65000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekt i Temp-mappen. <br>(ELLER)</li> <li> Kontrollera status för certifikat. <br> a. Öppna **hantera datorcertifikat** (i Kontrollpanelen). <br> b. Expandera den **personliga** nod och dess underordnade nod **certifikat**.<br> c.  Ta bort certifikatet **verktyg för Windows Azure**. <br> d. Gör om registreringen i Azure Backup-klienten. <br> (ELLER) </li> <li> Kontrollera om någon Grupprincip är på plats. </li></ul> |
| Push-installation-agenter till skyddade servrar | Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM-agentkoordinatortjänsten på \<ServerName >. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du utföra följande steg**: <ul><li> Om du kopplar en dator från en ej betrodd domän, Följ [här](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (ELLER) </li><li> Om du kopplar en dator från en betrodd domän, felsöka med hjälp av stegen som beskrivs i [bloggen](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(ELLER)</li><li> Prova att inaktivera antivirusprogram som en felsökningsåtgärd. Om det löser problemet, ändra antivirus inställningarna enligt förslaget i [i den här artikeln](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |
| Push-installation-agenter till skyddade servrar | De autentiseringsuppgifter som har angetts för servern är ogiltig. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du utföra följande steg**: <br> Försök att installera skyddsagenten manuellt på produktionsservern som anges i [i den här artikeln](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup-agenten kunde inte ansluta till Azure Backup-tjänsten (ID: 100050) | Azure Backup-agenten kunde inte ansluta till Azure Backup-tjänsten. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du utföra följande steg**: <br>1. Kör följande kommando från en upphöjd kommandotolk: **psexec -i -s ”c:\Program c:\Program\Internet Explorer\iexplore.exe**. Internet Explorer-fönstret öppnas. <br/> 2. Gå till **verktyg** > **Internetalternativ** > **anslutningar** > **LAN-inställningar**. <br/> 3. Kontrollera proxyinställningarna för system-kontot. Ange Proxy-IP och port. <br/> 4. Stäng Internet Explorer.|
| Azure Backup Agent-installationen misslyckades | Microsoft Azure Recovery Services-installationen misslyckades. Alla ändringar som gjorts i systemet av Microsoft Azure Recovery Services-installationen återställdes. (ID: 4024) | Installera Azure-agenten manuellt.


## <a name="configuring-protection-group"></a>Konfigurera skydd för skyddsgruppen
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera skyddsgrupper | DPM kunde inte räkna upp PROGRAMKOMPONENTEN på den skyddade datorn (skyddat datornamn). | Välj **uppdatera** på skärmen Konfigurera skydd grupp Användargränssnittet på nivån datakälla-komponent. |
| Konfigurera skyddsgrupper | Det gick inte att konfigurera skydd | Om den skyddade servern är en SQL-server kontrollerar du att sysadmin-rollbehörighet har angetts till systemkontot (NTAuthority\System) på den skyddade datorn enligt beskrivningen i [i den här artikeln](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Konfigurera skyddsgrupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skyddsgruppen. | Diskar som läggs till i lagringspoolen [får inte innehålla en partition](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Ta bort alla befintliga volymer på diskarna. Lägg sedan till dem i lagringspoolen.|
| Principändring |Det gick inte att ändra principen för säkerhetskopiering. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen efter en stund har överskridits. Kontakta Microsoft support om problemet kvarstår. |**Orsak:**<br/>Det här felet uppstår under tre villkor: när säkerhetsinställningar har aktiverats vid försök att minska kvarhållningsintervallet under de lägsta värdena anges tidigare och när du är på en version som inte stöds. (Versioner som inte stöds är de under Microsoft Azure Backup Server version 2.0.9052 och Azure Backup Server uppdatering 1.) <br/>**Rekommenderad åtgärd:**<br/> Om du vill fortsätta med princip-relaterade uppdateringar, ange kvarhållningsperiod ovan minsta kvarhållning tidsperioden. (Den minsta bevarandeperioden är sju dagar för varje dag, fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år.) <br><br>Du kan också rekommenderas en annan metod är att uppdatera backup-agenten och Azure Backup-Server för att kunna utnyttja alla säkerhetsuppdateringar. |

## <a name="backup"></a>Säkerhetskopiering
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Säkerhetskopiering | Repliken är inkonsekvent | Kontrollera att alternativet Automatisk konsekvenskontroll kontroll i guiden skydd är aktiverat. Mer information om orsakerna till replik inkonsekvens och relevanta förslag finns i Microsoft TechNet-artikeln [repliken är inkonsekvent](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Kontrollera att Windows Server Backup är installerad på den skyddade servern vid säkerhetskopiering av systemtillstånd/BMR.</li><li> Kontrollera om utrymme-relaterade problem i DPM-lagringspoolen på DPM-/ Microsoft Azure Backup-Server och allokera lagringsutrymme som krävs.</li><li> Kontrollera tillståndet för tjänsten Volume Shadow Copy på den skyddade servern. Om den är i ett inaktiverat tillstånd måste ställas in att starta manuellt. Starta tjänsten på servern. Sedan gå tillbaka till konsolen för DPM-/ Microsoft Azure Backup-Server och starta synkroniseringen med konsekvenskontrolljobbet.</li></ol>|
| Säkerhetskopiering | Ett oväntat fel uppstod när jobbet kördes. Enheten är inte klar. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar kan du göra följande:** <br> <ul><li>Ange lagringsutrymmet för skuggkopian utrymme till obegränsad på objekten i skyddsgruppen och kör sedan konsekvenskontrollen.<br></li> (ELLER) <li>Försök att ta bort det befintliga skyddet grupp och skapa flera nya grupper. Varje ny skyddsgrupp ska innehålla ett enskilt objekt.</li></ul> |
| Säkerhetskopiering | Om du säkerhetskopierar endast systemtillstånd, kontrollerar du att det finns tillräckligt med ledigt diskutrymme på den skyddade datorn för att lagra säkerhetskopian av systemtillståndet. | <ol><li>Kontrollera att Windows Server Backup är installerad på den skyddade datorn.</li><li>Kontrollera att det finns tillräckligt med diskutrymme på den skyddade datorn för systemtillståndet. Det enklaste sättet att verifiera det här är att gå till den skyddade datorn, öppna Windows Server Backup, klicka dig igenom alternativen och väljer BMR. Användargränssnittet sedan anger du hur mycket utrymme som krävs. Öppna **WSB** > **lokal säkerhetskopia** > **schema för säkerhetskopiering** > **Välj Säkerhetskopieringskonfigurationen**  >  **Fullständig server** (storleken visas). Använd den här storleken för verifiering.</li></ol>
| Säkerhetskopiering | Det gick inte att skapa onlineåterställningspunkt | Om ett felmeddelande som säger ”Windows Azure Backup-agenten gick inte att skapa en ögonblicksbild av den valda volymen” försök att öka utrymmet i replik-och återställningspunktvolym.
| Säkerhetskopiering | Det gick inte att skapa onlineåterställningspunkt | Om felmeddelandet ”Windows Azure Backup-agenten inte kan ansluta till tjänsten OBEngine”, kan du kontrollera att OBEngine finns i listan över tjänster som körs på datorn. Om tjänsten OBEngine inte körs kan du använda kommandot ”net start OBEngine” för att starta tjänsten OBEngine.
| Säkerhetskopiering | Det gick inte att skapa onlineåterställningspunkt | Om ett felmeddelande som säger ”krypteringslösenfrasen för den här servern inte har angetts. Konfigurera krypteringslösenfrasen ”, försök att konfigurera krypteringslösenfrasen. Om den inte gör du följande: <br> <ol><li>Kontrollera att den virtuella platsen finns. Det här är den plats som anges i registret **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, med namnet **ScratchLocation** ska finnas.</li><li> Om det finns virtuella plats Försök registrera med hjälp av det gamla lösenordet. *När du konfigurerar krypteringslösenfrasen kan du spara den på en säker plats.*</li><ol>
| Säkerhetskopiering | Säkerhetskopieringen har misslyckats för BMR | Om BMR är stor, flytta vissa programfiler till OS-enhet och försök igen. |
| Säkerhetskopiering | Alternativet för att skydda igen en VMware VM på en ny Microsoft Azure Backup-Server visas inte som tillgängliga för att lägga till. | Egenskaper för VMware pekar på en gamla, Tillbakadragna instans av Microsoft Azure Backup-Server. Så här löser du problemet:<br><ol><li>I VCenter (SC-VMM-motsvarighet), går du till den **sammanfattning** fliken och sedan till **anpassade attribut som**.</li>  <li>Ta bort det gamla Microsoft Azure Backup Server-namnet från den **DPMServer** värde.</li>  <li>Gå tillbaka till den nya Microsoft Azure Backup-servern och ändra sida  När du har valt den **uppdatera** knappen, visas den virtuella datorn med en kryssruta som kan läggas till skydd.</li></ol> |
| Säkerhetskopiering | Fel vid åtkomst till filer/delade mappar | Försök att ändra inställningarna antivirusprogram som föreslås i TechNet-artikeln [köra antivirusprogram på DPM-servern](https://technet.microsoft.com/library/hh757911.aspx).|
| Säkerhetskopiering | Jobb för skapande av onlineåterställningspunkt för VMware VM misslyckas. Ett VMware-fel inträffade vid försök att hämta information om ChangeTracking. ErrorCode - FileFaultFault (ID 33621) |  <ol><li> Återställa CTK på VMware för de virtuella datorerna som påverkas.</li> <li>Kontrollera oberoende disken inte är på plats i VMware.</li> <li>Stoppa skydd för de berörda virtuella datorerna och skydda igen med den **uppdatera** knappen. </li><li>Kör en kopia för de virtuella datorerna som påverkas.</li></ol>|


## <a name="change-passphrase"></a>Ändra lösenfras
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändra lösenfras |Säkerhet PIN-kod som har angetts är felaktig. Ange rätt säkerhet PIN-kod för att slutföra åtgärden. |**Orsak:**<br/> Det här felet uppstår när du anger en ogiltig eller upphört att gälla säkerhet PIN-kod medan du utför en kritisk åtgärd (till exempel ändra en lösenfras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig säkerhet PIN-kod för att slutföra åtgärden. PIN-koden får logga in på Azure portal och gå till Recovery Services-valvet. Gå till **inställningar** > **egenskaper** > **generera säkerhet PIN-kod**. Använd den här PIN-kod för att ändra lösenfrasen. |
| Ändra lösenfras |Åtgärden misslyckades. ID: 120002 |**Orsak:**<br/>Det här felet uppstår när säkerhetsinställningar har aktiverats, eller när du försöker ändra lösenfrasen när du använder en version som inte stöds.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfrasen måste du uppdatera backup-agenten till den lägsta versionen som är 2.0.9052. Du måste också uppdatera Azure Backup Server till minst update 1 och ange en giltig säkerhet PIN-kod. PIN-koden får logga in på Azure portal och gå till Recovery Services-valvet. Gå till **inställningar** > **egenskaper** > **generera säkerhet PIN-kod**. Använd den här PIN-kod för att ändra lösenfrasen. |


## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera e-postmeddelanden med hjälp av Office 365-konto |Fel-ID: 2013| **Orsak:**<br> Försöker använda Office 365-konto <br>**Rekommenderad åtgärd:**<ol><li> Det första så är ”Tillåt anonyma Relay på ett får Connector” för DPM-servern har konfigurerats på Exchange. Mer information om hur du konfigurerar detta finns [Tillåt anonyma Relay på en koppling som tar emot](http://technet.microsoft.com/en-us/library/bb232021.aspx) på TechNet.</li> <li> Om du inte använder en intern SMTP relay och måste ställa in med hjälp av Office 365-server, kan du konfigurera IIS att ett relä. Konfigurera DPM-servern ska [SMTP till O365 med hjälp av IIS-relä](https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx).<br><br> **Viktigt:** måste du använda den user@domain.com format och *inte* domän\användare.<br><br><li>Punkt DPM ska använda det lokala servernamnet som SMTP-servern port 587. Styr det till i användarens e-postadress som e-postmeddelanden ska hämtas från.<li> Användarnamn och lösenord på installationssidan DPM SMTP ska för ett domänkonto i den domän som DPM finns på. </li><br> **Obs**: när du ändrar SMTP-serveradressen kan göra ändringar i de nya inställningarna, Stäng dialogrutan Inställningar och öppna den så att den återspeglar det nya värdet.  Bara ändra och testa kanske inte alltid orsakar de nya inställningarna ska träda i kraft så att testa det här sättet är bästa praxis.<br><br>När som helst under den här processen kan du rensa inställningarna genom att stänga DPM-konsolen och redigera följande registernycklar: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> ta bort SMTPPassword och SMTPUserName nycklar**. Du kan lägga till dem till Användargränssnittet när du startar den igen.
