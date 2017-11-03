---
title: "Felsöka Azure filsynkronisering (förhandsversion) | Microsoft Docs"
description: "Felsök vanliga problem med synkronisering av Azure-fil"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Felsöka Azure filsynkronisering (förhandsgranskning)
Med Azure File Sync (förhandsversionen) kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln är utformat för att hjälpa dig att felsöka och lösa problem som kan uppstå med Azure filsynkronisering distributionen. Som visar den här guiden hur du kan samla in viktig loggar från systemet för att underlätta en mer ingående undersökning av problem. Om du inte hittar svar på din fråga här kan Tveka inte att nå oss via följande kanaler (i växande ordning):

1. I avsnittet kommentarer i den här artikeln.
2. [Azure Storage-Forum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft Support: Om du vill skapa ett nytt supportärende, gå till ”hjälp + support” på Azure portal och klicka på ”ny supportbegäran”.

## <a name="agent-installation-and-server-registration"></a>Agentregistreringen för installation och server
<a id="agent-installation-failures"></a>**Felsökning av agentinstallationsproblem**  
Om installationen av Azure filsynkronisering misslyckas, kör du följande kommando från en upphöjd kommandotolk för att aktivera loggning under agentinstallationen:

```
StorageSyncAgent.msi /l*v Installer.log
```

När installationen misslyckas, kan du läsa installer.log om du vill ta reda på orsaken. 

> [!Note]  
> Agentinstallationen misslyckas om du väljer för att använda Microsoft Update och Windows Update-tjänsten körs inte.

<a id="server-registration-missing"></a>**Servern visas under registrerade servrar i Azure-portalen**  
Om en server inte visas under registrerade servrar för en tjänst för synkronisering av lagring, utför följande steg:
1. Logga in på den server som du vill registrera.
2. Öppna Utforskaren och navigera till installationskatalogen för lagring Sync Agent (standardsökvägen är `C:\Program Files\Azure\StorageSyncAgent`). 
3. Kör ServerRegistration.exe och Följ guiden för att registrera servern med en Storage-synkroniseringstjänsten.

<a id="server-already-registered"></a>**Registrera servern visas följande meddelande när du har installerat agenten Azure filsynkronisering: ”den här servern är redan registrerad”**  
![En skärmbild av dialogrutan registrera servern med felet ”servern är redan registrerad” meddelande](media/storage-sync-files-troubleshoot/server-registration-1.png)

Det här meddelandet visas om servern tidigare har registrerats med en tjänst för synkronisering av lagring. Följ stegen för att för att avregistrera servern med den aktuella synkroniseringstjänsten för lagring och registrera med en ny lagring synkroniseringstjänsten [Avregistrerar en server med Azure filsynkronisering](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Om servern inte finns med under registrerade servrar i synkroniseringstjänsten för lagring, kör du följande PowerShell-kommandon på den server som du vill avregistrera:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Om servern ingår i ett kluster, det är en valfri `Reset-StorageSyncServer -CleanClusterRegistration` parameter som kommer också att ta bort registreringen av klustret.

<a id="web-site-not-trusted"></a>**När du registrerar en server visas flera ”webbplats inte betrott” svar varför?**  
Det här felet beror på **Förbättrad säkerhetskonfiguration i Internet Explorer** principen aktiveras under registreringen av servern. Mer information om hur du inaktiverar korrekt den **Förbättrad säkerhetskonfiguration i Internet Explorer** princip, se [förbereda Windows-servrar för användning med Azure filsynkronisering](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync) och [hur du distribuerar Azure File Synkronisera (förhandsgranskning)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Synkronisera grupphantering
<a id="cloud-endpoint-using-share"></a>**Moln slutpunkt skapas misslyckas med följande fel: ”den angivna Azure-filresursen används redan av en annan CloudEndpoint”**  
Det här felet uppstår om Azure-filresursen används redan av en annan slutpunkt i molnet. 

Om du får felet och Azure-filresurs inte är för närvarande används av en Molnslutpunkt, utför du stegen nedan för att rensa metadata filsynkronisering Azure på Azure-filresursen:

> [!Warning]  
> Om du tar bort metadata i en Azure-filresurs som för närvarande används av en molnslutpunkt uppstår Azure filen synkroniseringsåtgärder misslyckas. 

1. Gå till din Azure-filresurs i Azure Portal.  
2. Högerklicka på Azure-filresurs och välj **redigera metadata**
3. Högerklicka på SyncService och välj **ta bort**.

<a id="cloud-endpoint-authfailed"></a>**Moln slutpunkt skapas misslyckas med felkoden: AuthorizationFailed**  
Det här problemet uppstår om ditt konto inte har tillräcklig behörighet för att skapa en Molnslutpunkt. 

Om du vill skapa en Molnslutpunkt måste ditt användarkonto ha Microsoft Authorization följande behörigheter:  
* Läs: Hämta rolldefinitionen
* Skriv: Skapa eller uppdatera anpassad rolldefinition
* Läs: Hämta rolltilldelningen
* Skriv: Skapa rolltilldelning

Följande inbyggda roller har Microsoft Authorization behörighet:  
* Ägare
* Administratör för användaråtkomst

För att fastställa om din användarroll för kontot har behörighet att utföra följande:  
* Klicka på **resursgrupper** i Azure-portalen
* Markera den resursgrupp där lagringskontot finns och klicka på **åtkomstkontroll (IAM)**
* Klicka på den **rollen** (t.ex. ägare, deltagare) för ditt konto.
* I den **Resursprovidern** väljer **Microsoft Authorization** 
* **Rolltilldelningen** ska ha **Läs** och **skrivbehörighet**
* **Rolldefinitionen** ska ha **Läs** och **skrivbehörighet**

<a id="cloud-endpoint-deleteinternalerror"></a>**Slutpunkten borttagningen misslyckas med felkoden: MgmtInternalError**  
Det här problemet kan inträffa om Azure File filresurs- eller kontot har tagits bort innan du tar bort slutpunkten molnet. Det här problemet korrigeras i en kommande uppdatering och moln-slutpunkten kan tas bort.

Förhindra det här problemet genom att ta bort Molnslutpunkt innan du tar bort kontot Azure File share eller lagring.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Jag har skapat en fil direkt i min Azure-filresursen via SMB eller via portalen. Hur lång tid tills filen har synkroniserats till servrar i gruppen synkronisering?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Felsökning av synkronisering fungerar inte på en server**  
Om synkroniseringen misslyckas på en server kan du utföra följande:
- Kontrollera Server-Endpoint finns i Azure-portalen för den katalog som du vill synkronisera till en filresurs i Azure:
    
    ![En skärmbild av en grupp för synkronisering med både en moln- och Server Endpoint i Azure-portalen](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- Granska händelseloggarna för drift och diagnostik i Loggboken, finns under `Applications and Services\Microsoft\FileSync\Agent`.
- Bekräfta att servern är ansluten till internet.
- Kontrollera Azure filsynkronisering tjänsten körs på servern genom att öppna snapin-modulen tjänster i MMC och Sync-Agenttjänsten för lagring (FileSyncSvc) körs.

<a id="replica-not-ready"></a>**Synkroniseringen misslyckas med felkoden: 0x80c8300f - replikeringen inte är redo att utföra åtgärden krävs**  
Det här felet är förväntat om du skapar en Molnslutpunkt och använder en Azure-filresurs som innehåller data. När identifiering av ändring av har slutförts på Azure-filresursen (kan ta upp till 24 timmar) bör starta synkronisering fungerar korrekt.

<a id="broken-sync-files"></a>**Felsökning av enskilda filer inte kan synkronisera**  
Om enskilda filer misslyckas för synkronisering kan du utföra följande:
- Granska händelseloggarna för drift och diagnostik under `Applications and Services\Microsoft\FileSync\Agent` i Loggboken
- Kontrollera att det finns inga öppna referenser i filen
    - Obs: Azure filsynkronisering regelbundet tar VSS-ögonblicksbilder för att synkronisera filer med öppna referenser

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
<a id="files-fail-tiering"></a>**Felsökning av filer som inte tjänstnivån**  
Om filerna inte tjänstnivån till Azure Files, utför följande steg:

- Kontrollera att filen finns i Azure-filresursen
    - Obs: En fil måste synkroniseras till en Azure-filresursen innan den kan nivåindelas
- Granska drift och diagnostik händelseloggarna, finns under `Applications and Services\Microsoft\FileSync\Agent` i Loggboken
- Bekräfta att servern är ansluten till internet 
- Verifiera Azure filen Sync-filterdrivrutiner (StorageSync.sys & StorageSyncGuard.sys) körs
    - Öppna en upphöjd kommandotolk, kör `fltmc` och kontrollera de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas

<a id="files-fail-recall"></a>**Felsökning av filer som inte kan återställas**  
Om filerna inte återställas, utför följande steg:
- Granska drift och diagnostik händelseloggarna, finns under `Applications and Services\Microsoft\FileSync\Agent` i Loggboken
- Kontrollera filen eller filerna finns i Azure-filresurs
- Bekräfta att servern är ansluten till internet 
- Verifiera Azure filen Sync-filterdrivrutiner (StorageSync.sys & StorageSyncGuard.sys) körs
    - Öppna en upphöjd kommandotolk, kör `fltmc` och kontrollera de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas

<a id="files-unexpectedly-recalled"></a>**Felsökning av filer som oväntat återställdes på en server**  
Ett virusskyddsprogram, säkerhetskopiering och andra program som läser stort antal filer kommer att orsaka oönskade återställningar om de inte respekterar attributet offline och hoppa över läsa innehållet i filerna. Hoppar över offlinefiler för de produkter som stöder detta hjälper till att undvika oönskad återställningar när du utför åtgärder som till exempel antivirusprogram genomsökningar eller säkerhetskopieringsjobb.

Kontakta programvaruleverantören om hur du konfigurerar sitt lösning för att hoppa över läsa offlinefiler.

Ytterligare kan områdena återställning inträffa i andra scenarier som kan läsa filer i Utforskaren. Öppna en mapp med molnet nivåer filer i Utforskaren på servern kan resultera i oönskade återställningar mer om ett virusskyddsprogram är aktiverad på servern.

## <a name="general-troubleshooting"></a>Allmän felsökning
Om du får problem med synkronisering av Azure-filen på en server måste du starta genom att utföra följande:
- Granska händelseloggarna i Loggboken för diagnostik- och drift
    - Synkronisera skiktning, och återkalla problem loggas i händelseloggarna diagnostik- och operativa under`Applications and Services\Microsoft\FileSync\Agent`
    - Problem med att hantera en server (till exempel konfigurationsinställningar) loggas i händelseloggarna diagnostik- och operativa under`Applications and Services\Microsoft\FileSync\Management`
- Verifiera Azure filen Sync-tjänsten körs på servern
    - Öppna snapin-modulen tjänster i MMC och verifiera Sync-Agenttjänsten för lagring (FileSyncSvc) körs
- Verifiera Azure filen Sync-filterdrivrutiner (StorageSync.sys & StorageSyncGuard.sys) körs
    - Öppna en upphöjd kommandotolk, kör fltmc och kontrollera de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas

Om problemet kvarstår när du har utfört stegen ovan, kör du verktyget AFSDiag genom att utföra följande steg:
1. Skapa en katalog som ska användas för att spara AFSDiag utdata (till exempel c:\output).
2. Öppna en kommandotolk med PowerShell och kör följande kommandon (träffar ange efter varje kommando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. För Azure filsynkronisering kernel-läge trace-nivån, ange 1 (såvida inte anges för att skapa mer utförlig spårningar) och träffar.
4. För Azure filsynkronisering användaren läge trace-nivån, ange 1 (såvida inte anges för att skapa mer utförlig spårningar) och träffar.
5. Återskapa problemet och tryck på D när du är klar.
6. En .zip-fil som innehåller loggfiler och spårningsfiler ska finnas i den angivna katalogen.

## <a name="see-also"></a>Se även
- [Vanliga och frågor svar om Azure-filer](storage-files-faq.md)
- [Felsökning av problem med Azure-filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsökning av problem med Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
