---
title: "Felsöka Azure filsynkronisering (förhandsversion) | Microsoft Docs"
description: "Felsöka vanliga problem med synkronisering av Azure-filen."
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
ms.openlocfilehash: 265c5f660c4bee53a2faf4a073384587eb3f65fc
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Felsöka Azure filsynkronisering (förhandsgranskning)
Använda Azure filsynkronisering (förhandsgranskning) för att centralisera din organisations filresurser i Azure-filer, samtidigt som flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure filsynkronisering omvandlar Windows Server till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt data lokalt, inklusive SMB och NFS FTPS. Du kan ha valfritt antal cacheminnen som du behöver över hela världen.

Den här artikeln är utformat för att hjälpa dig att felsöka och lösa problem som kan uppstå med Azure filsynkronisering distributionen. Vi beskriver också hur du samlar in viktiga loggar från systemet om det krävs en mer ingående undersökning av problemet. Om du inte hittar svar på din fråga kan kontakta du oss via följande kanaler (i växande ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [Forum för Azure Storage](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata).
3. [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft-supporten. Att skapa en ny supportförfrågan i Azure-portalen på den **hjälp** väljer den **hjälp + support** och välj sedan **ny supportbegäran**.

## <a name="agent-installation-and-server-registration"></a>Agentregistreringen för installation och server
<a id="agent-installation-failures"></a>**Felsöka agentinstallationsproblem**  
Om installationen av Azure filsynkronisering misslyckas vid en upphöjd kommandotolk kör du följande kommando för att aktivera loggning under agentinstallationen:

```
StorageSyncAgent.msi /l*v Installer.log
```

Granska installer.log om du vill ta reda på orsaken till att installationen misslyckades. 

> [!Note]  
> Agentinstallationen misslyckas om datorn har konfigurerats att använda Microsoft Update och Windows Update-tjänsten körs inte.

<a id="server-registration-missing"></a>**Servern visas under registrerade servrar i Azure-portalen**  
Om en server inte visas **registrerade servrar** för en tjänst för synkronisering av lagring:
1. Logga in på den server som du vill registrera.
2. Öppna Utforskaren och gå till installationskatalogen lagring Sync Agent (standardsökvägen är C:\Program Files\Azure\StorageSyncAgent). 
3. Kör ServerRegistration.exe och slutför guiden för att registrera servern med en Storage-synkroniseringstjänsten.

<a id="server-already-registered"></a>**Registrera servern visas följande meddelande under Azure filsynkronisering agentinstallationen: ”den här servern är redan registrerad”** 

![En skärmbild av dialogrutan registrera servern med felet ”servern är redan registrerad” meddelande](media/storage-sync-files-troubleshoot/server-registration-1.png)

Detta meddelande visas om servern tidigare har registrerats med en tjänst för synkronisering av lagring. För att avregistrera servern från den aktuella synkroniseringstjänsten för lagring och registrera sedan med en ny lagring-synkroniseringstjänsten måste slutföra stegen som beskrivs i [Avregistrerar en server med Azure filsynkronisering](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Om servern inte finns med **registrerade servrar** i synkroniseringstjänsten lagring på den server som du vill avregistrera, kör följande PowerShell-kommandon:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Om servern är en del av ett kluster, kan du använda det valfria *Återställ StorageSyncServer - CleanClusterRegistration* att även ta bort registreringen för klustret.

<a id="web-site-not-trusted"></a>**När jag registrera en server visas flera ”webbplats inte betrott” svar. Varför?**  
Det här problemet uppstår när den **Förbättrad säkerhetskonfiguration i Internet Explorer** principen aktiveras under registreringen av servern. Mer information om hur du inaktiverar korrekt den **Förbättrad säkerhetskonfiguration i Internet Explorer** princip, se [förbereda Windows Server till Azure filsynkronisering](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) och [hur du distribuerar Azure File Synkronisera (förhandsgranskning)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Synkronisera grupphantering
<a id="cloud-endpoint-using-share"></a>**Molnet Endpoint skapa misslyckas med felet: ”angivna Azure filresursen används redan av en annan CloudEndpoint”**  
Det här problemet uppstår om Azure-filresursen används redan av en annan slutpunkt i molnet. 

Om du ser det här meddelandet och Azure-filresursen är för närvarande inte används av en Molnslutpunkt, Slutför följande steg om du vill rensa metadata Azure filsynkronisering i Azure-filresursen:

> [!Warning]  
> Ta bort metadata i en Azure-filresurs som för närvarande används av en Molnslutpunkt gör att Azure-filen synkroniseringsåtgärder misslyckas. 

1. Gå till Azure-filresursen i Azure-portalen.  
2. Högerklicka på Azure-filresursen och välj sedan **redigera metadata**.
3. Högerklicka på **SyncService**, och välj sedan **ta bort**.

<a id="cloud-endpoint-authfailed"></a>**Molnet Endpoint skapa misslyckas med felet: ”AuthorizationFailed”**  
Det här problemet uppstår om ditt konto inte har tillräcklig behörighet för att skapa en Molnslutpunkt. 

Om du vill skapa en Molnslutpunkt måste ditt användarkonto ha Microsoft Authorization följande behörigheter:  
* Läs: Hämta rolldefinitionen
* Skriv: Skapa eller uppdatera anpassad rolldefinition
* Läs: Hämta rolltilldelningen
* Skriv: Skapa rolltilldelning

Följande inbyggda roller har behörigheterna som krävs Microsoft Authorization:  
* Ägare
* Administratör för användaråtkomst

Att avgöra om din användarroll konto har behörigheterna som krävs:  
1. Välj i Azure-portalen **resursgrupper**.
2. Markera den resursgrupp där lagringskontot finns och välj sedan **åtkomstkontroll (IAM)**.
3. Välj den **rollen** (till exempel ägare eller deltagare) för ditt konto.
4. I den **Resursprovidern** väljer **Microsoft Authorization**. 
    * **Rolltilldelningen** ska ha **Läs** och **skriva** behörigheter.
    * **Rolldefinitionen** ska ha **Läs** och **skriva** behörigheter.

<a id="cloud-endpoint-deleteinternalerror"></a>**Molnet Endpoint borttagningen misslyckas med felet: ”MgmtInternalError”**  
Det här problemet kan inträffa om Azure file filresurs- eller kontot har tagits bort innan du tar bort slutpunkten molnet. Det här problemet korrigeras i en kommande uppdatering. Du kommer att kunna ta bort en Molnslutpunkt när du tar bort det Azure file share eller lagring som helst.

Under tiden för att förhindra det här problemet kan ta bort molnet slutpunkten innan du tar bort kontot Azure file share eller lagring.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Om jag har skapat en fil direkt i min Azure-filresursen via SMB eller via portalen hur lång tid det tar för filen som synkroniseras till servrar i gruppen synkronisering?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Synkronisering misslyckas på en server**  
Om synkroniseringen misslyckas på en server:
1. Kontrollera att det finns en Server-slutpunkt i Azure-portalen för den katalog som du vill synkronisera till en Azure filresurs:
    
    ![En skärmbild av en grupp för synkronisering med både en Molnslutpunkt och en slutpunkt för Server i Azure-portalen](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. Granska de operativa och diagnostik händelseloggar, finns under program- och Services\Microsoft\FileSync\Agent i Loggboken.
    1. Kontrollera att servern är ansluten till internet.
    2. Kontrollera att Azure filen Sync-tjänsten körs på servern. Öppna snapin-modulen tjänster i MMC för att göra detta, och kontrollera att lagring Sync Agent-tjänsten (FileSyncSvc) körs.

<a id="replica-not-ready"></a>**Synkroniseringen misslyckas med felet: ”0x80c8300f - repliken är inte redo att utföra nödvändiga åtgärden”**  
Det här problemet är förväntat om du skapar en Molnslutpunkt och använda en Azure-filresurs som innehåller data. När ändringen identifiering jobbet har slutförts körs på Azure-filresursen (det kan ta upp till 24 timmar) ska starta synkronisering fungerar korrekt.

<a id="broken-sync-files"></a>**Felsökning av enskilda filer som inte kan synkronisera**  
Om det inte går att synkronisera enskilda filer:
1. Granska de operativa och diagnostik händelseloggar, finns under program- och Services\Microsoft\FileSync\Agent i Loggboken.
2. Kontrollera att det finns inga öppna referenser i filen.

    > [!NOTE]
    > Azure filsynkronisering tar regelbundet VSS-ögonblicksbilder för att synkronisera filer som har öppna referenser.

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
<a id="files-fail-tiering"></a>**Felsökning av filer som inte tjänstnivån**  
Om filerna inte tjänstnivån till Azure Files:

1. Kontrollera att filerna finns i Azure-filresursen.

    > [!NOTE]
    > En fil måste synkroniseras till en Azure-filresursen innan den kan nivåindelas.
2. Granska de operativa och diagnostik händelseloggar, finns under program- och Services\Microsoft\FileSync\Agent i Loggboken.
    1. Kontrollera att servern är ansluten till internet. 
    2. Kontrollera att Azure filen Sync-filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
        - Vid en upphöjd kommandotolk kör `fltmc`. Kontrollera att de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas.

<a id="files-fail-recall"></a>**Felsökning av filer som inte kan återställas**  
Om filerna kan inte återställas:
1. Granska de operativa och diagnostik händelseloggar, finns under program- och Services\Microsoft\FileSync\Agent i Loggboken.
    1. Kontrollera att filerna finns i Azure-filresursen.
    2. Kontrollera att servern är ansluten till internet. 
    3. Kontrollera att Azure filen Sync-filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
        - Vid en upphöjd kommandotolk kör `fltmc`. Kontrollera att de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas.

<a id="files-unexpectedly-recalled"></a>**Felsökning av filer som oväntat återställs på en server**  
Antivirus, säkerhetskopiering och andra program som läser stort antal filer orsaka oönskade återställningar om de inte respekterar attributet hoppa över offline och hoppa över läsa innehållet i filerna. Hoppar över offline-filer för produkter som stöder det här alternativet hjälper dig att undvika att återställning av oavsiktliga under åtgärder som virusgenomsökning eller säkerhetskopieringsjobb.

Kontakta programleverantören för att lära dig hur du konfigurerar sitt lösning för att hoppa över läsa offlinefiler.

Oavsiktligt återställning kan inträffa i andra scenarier, t.ex. när du bläddrar filer i Utforskaren. Öppna en mapp som innehåller molnet nivåer filer i Utforskaren på servern kan resultera i oväntade återställningar. Detta är ännu mer sannolikt om en lösning för antivirusprogram är aktiverad på servern.

## <a name="general-troubleshooting"></a>Allmän felsökning
Om du får problem med synkronisering av Azure-filen på en server, starta genom att utföra följande steg:
1. Granska händelseloggarna för drift och diagnostik i Loggboken.
    - Synkronisera skiktning och återkalla problem loggas i händelseloggarna diagnostik- och operativa under program- och Services\Microsoft\FileSync\Agent.
    - Problem som kan hantera en server (till exempel konfigurationsinställningar) loggas i händelseloggarna fungerar och diagnostik under program- och Services\Microsoft\FileSync\Management.
2. Kontrollera att Azure filen Sync-tjänsten körs på servern:
    - Öppna snapin-modulen tjänster i MMC och kontrollera att lagring Sync Agent-tjänsten (FileSyncSvc) körs.
3. Kontrollera att Azure filen Sync-filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
    - Vid en upphöjd kommandotolk kör `fltmc`. Kontrollera att de StorageSync.sys och StorageSyncGuard.sys filterdrivrutinerna för filsystemet visas.

Om problemet kvarstår kör du verktyget AFSDiag:
1. Skapa en katalog där AFSDiag utdata ska sparas (till exempel C:\Output).
2. Öppna ett upphöjt PowerShell-fönster och kör sedan följande kommandon (tryck på RETUR efter varje kommando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Spårningsnivån för Azure filsynkronisering kernel-läge, ange **1** (om inget annat anges, att skapa mer utförlig spårningar), och tryck sedan på RETUR.
4. Spårningsnivån för Azure filsynkronisering användaren läge ange **1** (om inget annat anges, att skapa mer utförlig spårningar), och tryck sedan på RETUR.
5. Återskapa problemet. När du är klar kan du ange **D**.
6. En .zip-fil som innehåller loggfiler och spårningsfiler sparas i den angivna katalogen som du angav.

## <a name="see-also"></a>Se även
- [Vanliga och frågor svar om Azure-filer](storage-files-faq.md)
- [Felsökning av problem med Azure-filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsökning av problem med Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
