---
title: Vanliga frågor om Azure-filer | Microsoft Docs
description: Få svar på vanliga frågor och svar om Azure-filer.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: e203787bffa80b324508f7df8f8e7a8c62355695
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Vanliga frågor och svar om Azure-filer
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via standardmässiga [Server Message Block (SMB) protokollet](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (även kallat Common Internet File System eller CIFS). Du kan montera Azure-filresurser samtidigt på molnet eller lokala distributioner av Windows, Linux och macOS. Du kan också cachelagra Azure-filresurser på Windows Server-datorer med hjälp av Azure filsynkronisering (förhandsversion) för snabb åtkomst nära där data används.

Den här artikeln innehåller svar på vanliga frågor om Azure-filer och funktioner, inklusive användning av Azure filsynkronisering med Azure-filer. Om du inte hittar svar på din fråga kan kontakta du oss via följande kanaler (i växande ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [Forum för Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Support. Att skapa en ny supportförfrågan i Azure-portalen på den **hjälp** väljer den **hjälp + support** och välj sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>
**Hur är Azure Files användbar?**  
   Du kan använda Azure-filer för att skapa filresurser i molnet, utan att vara ansvarig för hantering av en fysisk server, en enhet eller en enhet. Vi arbetar monotont för dig, inklusive tillämpa operativsystemuppdateringar och ersätta skadade diskar. Mer information om scenarier som Azure-filer kan hjälpa dig med finns [varför Azure-filer är användbart](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Vad är olika sätt att komma åt filer i Azure-filer?**  
    Du kan montera filresursen på den lokala datorn med hjälp av SMB 3.0-protokollet eller använda verktyg som [Lagringsutforskaren](http://storageexplorer.com/) kan komma åt filer i filresursen. Från ditt program använda du lagringsklientbiblioteken REST API: er, PowerShell eller Azure CLI för att komma åt dina filer i Azure-filresursen.

* <a id="what-is-afs"></a>
**Vad är Azure filsynkronisering?**  
    Du kan använda Azure filsynkronisering för att centralisera din organisations filresurser i Azure-filer, samtidigt som flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure filsynkronisering omvandlar Windows Server-datorer till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt data lokalt, inklusive SMB-, Network File System (NFS) och File Transfer Protocol Service (FTPS). Du kan ha valfritt antal cacheminnen som du behöver över hela världen.

* <a id="files-versus-blobs"></a>
**Varför ska jag använda en Azure-filresursen jämfört med Azure Blob storage för Mina data?**  
    Azure Files och Azure Blob storage både innehåller olika sätt att lagra stora mängder data i molnet, men de är användbara i något annat syfte. 
    
    Azure Blob storage är användbart för massiv skala, moln-intern program som behöver lagra Ostrukturerade data. Om du vill maximera prestanda och skalning är Azure Blob storage en enklare lagring abstraktion än ett true filsystem. Du kan komma åt Azure Blob storage endast via REST-baserade klientbibliotek (eller direkt via REST-baserat protokoll).

    Azure Files är specifikt ett filsystem. Azure Files har alla fil-sammanfattningar som du känner till och från flera års arbete med lokala operativsystem. Som Azure Blob storage erbjuder ett REST-gränssnitt och REST-baserade klientbibliotek Azure-filer. Till skillnad från Azure Blob storage erbjuder Azure Files SMB-åtkomst till Azure-filresurser. Med hjälp av SMB, kan du montera en Azure-filresursen direkt på Windows, Linux eller macOS, antingen lokalt eller i molnet virtuella datorer, utan att skriva någon kod eller att inga särskilda drivrutiner till filsystemet. Du kan också cachelagra Azure-filresurser på lokala filservrar med hjälp av Azure filsynkronisering för Snabbåtkomst nära där data används. 
   
    En mer detaljerad beskrivning av skillnaderna mellan Azure-filer och Azure Blob storage finns [avgöra när du behöver använda Azure Blob storage, Azure-filer eller Azure-diskar](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Läs mer om Azure Blob storage i [introduktion till Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Varför ska jag använda en Azure-filresursen i stället för Azure-diskar**  
    En disk i Azure-diskar är helt enkelt en disk. En fristående disk ensamt är inte användbar för. Om du vill hämta värdet från Azure-diskar, måste du koppla en disk till en virtuell dator som körs i Azure. Azure-diskar som kan användas för allt som du vill använda en disk för på en lokal server. Du kan använda den som en OS-systemdisken, växlingsutrymme för ett operativsystem eller dedikerad lagring för ett program. En intressant Azure diskar används för att skapa en filserver i molnet för att använda på samma plats där du kan använda en Azure-filresursen. Distribuera en filserver i Azure Virtual Machines är ett högpresterande sätt att få fillagring i Azure när du behöver distributionsalternativ som för närvarande inte stöds av Azure-filer (till exempel NFS-protokollet-stöd- eller premium-lagring). 

    Kör en filserver med Azure-diskar som backend-lagring vanligtvis är dock mycket dyrare än att använda en Azure-filresursen på olika orsaker. Först, förutom att betala för disklagring, du också måste betalar för kostnaderna för att använda en eller flera virtuella Azure-datorer. Andra, du kan också måste hantera de virtuella datorer som används för att köra filservern. Till exempel ansvarar du för OS-uppgraderingar. Slutligen om du behöver slutligen data till cachelagrade lokalt, är det till dig att konfigurera och hantera replikering tekniker, till exempel DFSR Distributed File System Replication (), så att det sker.

    En metod för att få bästa av både Azure-filer och en filserver som finns i Azure Virtual Machines (förutom med hjälp av Azure-diskar som backend-lagring) är att installera Azure filsynkronisering på en filserver som finns på ett moln VM. Om Azure-filresursen finns i samma region som din filserver kan aktivera du molnet skiktning och ange mängden ledigt utrymme procent till maximalt (99%). Detta säkerställer minimal duplicering av data. Du kan också använda alla program som du vill ha med dina filservrar som stöd för program som kräver NFS-protokollet.

    Information om ett alternativ för hur du konfigurerar en hög prestanda och hög tillgänglighet filserver i Azure finns [distribuera IaaS-VM gäst-kluster i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer detaljerad beskrivning av skillnaderna mellan Azure-filer och Azure-diskar, se [avgöra när du behöver använda Azure Blob storage, Azure-filer eller Azure-diskar](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Läs mer om Azure-diskar i [översikt över Azure hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Hur kommer jag igång med Azure-filer?**  
   Det är lätt att komma igång med Azure-filer. Första, [skapa en filresurs](storage-how-to-create-file-share.md), och montera den i önskat operativsystem: 

    * [Montera i Windows](storage-how-to-use-files-windows.md)
    * [Montera i Linux](storage-how-to-use-files-linux.md)
    * [Montera i macOS](storage-how-to-use-files-mac.md)

   En mer detaljerad guide om hur du distribuerar en Azure-filresursen att ersätta produktion filresurser i din organisation, finns [planera för distribution av en Azure-filer](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Vilka lagringsalternativ för redundans stöds av Azure-filer?**  
    För närvarande stöder Azure-filer lokalt redundant lagring (LRS), zonen redundant lagring (ZRS) och geo-redundant lagring (GRS). Vi planerar att stödja läsbehörighet geo-redundant lagring med (RA-GRS) i framtiden, men det finns ingen tidslinjer att dela just nu.

* <a id="tier-options"></a>
**Vilka lagringsnivåer stöds i Azure-filer?**  
    Azure-filer stöder för närvarande endast standard lagringsnivå. Vi har inte tidslinjer att dela för premium-lagring och lågfrekvent stöder just nu. 
    
    > [!NOTE]
    > Du kan inte skapa Azure-filresurser från endast blob storage-konton eller från premium storage-konton.

* <a id="give-us-feedback"></a>
**Jag vill verkligen finns en specifik funktion som lagts till i Azure-filer. Kan du lägga till den?**  
    Azure Files team vill höra alla feedback som du har om vår tjänst. Kontrollera rösta på funktioner som efterfrågas på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi söker vidarebefordra till delighting du med många nya funktioner.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Vilka regioner som stöds för Azure filsynkronisering (förhandsgranskning)?**  
    Azure filsynkronisering är för närvarande finns i östra, Kanada Central, östra USA, Sydostasien, Storbritannien, Syd, Västeuropa och USA, västra. Stöd för flera områden läggs medan vi mot allmän tillgänglighet. Mer information finns i [regional tillgänglighet](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**Kan jag ha domänanslutna och icke-domänanslutna servrar i gruppen samma synkronisering?**  
    Ja. En sync-grupp kan innehålla server-slutpunkter som har olika Active Directory-medlemskap, även om de inte är ansluten till domänen. Även om den här konfigurationen fungerar tekniskt, rekommenderar vi inte detta som en typisk konfiguration eftersom åtkomstkontrollistor (ACL) som har definierats för filer och mappar på en server kan inte tillämpas på andra servrar i gruppen synkronisering. För bästa resultat rekommenderar vi synkroniserar mellan servrar som finns i samma Active Directory-skog, mellan servrar som finns i olika Active Directory-skogar men som finns upprättade förtroenderelationer eller mellan servrar som inte ingår i en domän. Vi rekommenderar att du inte använder en blandning av dessa konfigurationer.

* <a id="afs-change-detection"></a>
**Jag har skapat en fil direkt i min Azure-filresursen via SMB eller i portalen. Hur lång tid tar det för filen som synkroniseras till servrar i gruppen synkronisering?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Vad händer om samma fil ändras på två servrar på ungefär samma tillfälle?**  
    Azure filsynkronisering använder en enkel konfliktlösning strategi: vi Behåll båda ändringarna till filer som ändras på två servrar på samma gång. Senast skrivna ändringen behåller det ursprungliga filnamnet. Den äldre filen har ”källdatorn” och antalet konflikt läggs till i namnet. Den här taxonomi följer: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< EXT\>  

    CompanyReport.docx första konflikten skulle bli CompanyReport CentralServer.docx om CentralServer är där den äldre skrivningen inträffade. Andra konflikten namnet CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Stöds geo-redundant lagring för Azure filsynkronisering?**  
    Ja, Azure-filer har stöd för både lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). Om det uppstår GRS växling mellan parad regioner, rekommenderar vi att du hantera den nya regionen som en säkerhetskopia av data endast. Azure filsynkronisering börjar automatiskt inte synkroniserar med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>
**Varför inte den *storlek på disken* -egenskapen för en fil matchar den *storlek* egenskapen när du använder Azure filsynkronisering?**  
    Utforskaren exponerar två egenskaper som representerar storleken på en fil: **storlek** och **storlek på disken**. Dessa egenskaper skiljer sig något i betydelse. **Storlek** representerar fullständig storleken på filen. **Storlek på disk** representerar storleken på filström som lagras på disken. Värden för dessa egenskaper kan skiljer sig för en mängd olika orsaker, till exempel komprimering, användning av Datadeduplicering eller molnet skiktning med synkronisering av Azure-filen. Om en fil nivåer till en Azure-filresurs, är storleken på disken noll, eftersom dataströmmen filen lagras i Azure-filresursen och inte på disken. Det är också möjligt för en fil som ska vara delvis nivåindelade (eller delvis återkallade). I en delvis nivåindelade fil är en del av filen på disken. Detta kan inträffa när filer läses delvis av program som multimedia spelare och zip-verktyg. 

* <a id="is-my-file-tiered"></a>
**Hur vet jag om en fil har nivåindelade?**  
    Det finns flera sätt att kontrollera om en fil nivåer till Azure-filresursen:
    
   *  **Kontrollera filattribut på filen.**
     Om du vill göra det, högerklickar du på en fil, gå till **information**, och bläddra till den **attribut** egenskapen. En skiktad fil har de följande attribut:     
        
        | Attributet bokstav | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen bör säkerhetskopieras av programvara för säkerhetskopiering. Det här attributet anges alltid, oavsett om filen är nivåer eller fullständigt lagras på disk. |
        | P | Sparse-fil | Anger att filen är en sparse-fil. En sparse-fil är en särskild typ av fil som erbjuder NTFS för effektiv användning när filen på disken dataströmmen främst är tom. Azure filsynkronisering använder sparse-filer eftersom en fil nivåer helt eller delvis återkallas. I en fullständigt nivåindelade fil lagras dataströmmen filen i molnet. I en delvis återställd fil, som en del av filen redan finns på disken. Om en fil är fullständigt återkallas till disk Azure filsynkronisering konverteras från en sparse-fil till en vanlig fil. |
        | L | Referenspunkt | Anger att filen har en referenspunkt. En referenspunkt är en särskild pekare för användning av en Filsystemfilter. Azure filsynkronisering använder referenspunkter definiera för Azure filsynkronisering Filsystemfilter (StorageSync.sys) molnet platsen där filen lagras. Detta stöder sömlös åtkomst. Användarna behöver inte veta att Azure filsynkronisering som används och hur du får åtkomst till filen i Azure-filresursen. När en fil återställs fullständigt bort Azure filsynkronisering referenspunkten från filen. |
        | O | Offline | Anger att vissa eller alla av filens innehåll lagras på disk. När en fil återställs fullständigt Azure filsynkronisering tar du bort det här attributet. |

        ![Dialogrutan Egenskaper för en fil med fliken Information markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Du kan se attributen för alla filer i en mapp genom att lägga till den **attribut** till tabellen visningen av Utforskaren. Gör du genom att högerklicka på en befintlig kolumn (till exempel **storlek**), Välj **mer**, och välj sedan **attribut** från den nedrullningsbara listan.
        
   * **Använd `fsutil` att söka efter referenspunkter på en fil.**
       Enligt beskrivningen i föregående alternativ har en skiktad fil alltid en referenspunkt uppsättningen. En referenspunkt pekare är en särskild pekare för Azure filsynkronisering Filsystemfilter (StorageSync.sys). Kontrollera om en fil har en referenspunkt i en upphöjd kommandotolk eller PowerShell-fönstret genom att köra den `fsutil` verktyget:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen är en referenspunkt, du kan förvänta dig att se **referenspunkter Taggvärde: 0x8000001e**. Den här hexadecimalt värde är referenspunkter punktvärdet som ägs av Azure filsynkronisering. Utdata innehåller också referensdata som representerar sökvägen till filen på Azure-filresursen.

        > [!WARNING]  
        > Den `fsutil reparsepoint` verktyget kommandot har också möjlighet att ta bort en referenspunkt. Kör inte kommandot om Azure filsynkronisering teknikteamet ber dig. Kör detta kommando kan resultera i förlust av data. 

* <a id="afs-recall-file"></a>**En fil som du vill använda har tagits nivåer. Hur kan jag för att återställa filen till disk för att använda lokalt?**  
    Det enklaste sättet att återställa till en fil är att öppna filen. Azure filsynkronisering Filsystemfilter (StorageSync.sys) hämtas sömlöst från din Azure-filresursen utan från din sida. För filtyper som kan vara delvis lästes från, till exempel multimedia eller ZIP-filer, öppna en fil inte ladda ned hela filen.

    Du kan också använda PowerShell för att tvinga en fil som ska återställas. Det här alternativet kan vara användbart om du vill återställa flera filer samtidigt, till exempel alla filer i en mapp. Öppna ett PowerShell-session till servernod där Azure filsynkronisering är installerat och kör följande PowerShell-kommandon:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Hur jag tvinga en fil eller katalog till nivåindelas?**  
    När funktionen molnet lagringsnivåer aktiveras molnet skiktning automatiskt nivåer filer baserat på senaste åtkomst och ändra gånger för att uppnå den volymen ledigt utrymme i procent som anges på molnet slutpunkten. Ibland, men kanske du vill manuellt framtvinga en fil till nivån. Detta kan vara användbart om du sparar en stor fil som du inte tänker använda igen under en längre tid och du vill använda det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan tvinga skiktning med hjälp av följande PowerShell-kommandon:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Hur är *ledigt utrymme på volymen* tolkas när jag har flera server-slutpunkter på en volym?**  
    När det finns mer än en serverslutpunkt på en volym är är effektiva volym ledigt utrymme tröskelvärdet den största ledigt utrymme på volymen anges i valfri serverslutpunkt på den volymen. Filer kommer att nivåer enligt deras användningsmönster oavsett vilken serverslutpunkt som de tillhör. Till exempel om du har två server-slutpunkter på en volym, slutpunkt 1 och Endpoint2, där slutpunkt 1 har ett tröskelvärde för ledigt utrymme av volymen 25% och Endpoint2 har ett tröskelvärde för ledigt utrymme en volym på 50% kommer ledigt utrymme tröskeln för både server-slutpunkter att 50%.

* <a id="afs-files-excluded"></a>
**Vilka filer och mappar undantas automatiskt av Azure filen synkronisering?**  
    Som standard omfattar Azure filsynkronisering inte följande filer:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Följande mappar undantas också som standard:

    * \System volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Kan jag använda Azure filsynkronisering med Windows Server 2008 R2, Linux eller enheten nätverksansluten lagring (NAS)?**  
    Azure filsynkronisering stöder för närvarande endast Windows Server 2016 och Windows Server 2012 R2. Vi har inte de scheman som vi kan dela just nu, men vi har öppna stöd för ytterligare plattformar baserat på kundernas behov. Berätta för oss på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) vilka plattformar som vi ska stödja.

## <a name="security-authentication-and-access-control"></a>Säkerhet, autentisering och åtkomstkontroll
* <a id="ad-support"></a>
**Är Active Directory-baserad autentisering och åtkomstkontroll som stöds av Azure-filer?**  
    Azure Files finns två sätt att hantera åtkomstkontroll:

    - Du kan använda signaturer för delad åtkomst (SAS) för att generera token som har specifika behörigheter och som är giltiga för ett angivet tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en viss fil som har en 10 minuters upphör att gälla. Alla som har token när denna token är giltig har skrivskyddad åtkomst till filen för de 10 minuterna. Signatur för delad åtkomstnycklar stöds för närvarande endast via REST-API: et eller klientbiblioteken. Med hjälp av lagringskontonycklar måste du montera Azure-filresursen via SMB.

    - Azure filsynkronisering bevaras och replikerar alla godtyckliga åtkomstkontrollistor eller DACL: er och (om Active Directory-baserad eller lokala) till alla server-slutpunkter som synkroniseras med. Eftersom Windows Server redan kan autentisera med Active Directory, Azure filsynkronisering är ett effektivt stoppa lucka alternativ tills fullständigt stöd för Active Directory-baserad autentisering och anländer ACL-stöd.

    För närvarande är stöder Azure Files inte Active Directory direkt.

* <a id="encryption-at-rest"></a>
**Hur kan jag säkerställa att min Azure-filresursen är krypterat i vila?**  
    Azure Storage Service-kryptering håller på att aktiveras som standard i alla regioner. För dessa regioner behöver du inte vidta några åtgärder för att aktivera kryptering. Andra regioner finns [kryptering för serversidan](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hur kan ge åtkomst till en viss fil med hjälp av en webbläsare?**  
    Du kan använda signaturer för delad åtkomst för att generera token som har specifika behörigheter och som är giltiga för ett angivet tidsintervall. Du kan till exempel generera en token som ger läsbehörighet till en viss fil för en angiven tidsperiod. Alla som har URL: en kan komma åt filen direkt från en webbläsare när token är giltig. Du kan enkelt skapa en nyckel för signatur för delad åtkomst från ett gränssnitt som Lagringsutforskaren.

* <a id="file-level-permissions"></a>
**Är det möjligt att ange skrivskyddad eller lässkyddad behörighet för mapparna på resursen?**  
    Om du monterar filresursen via SMB inte mappnivå kontroll över behörigheter. Om du skapar en signatur för delad åtkomst med hjälp av REST API eller klientbibliotek kan du ange skrivskyddad eller lässkyddad behörighet för mapparna på resursen.

* <a id="ip-restrictions"></a>
**Kan jag implementera IP-begränsningar för en Azure-filresursen?**  
    Ja. Åtkomst till din Azure-filresursen kan vara begränsad på kontonivå lagring. Mer information finns i [konfigurera brandväggar för Azure-lagring och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Vilka data efterlevnadsprinciper stöder Azure-filer?**  
   Azure Files körs ovanpå samma lagringsarkitekturen som används i andra storage-tjänster i Azure Storage. Azure Files gäller samma data efterlevnadsprinciper som används i andra Azure-lagringstjänster. Mer information om kompatibilitet för Azure Storage-data som du kan hämta och referera till den [dataskydd i Microsoft Azure-dokumentet](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409), och gå till den [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Lokal åtkomst
* <a id="expressroute-not-required"></a>
**Måste jag använda Azure ExpressRoute för att ansluta till Azure-filer eller om du vill använda Azure filsynkronisering lokalt?**  
    Nej. ExpressRoute krävs inte för att få åtkomst till en Azure-filresursen. Om du monterar en Azure-filresurs direkt lokalt, allt som krävs är att port 445 (TCP utgående) öppen för Internetåtkomst (detta är den port som använder SMB för att kommunicera). Om du använder Azure filsynkronisering allt som krävs är port 443 (TCP utgående) för HTTPS-åtkomst (ingen SMB krävs). Men du *kan* använda ExpressRoute med något av dessa alternativ.

* <a id="mount-locally"></a>
**Hur kan jag montera en Azure-filresursen på min dator?**  
    Du kan montera filresursen med hjälp av SMB-protokollet om port 445 (TCP utgående) är öppen och klienten stöder SMB 3.0-protokollet (till exempel om du använder Windows 10 eller Windows Server 2016). Om port 445 blockeras av organisationens princip eller av din Internetleverantör, kan du använda Azure filsynkronisering åtkomst till Azure-filresursen.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hur gör jag säkerhetskopiera min Azure fil dela?**  
    Du kan använda periodiska [dela ögonblicksbilder](storage-snapshots-files.md) för skydd mot oavsiktliga borttagningar. Du kan också använda AzCopy, Robocopy eller tredje parts säkerhetskopiering som kan säkerhetskopiera en monterad filresurs. 

## <a name="share-snapshots"></a>Dela ögonblicksbilder
### <a name="share-snapshots-general"></a>Dela ögonblicksbilder: Allmänt
* <a id="what-are-snaphots"></a>
**Vad är filen resursen ögonblicksbilder?**  
    Du kan använda Azure file share ögonblicksbilder för att skapa en skrivskyddad version av din filresurser. Du kan också använda Azure-filer för att kopiera en tidigare version av säkerhetskopian innehåll till samma resurs, till en annan plats i Azure eller lokalt för flera ändringar. Mer information om resursen ögonblicksbilder finns i [dela ögonblicksbild översikt](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Var lagras mina resursen ögonblicksbilder?**  
    Dela ögonblicksbilder som lagras i samma lagringskonto som filresursen.

* <a id="snapshot-perf-impact"></a>
**Finns det några konsekvenser för prestanda för att använda resursen ögonblicksbilder?**  
    Dela ögonblicksbilder har inte någon prestanda försämras.

* <a id="snapshot-consistency"></a>
**Är resursen ögonblicksbilder programkonsekventa?**  
    Nej, dela ögonblicksbilder stöds inte programkonsekventa. Användaren måste tömma skrivningar från programmet till resursen innan dela ögonblicksbild.

* <a id="snapshot-limits"></a>
**Finns det någon gräns för antalet ögonblicksbilder för resursen kan användas?**  
    Ja. Azure Files kan behålla högst 200 resursen ögonblicksbilder. Dela ögonblicksbilder räknas inte in i filresursens kvot så att det finns ingen gräns per resurs totalt utrymme som används av alla ögonblicksbilder för resursen. Lagringskontogränser gäller fortfarande. Efter 200 resursen ögonblicksbilder, måste du ta bort äldre ögonblicksbilder för att skapa nya ögonblicksbilder som resursen.
* <a id="snapshot-cost"></a>
**Hur mycket delar ögonblicksbild kostnaden?**  
    Standard transaktionen och lagringskostnaden är standard gäller för ögonblicksbilder. Ögonblicksbilder är inkrementell till sin natur. Grundläggande ögonblicksbilden är resursen sig själv. Alla efterföljande ögonblicksbilder är inkrementell och kommer bara att lagra diff från den tidigare ögonblicksbilden. Det innebär att deltaändringar som visas i växeln blir minimal om omsättning av arbetsbelastning är minimal. Se [priser sidan](https://azure.microsoft.com/en-us/pricing/details/storage/files/) för Azure standardfiler prisinformation. Idag sättet att titta på storleken som används av filresursen ögonblicksbild är genom att jämföra fakturerade kapacitet med används kapacitet. Vi arbetar på tooling för att förbättra reporting.


### <a name="create-share-snapshots"></a>Skapa resursen ögonblicksbilder
* <a id="file-snaphsots"></a>
**Kan jag skapa resursen ögonblicksbild av enskilda filer?**  
    Dela ögonblicksbilder skapas på filnivå för resursen. Du kan återställa enskilda filer från filen dela ögonblicksbild men skapa inte ögonblicksbilder på filnivå resursen. Men om du har tagit en resursnivå dela ögonblicksbild och du vill visa resursen ögonblicksbilder där en viss fil har ändrats, kan du göra det under **tidigare versioner** på en Windows-monterade resurs. 
    
    Om du behöver en ögonblicksbild funktionen berätta för oss på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Kan jag skapa resursen ögonblicksbilder av en krypterad filresurs?**  
    Du kan ta en ögonblicksbild för resursen av Azure-filresurser som har kryptering i vila aktiverad. Du kan återställa filer från en ögonblicksbild av en resurs till en krypterad filresurs. Om resursen är krypterad krypteras din resurs ögonblicksbild dessutom.

* <a id="geo-redundant-snaphsots"></a>
**Är Mina resursen ögonblicksbilder geo-redundant?**  
    Dela ögonblicksbilder har samma redundans som Azure-filresursen de togs. Om du har valt geo-redundant lagring för ditt konto, lagras resursen ögonblicksbild också redundant i parad region.

### <a name="manage-share-snapshots"></a>Hantera resursen ögonblicksbilder
* <a id="browse-snapshots-linux"></a>
**Kan jag Bläddra min resursen ögonblicksbilder från Linux?**  
    Du kan använda Azure CLI 2.0 för att skapa, visa, bläddra och återställa resursen ögonblicksbilder i Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan jag kopiera ögonblicksbilderna resurs till ett annat lagringskonto?**  
    Du kan kopiera filer från resursen ögonblicksbilder till en annan plats, men du kan inte kopiera resurs ögonblicksbilderna sig själva.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från resursen ögonblicksbilder
* <a id="promote-share-snapshot"></a>
**Kan jag uppgradera en ögonblicksbild av en resurs till grundläggande resursen?**  
    Du kan kopiera data från en ögonblicksbild av en resurs till andra mål. Du kan inte befordra en ögonblicksbild av en resurs till grundläggande resursen.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan jag återställa data från min dela ögonblicksbild till ett annat lagringskonto?**  
    Ja. Filer från en ögonblicksbild av en resurs kan kopieras till den ursprungliga platsen eller till en annan plats som innehåller samma lagringskonto eller ett annat lagringskonto i samma region eller i olika regioner. Du kan också kopiera filer till en lokal plats eller till andra moln.    
  
### <a name="clean-up-share-snapshots"></a>Rensa resursen ögonblicksbilder
* <a id="delete-share-keep-snapshots"></a>
**Kan jag bort min resurs men inte ta bort min resursen ögonblicksbilder?**  
    Om du har active resursen ögonblicksbilder på nätverksresursen, kan du ta bort resursen. Du kan använda en API för att ta bort resursen ögonblicksbilder, tillsammans med resursen. Du kan också radera både ögonblicksbilder för resursen och resursen i Azure-portalen.

* <a id="delete-share-with-snapshots"></a>
**Vad händer med min resursen ögonblicksbilder om jag tar bort mitt lagringskonto?**  
    Om du tar bort ditt lagringskonto tas resursen ögonblicksbilder också bort.

## <a name="billing-and-pricing"></a>Fakturering och priser
* <a id="vm-file-share-network-traffic"></a>
**Räknas nätverkstrafik mellan en Azure VM och en Azure-filresurs som extern bandbredd som debiteras genom prenumerationen?**  
    Om filresursen och den virtuella datorn finns i samma Azure-region, finns utan extra kostnad för trafik mellan filresursen och den virtuella datorn. Om filresursen och den virtuella datorn är i olika områden debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>
**Hur mycket delar ögonblicksbilder kostnaden?**  
     Under förhandsgranskningen är gratis för resursen ögonblicksbild kapacitet. Standard lagringskostnader för utgående trafik och transaktionen gäller. Prenumerationer kommer att debiteras för kapacitet och transaktioner på resursen ögonblicksbilder efter allmän tillgänglighet.
     
     Dela ögonblicksbilder är inkrementell till sin natur. Grundläggande resursen ögonblicksbilden är resursen sig själv. Alla efterföljande resursen ögonblicksbilder är inkrementell och lagra skillnaden från föregående dela ögonblicksbild. Du debiteras endast för det ändrade innehållet. Om du har en resurs med 100 GiB av data, men endast 5 GiB har ändrats sedan din senaste dela ögonblicksbild, dela ögonblicksbild förbrukar endast 5 ytterligare GiB och du debiteras för 105 GiB. Mer information om transaktionen och standard utgång avgifter finns i [priser sidan](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalning och prestanda
* <a id="files-scale-limits"></a>
**Vad är Azure Files skala gränser?**  
    Information om skalbarhets- och prestandamål för Azure-filer, se [skalbarhets- och prestandamål för Azure-filer](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Jag behöver en större filresurs än Azure-filer finns för närvarande. Kan jag öka storleken på min Azure-filresursen?**  
    Nej. Den maximala storleken för en Azure-filresursen är 5 TiB. Detta är för närvarande en hård gräns som vi inte kan ändras. Vi arbetar på en lösning för att öka storleken på filresursen till 100 TiB, men det finns ingen tidslinjer att dela just nu.

* <a id="open-handles-quota"></a>
**Hur många klienter har åtkomst till samma fil samtidigt?**   
    Det finns en kvot på 2 000 öppna referenser i en enda fil. När du har 2 000 öppna referenser visas ett felmeddelande som säger kvoten har uppnåtts.

* <a id="zip-slow-performance"></a>
**Min går långsamt när jag packa upp filer i Azure-filer. Vad ska jag göra?**  
    För att överföra stora mängder filer till Azure-filer, rekommenderar vi att du använder AzCopy (för Windows, i förhandsvisning för Linux och UNIX) eller Azure PowerShell. Dessa verktyg har optimerats för nätverksöverföring.

* <a id="slow-perf-windows-81-2012r2"></a>
**Varför är min långsamma prestanda när jag ansluta min Azure-filresursen på Windows Server 2012 R2 eller Windows 8.1?**  
    Det finns ett känt problem när du monterar en Azure-filresursen på Windows Server 2012 R2 och Windows 8.1. Problemet har korrigeras i April 2014 kumulativ uppdatering för Windows 8.1 och Windows Server 2012 R2. Se till att alla instanser av Windows Server 2012 R2 och Windows 8.1 har denna uppdatering har tillämpats för optimal prestanda. (Du bör alltid få Windows korrigeringsfiler via Windows Update.) Mer information finns i tillhörande Microsoft Knowledge Base-artikel [låga prestanda när du har åtkomst till Azure-filer från Windows 8.1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och samverkan med andra tjänster
* <a id="cluster-witness"></a>
**Kan jag använda min Azure-filresursen som en *filresursvittne* för min redundanskluster för Windows Server?**  
    Den här konfigurationen stöds för närvarande inte för en Azure-filresursen. Mer information om hur du anger detta för Azure Blob storage finns [distribuera ett moln vittne för ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Kan jag montera en Azure-filresurs på en instans av Azure-behållare?**  
    Ja, Azure-filresurser är ett bra alternativ när du vill spara information utöver livslängden för en instans i behållaren. Mer information finns i [montera en filresurs som Azure med Azure-behållare instanser](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Finns det en åtgärd för namnbyten i REST API?**  
    Inte just nu.

* <a id="nested-shares"></a>
**Kan jag skapa kapslade resurser? Med andra ord en resurs under en annan resurs?**  
    Nej. Filresursen *är* den virtuella drivrutin som du kan montera, så kapslade resurser inte stöds.

* <a id="ibm-mq"></a>
**Hur använder Azure-filer med IBM MQ?**  
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder konfigurera Azure-filer med IBM-tjänsten. Mer information finns i [hur du ställer in en IBM MQ flera instanser köhanteraren med tjänsten Microsoft Azure-filer](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se också
* [Felsöka Azure filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure filsynkronisering (förhandsgranskning)](storage-sync-files-troubleshoot.md)
