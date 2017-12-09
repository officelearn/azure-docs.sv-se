---
title: "Felsöka Azure Backup-Server | Microsoft Docs"
description: "Felsöka installationen registrering av Azure Backup-Server och säkerhetskopiering och återställning av arbetsbelastningar för program"
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
ms.openlocfilehash: 696f86f616575364bb65021260daf0c8458fc4e9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Du kan felsöka fel påträffades när på Azure Backup Server med information som anges i följande tabell.

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter som har angetts 

Följ dessa [felsökningssteg] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) för att lösa problemet.

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM Agent Coordinator-tjänsten på servern 

Följ dessa [felsökningssteg] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) för att lösa problemet.

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera registret metadata

Följ dessa [felsökningssteg] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) för att lösa problemet.


## <a name="installation-issues"></a>Problem med installation

| Åtgärd | Felinformation | Lösning |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera registret metadata. Den här uppdateringen kan komma att över användning av användningen av lagringsutrymme. Om du vill undvika detta genom att uppdatera registerposten ReFS trimning. | Justera registernyckeln SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Ange värdet för Dword 1. |
|Installation | Det gick inte att uppdatera registret metadata. Den här uppdateringen kan komma att över användning av användningen av lagringsutrymme. Om du vill undvika detta genom att uppdatera registerposten SnapOptimization volym. | Skapa registernyckeln SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds med ett tomt värde. |

