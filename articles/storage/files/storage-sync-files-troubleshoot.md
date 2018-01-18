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
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 7562e43f58f303ea34a08b8b9e056a0c3d0c10d0
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Felsöka Azure filsynkronisering (förhandsgranskning)
Använda Azure filsynkronisering (förhandsgranskning) för att centralisera din organisations filresurser i Azure-filer, samtidigt som flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure filsynkronisering omvandlar Windows Server till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt data lokalt, inklusive SMB och NFS FTPS. Du kan ha valfritt antal cacheminnen som du behöver över hela världen.

Den här artikeln är utformat för att hjälpa dig att felsöka och lösa problem som kan uppstå med Azure filsynkronisering distributionen. Vi beskriver också hur du samlar in viktiga loggar från systemet om det krävs en mer ingående undersökning av problemet. Om du inte hittar svar på din fråga kan kontakta du oss via följande kanaler (i växande ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [Forum för Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Support. Att skapa en ny supportförfrågan i Azure-portalen på den **hjälp** väljer den **hjälp + support** och välj sedan **ny supportbegäran**.

## <a name="agent-installation-and-server-registration"></a>Agentregistreringen för installation och server
<a id="agent-installation-failures"></a>**Felsöka agentinstallationsproblem**  
Om installationen av Azure filsynkronisering misslyckas vid en upphöjd kommandotolk kör du följande kommando för att aktivera loggning under agentinstallationen:

```
StorageSyncAgent.msi /l*v Installer.log
```

Granska installer.log om du vill ta reda på orsaken till att installationen misslyckades. 

> [!Note]  
> Agentinstallationen misslyckas om datorn har konfigurerats att använda Microsoft Update och Windows Update-tjänsten körs inte.

<a id="agent-installation-websitename-failure"></a>**Agentinstallationen misslyckas med felet: ”lagring Sync Agent guiden avslutades för tidigt”**  
Det här problemet kan inträffa om standardnamnet för IIS-webbplatsen har ändrats. Undvik det här problemet Byt namn på standardwebbplatsen för IIS som ”Default Web Site” och försök installera igen. Problemet korrigeras i en kommande uppdatering av agenten. 

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
<a id="cloud-endpoint-using-share"></a>**Moln slutpunkt skapas misslyckas med felet: ”angivna Azure filresursen används redan av en annan CloudEndpoint”**  
Det här problemet uppstår om Azure-filresursen används redan av en annan molnslutpunkt. 

Om du ser det här meddelandet och Azure-filresursen är för närvarande inte används av en molnslutpunkt, Slutför följande steg om du vill rensa metadata Azure filsynkronisering i Azure-filresursen:

> [!Warning]  
> Ta bort metadata i en Azure-filresurs som för närvarande används av en molnslutpunkt gör att Azure-filen synkroniseringsåtgärder misslyckas. 

1. Gå till Azure-filresursen i Azure-portalen.  
2. Högerklicka på Azure-filresursen och välj sedan **redigera metadata**.
3. Högerklicka på **SyncService**, och välj sedan **ta bort**.

<a id="cloud-endpoint-authfailed"></a>**Moln slutpunkt skapas misslyckas med felet: ”AuthorizationFailed”**  
Det här problemet uppstår om ditt konto inte har tillräcklig behörighet för att skapa en molnslutpunkt. 

Om du vill skapa en molnslutpunkt måste ditt användarkonto ha Microsoft Authorization följande behörigheter:  
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

<a id="server-endpoint-createjobfailed"></a>**Server-slutpunkten har skapats misslyckas med felet: ”MgmtServerJobFailed” (felkod:-2134375898)**                                                                                                                           
Det här problemet uppstår om slutpunktsökväg server finns i systemvolymen och moln skiktning är aktiverad. Molnet skiktning stöds inte på systemvolymen. Inaktivera molnet skiktning när du skapar Serverslutpunkten om du vill skapa en serverslutpunkt för på systemvolymen.

<a id="server-endpoint-deletejobexpired"></a>**Borttagning av slutpunkten misslyckas med felet: ”MgmtServerJobExpired”**                
Det här problemet uppstår om servern är offline eller inte har någon nätverksanslutning. Om servern är inte längre tillgänglig, avregistrera servern på portalen som tar bort server-slutpunkter. Om du vill ta bort server-slutpunkter, följer du stegen som beskrivs i [Avregistrerar en server med Azure filsynkronisering](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Om jag har skapat en fil direkt i min Azure-filresursen via SMB eller via portalen hur lång tid det tar för filen som synkroniseras till servrar i gruppen synkronisering?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**Synkronisering misslyckas på en server**  
Om synkroniseringen misslyckas på en server:
1. Kontrollera att det finns en serverslutpunkt i Azure-portalen för den katalog som du vill synkronisera till en Azure filresurs:
    
    ![En skärmbild av en grupp för synkronisering med både en molnslutpunkt och en serverslutpunkt i Azure-portalen](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. Granska de operativa och diagnostik händelseloggar, finns under program- och Services\Microsoft\FileSync\Agent i Loggboken.
    1. Kontrollera att servern är ansluten till internet.
    2. Kontrollera att Azure filen Sync-tjänsten körs på servern. Öppna snapin-modulen tjänster i MMC för att göra detta, och kontrollera att lagring Sync Agent-tjänsten (FileSyncSvc) körs.

<a id="replica-not-ready"></a>**Synkroniseringen misslyckas med felet: ”0x80c8300f - repliken är inte redo att utföra nödvändiga åtgärden”**  
Det här problemet är förväntat om du skapar en molnslutpunkt och använda en Azure-filresurs som innehåller data. När ändringen identifiering jobbet har slutförts körs på Azure-filresursen (det kan ta upp till 24 timmar) ska starta synkronisering fungerar korrekt.

<a id="broken-sync-files"></a>**Felsökning av enskilda filer som inte kan synkronisera**  
Om det inte går att synkronisera enskilda filer:
1. Granska de operativa och diagnostik händelseloggar, finns under program- och Services\Microsoft\FileSync\Agent i Loggboken.
2. Kontrollera att det finns inga öppna referenser i filen.

    > [!NOTE]
    > Azure filsynkronisering tar regelbundet VSS-ögonblicksbilder för att synkronisera filer som har öppna referenser.

Vi stöder för närvarande inte resursflyttningen till en annan prenumeration eller, flyttar till en annan Azure AD-klient.  Om prenumerationen flyttas till en annan klient, blir Azure-filresursen otillgänglig för vår tjänst ändringen i ägarskap. Om klienten har ändrats, du måste ta bort server-slutpunkter och molnslutpunkt (se synkronisering grupphantering avsnittet anvisningar så här rengör du Azure-filresursen för att återanvändas) och skapa gruppen synkronisering.

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Det finns två sökvägar för fel i moln skiktning:

- Filer kan det hända att nivån, vilket innebär att Azure filsynkronisering fel försöker tjänstnivån en fil till Azure Files.
- Filer kan det hända att återkalla, vilket innebär att Azure filsynkronisering Filsystemfilter (StorageSync.sys) går inte att hämta data när en användare attemptes åtkomst till en fil som har varit nivåer.

Det finns två huvudsakliga klasser av fel som kan ske via antingen fel sökväg:

- Molnet lagringsfel
    - *Problem med tjänsters tillgänglighet tillfälliga lagringen*. Se [serviceavtalet (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) för mer information.
    - *Tillgänglig Azure-filresursen*. Det här felet inträffar oftast när du tar bort Azure-filresursen när det är fortfarande är en molnslutpunkt i en grupp för synkronisering.
    - *Tillgänglig lagringskonto*. Det här felet inträffar oftast när du tar bort lagringskontot medan det fortfarande har en Azure-filresurs som är en molnslutpunkt i en grupp för synkronisering. 
- Serverfel 
    - *Filsynkronisering Filsystemfilter som Azure (StorageSync.sys) har inte lästs in*. För att kunna svara på skiktning/återkalla begäranden, måste Azure filsynkronisering Filsystemfilter läsas in. Filtret inte läses kan inträffa av olika orsaker, men den vanligaste orsaken är att en administratör minnet den manuellt. Azure filsynkronisering Filsystemfilter måste läsas in på alla tider för Azure filsynkronisering ska fungera korrekt.
    - *Saknas, är skadad eller på annat sätt brutna referenspunkt*. En referenspunkt är en särskild datastruktur för en fil som består av två delar:
        1. En referenspunkt-tagg som anger att operativsystemet Azure filsynkronisering Filsystemfilter (StorageSync.sys) kan behöva vidta vissa åtgärder i/o till filen. 
        2. Referensdata som anger att filsystemet filtrera URI för filen på slutpunkten associerade molnet (Azure-filresursen). 
        
        Det vanligaste sättet kan vara skadade en referenspunkt är om en administratör försöker att ändra koden eller dess data. 
    - *Problem med nätverksanslutning*. Servern måste ha Internetanslutning för att kunna återkalla en fil eller tjänstnivån.

Följande avsnitt visar hur du felsöker problem med cloud lagringsnivåer och avgöra om ett problem är ett problem för lagring av molnet eller ett serverproblem med.

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

## <a name="see-also"></a>Se också
- [Vanliga och frågor svar om Azure-filer](storage-files-faq.md)
- [Felsökning av problem med Azure-filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsökning av problem med Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