## <a name="registration-and-agent-related-issues"></a>Problem som rör registrering och Agent
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Registrera till ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som hör till återställningstjänsten | Åtgärda det här felet: <ol><li> Hämta senaste autentiseringsuppgifter från valvet och försök igen <br>(ELLER)</li> <li> Om instruktionen ovan inte fungerar, försök att hämta autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv <br>(ELLER)</li> <li> Försök att uppdatera datum och tid som anges i [bloggen](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/) <br>(ELLER)</li> <li> Kontrollera om c:\windows\temp har mer än 65000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekt i Temp-mappen <br>(ELLER)</li> <li> Kontrollera status för certifikat <br> a. Öppna ”hantera certifikat” (i Kontrollpanelen) <br> b. Expandera noden ”privat” och dess underordnade nod ”certifikat” <br> c.  Ta bort certifikat ”verktyg för Windows Azure” <br> d. Gör om registreringen i Azure Backup-klienten <br> (ELLER) </li> <li> Kontrollera om någon Grupprincip är på plats </li></ol> |
| Push-installation-agenter till skyddade servrar | Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM-agentkoordinatortjänsten på \<servernamn > | **Den rekommenderade åtgärden visas i produkten inte fungerar om**, <ol><li> Om du kopplar en dator från en ej betrodd domän, Följ [dessa](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) steg <br> (ELLER) </li><li> Om du kopplar en dator från en betrodd domän, felsöka med hjälp av stegen som beskrivs i [bloggen](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/) <br>(ELLER)</li><li> Prova att inaktivera antivirusprogram som en felsökningsåtgärd. Om det löser problemet, ändra Antivirus inställningarna enligt förslaget i [den här artikeln] (https://technet.microsoft.com/library/hh757911.aspx)</li></ol> |
| Push-installation-agenter till skyddade servrar | Autentiseringsuppgifterna som angetts för servern är ogiltiga | **Den rekommenderade åtgärden visas i produkten inte fungerar om**, <br> Försök att installera skyddsagenten manuellt på produktionsservern som anges i [i den här artikeln](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)|
| Azure Backup-agenten kunde inte ansluta till Azure Backup-tjänsten (ID: 100050) | Azure Backup-agenten kunde inte ansluta till Azure Backup-tjänsten. | **Den rekommenderade åtgärden visas i produkten inte fungerar om**, <br>1. Kör följande kommando från en upphöjd kommandoprompt, psexec -i -s ”c:\Program c:\Program\Internet Explorer\iexplore.exe” internet explorer-fönstret öppnas. <br/> 2. Gå till Verktyg -> Internet-alternativ -> anslutningar LAN-inställningar ->. <br/> 3. Kontrollera proxy-inställningar för System-kontot. Ange Proxy-IP och port. <br/> 4. Stäng Internet Explorer.|
| Azure Backup Agent-installationen misslyckades | Microsoft Azure Recovery Services-installationen misslyckades. Alla ändringar som gjorts av Microsoft Azure Recovery Services-installation i systemet återställdes. (ID: 4024) | Installera Azure-agenten manuellt


## <a name="configuring-protection-group"></a>Konfigurera skydd för skyddsgruppen
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera skyddsgrupper | DPM kunde inte räkna upp PROGRAMKOMPONENTEN på den skyddade datorn (skyddat datornamn) | Klicka på ”Uppdatera” på skärmen Konfigurera skydd grupp Användargränssnittet på nivån datakälla-komponent |
| Konfigurera skyddsgrupper | Det gick inte att konfigurera skydd | Kontrollera om den skyddade servern är en SQLServer om sysadmin-rollbehörighet har angetts till systemkontot (NTAuthority\System) på den skyddade datorn som anges i [i den här artikeln](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)
| Konfigurera skyddsgrupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skyddsgruppen | Diskar som har lagts till i lagringspoolen [får inte innehålla en partition](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Ta bort alla befintliga volymer på diskarna och lägger sedan till den i lagringspoolen|
| Principändring |Det gick inte att ändra principen för säkerhetskopiering. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen om en stund. Kontakta Microsoft-supporten om problemet kvarstår. |**Orsak:**<br/>Det här felet kommer när säkerhetsinställningar har aktiverats, försök att minska kvarhållningsintervallet under de lägsta värdena som anges ovan och du är på version som inte stöds (under MAB version 2.0.9052 och Azure Backup serveruppdatering 1). <br/>**Rekommenderad åtgärd:**<br/> I det här fallet bör du ställa in kvarhållningsperioden ovan minsta kvarhållning tidsperioden (sju dagar för varje dag, fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år) för att fortsätta med princip-relaterade uppdateringar. Du kan också är föredragen metod att uppdatera backup-agenten och Azure Backup-Server för att kunna utnyttja alla säkerhetsuppdateringar. |

## <a name="backup"></a>Säkerhetskopiering
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Säkerhetskopiering | Repliken är inkonsekvent | Kontrollera att automatisk konsekvenskontroll alternativ i guiden är aktiverad för Skyddsgruppen. Mer information om orsakerna till replik inkonsekvens och relevanta förslag [här](https://technet.microsoft.com/library/cc161593.aspx) <br> <ol><li> Vid säkerhetskopiering av systemtillstånd/BMR, kontrollera om Windows Server Backup är installerat eller inte på skyddad Server </li><li> Kontrollera diskutrymme relaterade problem på DPM-lagring på servern DPM/MABS-poolen och allokera lagringsutrymme som krävs </li><li> Kontrollera tillståndet för tjänsten Volume shadow copy på den skyddade servern. Om den är i inaktiverat tillstånd ställas in att starta manuellt och starta tjänsten på servern. Sedan gå tillbaka till konsolen DPM/MABS och starta synkroniseringen med konsekvenskontroll.</li></ol>|
| Säkerhetskopiering | Ett oväntat fel uppstod när jobbet kördes, enheten är inte klar | **Den rekommenderade åtgärden visas i produkten inte fungerar om**, <br> <ol><li>Ange lagringsutrymmet för skuggkopian utrymme till obegränsad på objekten i skyddsgruppen och kör konsekvenskontrollen <br></li> (ELLER) <li>Försök att ta bort den befintliga skyddsgruppen och skapa flera nya – ett med varje enskilt objekt i den</li></ol> |
| Säkerhetskopiering | Om du säkerhetskopierar endast systemtillstånd, kontrollerar du om det finns tillräckligt med ledigt utrymme på den skyddade datorn för att lagra säkerhetskopian av systemtillstånd | <ol><li>Kontrollera att WSB på den skyddade datorn är installerad</li><li>Kontrollera att det finns tillräckligt med utrymme finns på den skyddade datorn för systemtillstånd: Det enklaste sättet att göra detta är att gå till den skyddade datorn öppna WSB och klicka dig igenom alternativen och väljer BMR. Användargränssnittet visar hur mycket utrymme som krävs för detta. Öppna WSB -> lokal säkerhetskopia -> säkerhetskopiering schema -> Välj konfigurationen för säkerhetskopiering -> hela servern (storleken visas). Använd den här storleken för verifiering.</li></ol>
| Säkerhetskopiering | Det gick inte att skapa onlineåterställningspunkt | Om ett felmeddelande som säger ”Windows Azure Backup-agenten gick inte att skapa en ögonblicksbild av den valda volymen”, försök att öka utrymmet i replik-och återställningspunktvolym.
| Säkerhetskopiering | Det gick inte att skapa onlineåterställningspunkt | Om ett felmeddelande som säger Kontrollera ”Windows Azure Backup-agenten inte kan ansluta till tjänsten OBEngine”, att OBEngine finns i listan över tjänster som körs på datorn. Om tjänsten OBEngine inte körs kan du använda kommandot ”net start OBEngine” för att starta tjänsten OBEngine.
| Säkerhetskopiering | Det gick inte att skapa onlineåterställningspunkt | Om ett felmeddelande som säger ”krypteringslösenfrasen för den här servern inte har angetts. Konfigurera krypteringslösenfrasen ”försök att konfigurera krypteringslösenfrasen. Om det misslyckas <br> <ol><li>Kontrollera om den virtuella platsen finns eller inte. Den plats som anges i registernyckeln HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config med namnet ”ScratchLocation” ska finnas.</li><li> Om det finns virtuella plats Försök registrera med det gamla lösenordet. **När du konfigurerar krypteringslösenfrasen kan du spara den på en säker plats**</li><ol>
| Säkerhetskopiering | Säkerhetskopieringen har misslyckats för BMR | Om storleken på BMR är stort, försök igen efter att flytta vissa programfiler till OS-enhet |
| Säkerhetskopiering | Skydda VMWare VM igen på en ny server MABS visas inte som tillgängliga för att lägga till. | Egenskaper för VMWare pekar på en gamla tillbakadragna MABS-server. Lös problemet: I VCenter (SC-VMM motsvarande), gå till fliken 'sammanfattning- och anpassade attribut.  Ta bort gamla MABS servernamnet från 'DPMServer'-värde.  Gå tillbaka till den nya MABS-servern och ändra sida  När du använder på uppdateringsknappen visas den virtuella datorn en kryssruta som kan läggas till skydd. |
| Säkerhetskopiering | Fel vid åtkomst till filer/delade mappar | Försök att ändra antivirus inställningarna enligt förslaget [här](https://technet.microsoft.com/library/hh757911.aspx)|
| Säkerhetskopiering | Det går inte att skapa en onlineåterställningspunkt för VMware-VM. VMware-fel inträffade vid försök att hämta information om ChangeTracking. ErrorCode - FileFaultFault (ID 33621) |  1. Återställa ctk på VMWare, för de virtuella datorerna som påverkas <br/> 2. Kontrollera att oberoende disken inte är på plats i VMWare <br/> 3. Stoppa skydd för de berörda virtuella datorerna och skydda nytt med knappen Uppdatera <br/> 4. Kör en kopia för de virtuella datorerna som påverkas|


## <a name="change-passphrase"></a>Ändra lösenfras
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändra lösenfras |Säkerhet angiven PIN-kod är felaktigt. Ange rätt säkerhet PIN-koden för att slutföra den här åtgärden. |**Orsak:**<br/> Det här felet kommer när du anger ogiltiga eller utgångna säkerhet PIN-kod under kritiska åtgärd (t.ex. Ändra lösenfras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig säkerhet PIN-kod för att slutföra åtgärden. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valv > Inställningar > Egenskaper > Generera säkerhet PIN-kod. Använd den här PIN-kod för att ändra lösenfras. |
| Ändra lösenfras |Åtgärden misslyckades. ID: 120002 |**Orsak:**<br/>Det här felet kommer när säkerhetsinställningar har aktiverats, försök att ändra lösenfras och du är på version som inte stöds.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfrasen måste du uppdatera backup-agenten till minimiversionen minsta 2.0.9052 och Azure Backup server till minsta update 1, och ange sedan giltig säkerhet PIN-kod. PIN-koden får logga in på Azure-portalen och gå till Recovery Services-valv > Inställningar > Egenskaper > Generera säkerhet PIN-kod. Använd den här PIN-kod för att ändra lösenfras. |


## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar
| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Försök att konfigurera e-postmeddelanden med hjälp av Office 365-konto. | hämtar fel-ID: 2013| **Orsak:**<br/> Försöker använda Office 365-konto <br/> **Rekommenderad åtgärd:**<br/> 1. Det första du ska kontrollera är att ”tillåta anonym Relay på ett får Connector” för DPM-servern har konfigurerats på Exchange. Här är [en länk](http://technet.microsoft.com/en-us/library/bb232021.aspx) på hur du konfigurerar detta.  <br/> 2. Om du inte använder en intern SMTP relay och måste du ställa in med ditt Office 365-server, kan du konfigurera IIS ska vara ett relä för detta. Du måste konfigurera DPM-servern så att [kunna vidarebefordra SMTP till O365 med hjälp av IIS](https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx) <br/><br/> Viktigt meddelande: I steg 3 g -> -> ii, måste du använda user@domain.com format och inte domän\användare <br/> Punkt DPM ska använda lokala servername som SMTP-servern, port 587 och sedan på användarens e-postadress som e-postmeddelanden ska hämtas från. <br/> Användarnamn och lösenord på installationssidan DPM SMTP ska vara ett domänkonto i DPM finns på domänen. <br/><br/> Obs: När ändra SMTP-serveradressen göra ändringar i nya inställningar, Stäng dialogrutan Inställningar och öppna så att den återspeglar det nya värdet.  Bara ändra och testa kan inte alltid ta de nya inställningarna så att testa det här sättet är bästa praxis. <br/><br/> När som helst under den här processen, kan du rensa inställningarna genom att stänga DPM-konsolen och redigera följande registernycklar:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Ta bort SMTPPassword och SMTPUserName nycklar. <br/> Du kan lägga till dem i Användargränssnittet när du startar den igen.
