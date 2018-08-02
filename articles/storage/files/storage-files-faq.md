---
title: Vanliga frågor (och svar FAQ) för Azure Files | Microsoft Docs
description: Hitta svar på vanliga frågor och svar om Azure Files.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/19/2018
ms.author: renash
ms.openlocfilehash: 9ecedb171fba9ae8719121f51026134f2bdc1a5f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413713"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Vanliga frågor (och svar FAQ) om Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via vanliga [Server Message Block (SMB) protokollet](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Du kan montera Azure-filresurser samtidigt på molnet eller lokala distributioner av Windows, Linux och macOS. Du kan också cachelagra Azure-filresurser på Windows Server-datorer med hjälp av Azure File Sync för snabb åtkomst nära där data används.

Den här artikeln innehåller vanliga frågor och svar om Azure Files och funktioner, inklusive användning av Azure File Sync med Azure Files. Om du inte ser svar på din fråga, kan du kontakta oss via följande kanaler (i ständigt växande ordning):

1. Kommentarer i den här artikeln.
2. [Azure Storage-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [UserVoice för Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft-supporten. Att skapa en ny supportbegäran i Azure-portalen på den **hjälpa** fliken den **hjälp + support** och välj sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>
**Hur är Azure Files användbart?**  
   Du kan använda Azure Files för att skapa filresurser i molnet, utan att vara ansvarar för att hantera kostnaderna för att en fysisk server, en enhet eller en installation. Vi gör monotont arbetet åt dig, till exempel tillämpa uppdateringar av Operativsystemet och ersätta skadade diskar. Läs mer om de scenarier som Azure Files kan hjälpa dig med i [varför Azure Files är användbart](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Det finns olika sätt att komma åt filer i Azure Files?**  
    Du kan montera filresursen på den lokala datorn med hjälp av SMB 3.0-protokollet och du kan använda verktyg som [Lagringsutforskaren](http://storageexplorer.com/) att komma åt filer i filresursen. Från ditt program, kan du använda storage-klientbibliotek, REST API: er, PowerShell eller Azure CLI att få åtkomst till filer i Azure-filresursen.

* <a id="what-is-afs"></a>
**Vad är Azure File Sync?**  
    Du kan använda Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar dina Windows Server-datorer till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, Network File System (NFS) och File Transfer Protocol Service (FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

* <a id="files-versus-blobs"></a>
**Varför ska jag använda en Azure-filresurs jämfört med Azure Blob storage för Mina data?**  
    Azure Files och Azure Blob storage både erbjuder olika sätt att lagra stora mängder data i molnet, men de är användbara i något annat syfte. 
    
    Azure Blob storage är användbart för massiv skala, molnbaserade program som behöver lagra Ostrukturerade data. För att maximera prestanda och skalning, är Azure Blob storage en enklare storage abstraktion än något filsystem. Du kan komma åt Azure Blob storage bara via REST-baserade klientbibliotek (eller direkt via REST-baserade protokollet).

    Azure Files är särskilt ett filsystem. Azure Files har alla fil-sammanfattningar som du känner till och från flera år i samarbete med den lokala operativsystem. Som Azure Blob storage erbjuder Azure Files ett REST-gränssnitt och REST-baserade klientbibliotek. Till skillnad från Azure Blob storage erbjuder Azure Files SMB-åtkomst till Azure-filresurser. Du kan montera en Azure-filresurs direkt på Windows, Linux eller macOS, antingen lokalt eller i virtuella datorer, utan att skriva någon kod eller koppla eventuella särskilda drivrutiner till filsystemet med hjälp av SMB. Du kan också cachelagra Azure-filresurser på den lokala filservrar med hjälp av Azure File Sync för snabb åtkomst nära där data används. 
   
    En mer detaljerad beskrivning om skillnaderna mellan Azure Files och Azure Blob storage finns i [avgöra när du ska använda Azure Blob storage, Azure Files eller Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Läs mer om Azure Blob storage i [introduktion till Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Varför ska jag använda en Azure-filresurs i stället för Azure Disks?**  
    En disk i Azure-diskar är helt enkelt en disk. Om du vill få ut värde från Azure-diskar, måste du ansluta en disk till en virtuell dator som körs i Azure. Azure-diskar som kan användas för allt som du använder en disk för på en lokal server. Du kan använda den som en OS-disk för system, växlingsutrymme för ett operativsystem eller dedikerad lagring för ett program. En intressant Azure Disks används för att skapa en server i molnet för att använda på samma plats där du kan använda en Azure-filresurs. Distribuera en filserver i Azure Virtual Machines är en högpresterande sättet att få fillagring i Azure när du behöver distributionsalternativ som för närvarande inte stöds av Azure Files (till exempel NFS-protokollet support eller premium-lagring). 

    Kör en server med Azure-diskar som backend-lagring vanligtvis är dock mycket dyrare än att använda en Azure-filresurs olika orsaker. Först, förutom att betala för disklagring, betalar du även måste för kostnaderna för att använda en eller flera virtuella Azure-datorer. Andra: du kan också måste hantera de virtuella datorer som används för att köra filservern. Exempelvis kan ansvarar du för OS-uppgraderingar. Slutligen, om du slutligen kräver att data cachelagrade lokalt, det är upp till dig att konfigurera och hantera replikeringstekniker, till exempel distribuerade replikering DFSR (File System), att aktivera den här funktionen.

    En metod för att få bäst av både Azure Files och en filserver som är värd för virtuella datorer i Azure (förutom att använda Azure-diskar som backend-lagring) är att installera Azure File Sync på en filserver som finns på ett moln VM. Om Azure-filresursen är i samma region som din filserver, kan du aktivera molnet blobnivåindelning och ställer in volymen av ledigt utrymme i procent till maximalt (99%). Detta säkerställer minimal duplicering av data. Du kan också använda alla program som du vill med dina filservrar som stöd för program som kräver NFS-protokollet.

    Information om ett alternativ för hur du konfigurerar en server för höga prestanda och hög tillgänglighet i Azure finns i [distribuera IaaS VM-gästkluster i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer detaljerad beskrivning av skillnaderna mellan Azure Files och Azure-diskar, se [avgöra när du ska använda Azure Blob storage, Azure Files eller Azure Disks](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Läs mer om Azure-diskar i [översikt över Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Hur kommer jag igång med Azure Files?**  
   Det är enkelt att komma igång med Azure Files. Först [skapa en filresurs](storage-how-to-create-file-share.md), och sedan montera den i önskat operativsystem: 

    * [Montera i Windows](storage-how-to-use-files-windows.md)
    * [Montera i Linux](storage-how-to-use-files-linux.md)
    * [Montera i macOS](storage-how-to-use-files-mac.md)

   En mer detaljerad vägledning om hur du distribuerar en Azure-filresurs att ersätta filresurser för produktion i din organisation, se [planera för distribution av Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Vilka alternativ för lagringsredundans stöds av Azure Files?**  
    Azure Files stöder för närvarande, lokalt redundant lagring (LRS), zonredundant lagring (ZRS) och geo-redundant lagring (GRS). Vi planerar att stödja läsåtkomst till geografiskt redundant (RA-GRS)-lagring i framtiden, men vi har inte tidslinjer för att dela just nu.

* <a id="tier-options"></a>
**Vilka nivåer stöds i Azure Files?**  
    Azure Files stöder för närvarande endast standardlagring-nivå. Vi har inte tidslinjer för att dela för premium storage och lågfrekvent lagring stöder just nu. 
    
    > [!NOTE]
    > Du kan inte skapa Azure-filresurser från endast blob storage-konton eller från premium storage-konton.

* <a id="give-us-feedback"></a>
**Nu vill jag verkligen finns i en specifik funktion som läggs till i Azure-filer. Kan du lägga till den?**  
    Azure Files-teamet vill ha tagit reda på alla feedback som du har om vår tjänst. Du rösta på funktionsförfrågningar på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi tittar framåt till delighting du till många nya funktioner.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Vilka regioner stöds för Azure File Sync?**  
    Lista över tillgängliga regioner finns på den [regiontillgänglighet](storage-sync-files-planning.md#region-availability) avsnitt av Azure File Sync-planering guide. Vi lägger kontinuerligt till stöd för ytterligare regioner, inklusive icke-offentlig regioner.

* <a id="cross-domain-sync"></a>
**Kan jag ha domänanslutna och icke-domänanslutna servrar i samma synkroniseringsgruppen?**  
    Ja. En synkroniseringsgrupp kan innehålla serverslutpunkter som har olika medlemskap i Active Directory, även om de inte är ansluten till domänen. Den här konfigurationen fungerar tekniskt men rekommenderar vi inte detta som en typisk konfiguration eftersom åtkomstkontrollistor (ACL) som har definierats för filer och mappar på en server inte kanske kan tillämpas av andra servrar i synkroniseringsgruppen. För bästa resultat rekommenderar vi synkroniseras mellan servrar som finns i samma Active Directory-skog, mellan servrar som finns i olika Active Directory-skogar men som etablerade förtroenderelationer eller mellan servrar som inte ingår i en domän. Vi rekommenderar att du inte använder en blandning av de här konfigurationerna.

* <a id="afs-change-detection"></a>
**Jag har skapat en fil direkt i min Azure-filresurs med hjälp av SMB eller i portalen. Hur lång tid tar det för den fil som ska synkroniseras till servrar i synkroniseringsgruppen?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Vad händer om samma fil ändras på två servrar på ungefär samma gång?**  
    Azure File Sync använder en enkel konfliktlösning-strategi: Vi lagrar både ändringar till filer som ändras på två servrar på samma gång. Nyligen skriftliga ändringen behåller det ursprungliga filnamnet. Den äldre filen har ”källdatorn” och antalet konflikt läggas till i namnet. Det följer den här taxonomi: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< EXT\>  

    Första konflikter mellan CompanyReport.docx skulle bli CompanyReport CentralServer.docx om CentralServer är där det äldre Skriv inträffade. Andra konflikten namnet CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Stöds geo-redundant lagring för Azure File Sync?**  
    Ja, Azure Files stöder både lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). Om det uppstår redundans GRS mellan länkade regioner, rekommenderar vi att du ska hantera den nya regionen som en säkerhetskopia av data endast. Azure File Sync börjar automatiskt inte synkroniseras med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>
**Varför inte den *storlek på disken* -egenskapen för en fil matchar den *storlek* egenskapen när du har använt Azure File Sync?**  
    Windows Utforskaren exponerar två egenskaper för att representera storleken på en fil: **storlek** och **storlek på disken**. De här egenskaperna skriftspråk i betydelse. **Storlek** representerar fullständig storleken på filen. **Storlek på disken** representerar storleken på fildataströmmen som lagras på disken. Värden för dessa egenskaper kan variera för olika skäl, till exempel komprimering, användning av Datadeduplicering eller molnnivå med Azure File Sync. Om en fil är nivåindelad till en Azure-filresurs, är storleken på disken noll, eftersom fildataströmmen lagras i din Azure-filresurs och inte på disken. Det är också möjligt för en fil som ska innehålla delvis nivåindelade (eller delvis återkallade). I en delvis nivåindelad fil är en del av filen på disken. Detta kan inträffa när filer läses delvis av program, t.ex. multimedia spelare eller zip-verktyg. 

* <a id="is-my-file-tiered"></a>
**Hur vet jag om en fil har nivåindelade?**  
    Det finns flera sätt att kontrollera om en fil nivåer till din Azure-filresurs:
    
   *  **Kontrollera filattribut för filen.**
     Gör detta genom att högerklicka på en fil, gå till **information**, och bläddra till den **attribut** egenskapen. En nivåindelad fil har de följande attribut:     
        
        | Attributet bokstav | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen bör säkerhetskopieras av programvara för säkerhetskopiering. Det här attributet anges alltid, oavsett om filen nivåer eller helt lagras på disk. |
        | P | Sparse-fil | Anger att filen är en sparse-fil. En sparse-fil är en särskild typ av fil som NTFS erbjuder effektiv när filen på disk stream huvudsakligen är tom. Azure File Sync använder sparse-filer eftersom en fil nivåer helt eller delvis återkallas. Fildataströmmen är en fullständigt nivåindelad fil lagras i molnet. I en delvis återställd fil, som en del av filen finns redan på disk. Om en fil är fullständigt kanske till disk, Azure File Sync konverteras från en sparse-fil till en vanlig fil. |
        | L | Referenspunkt | Anger att filen har en referenspunkt. En referenspunkt är en särskild pekare för användning av ett filsystemsfilter. Azure File Sync använder referenspunkter för att definiera till Azure File Sync filsystemsfilter (StorageSync.sys) cloud platsen där filen lagras. Det stöder sömlös åtkomst. Användarna behöver inte veta att Azure File Sync som används eller hur du får åtkomst till filen i din Azure-filresurs. När en fil återställs helt bort Azure File Sync referenspunkten från filen. |
        | O | Offline | Anger att en eller flera av dess innehåll inte lagras på disk. När en fil återställs fullständigt Azure File Sync tar du bort det här attributet. |

        ![Dialogrutan Egenskaper för en fil med fliken information om markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Du kan se attributen för alla filer i en mapp genom att lägga till den **attribut** till tabellen visningen av Utforskaren. Gör detta genom att högerklicka på en befintlig kolumn (till exempel **storlek**), Välj **mer**, och välj sedan **attribut** från den nedrullningsbara listan.
        
   * **Använd `fsutil` att söka efter referenspunkter för en fil.**
       Enligt beskrivningen i föregående alternativ, har en nivåindelad fil alltid en referenspunkt set. En referenspunkt pekare är en särskild pekare för Azure File Sync filsystemsfilter (StorageSync.sys). Om du vill kontrollera om en fil har en referenspunkt, i en upphöjd kommandotolk eller PowerShell-kommandotolk, kör den `fsutil` verktyget:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen har en referenspunkt, du kan förvänta dig att se **referenspunkter Taggvärde: 0x8000001e**. Den här hexadecimalt värde är punktvärdet referenspunkter som ägs av Azure File Sync. Utdata innehåller också referensdata som representerar sökvägen till din fil på din Azure-filresurs.

        > [!WARNING]  
        > Den `fsutil reparsepoint` verktyget kommandot har också möjlighet att ta bort en referenspunkt. Kör inte det här kommandot om inte det tekniska teamet för Azure File Sync ber dig. Kör det här kommandot kan resultera i dataförlust. 

* <a id="afs-recall-file"></a>**En fil som jag vill använda har varit nivåindelade. Hur kan jag återställa filen till disk för att använda den lokalt?**  
    Det enklaste sättet att återställa en fil till disk är att öppna filen. Azure File Sync filsystemsfilter (StorageSync.sys) hämtas sömlöst från din Azure-filresurs utan något arbete från din sida. För filtyper som kan vara delvis läsning från, t.ex multimediafiler eller ZIP-filer, öppna en fil inte ladda ned hela filen.

    Du kan också använda PowerShell om du vill tvinga en fil som ska återställas. Det här alternativet kan vara användbart om du vill återställa flera filer samtidigt, till exempel alla filer i en mapp. Öppna en PowerShell-session till noden där Azure File Sync är installerad och kör följande PowerShell-kommandon:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Hur gör jag för att tvinga en fil eller katalog för att vara nivåindelad?**  
    När funktionen cloud lagringsnivåer aktiveras molnlagringsnivåer automatiskt nivåer filer baserat på senaste åtkomst och ändra gånger för att uppnå den volymen ledigt utrymme i procent som anges på molnslutpunkten. Ibland, men kanske du vill tvinga en fil till tier manuellt. Detta kan vara användbart om du sparar en stor fil som du inte planerar att använda igen under en längre tid och du vill ha det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan tvinga lagringsnivåer med hjälp av följande PowerShell-kommandon:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Hur är *ledigt utrymme på volym* tolkas när jag har flera serverslutpunkter på en volym?**  
    Om det finns fler än en serverslutpunkt på en volym, är effektiva volym ledigt utrymme tröskelvärdet den största ledigt utrymme på volym anges i valfri serverslutpunkt på den volymen. Filer nivåindelas enligt deras användningsmönster oavsett vilken serverslutpunkt som de tillhör. Till exempel om du har två server-slutpunkter på en volym, slutpunkt 1 och Endpoint2, är där slutpunkt 1 har ett tröskelvärde för ledigt utrymme för volymen med 25% och Endpoint2 har ett tröskelvärde för ledigt utrymme av volymen på 50% tröskelvärde för ledigt utrymme på volymen för både serverslutpunkter 50%.

* <a id="afs-files-excluded"></a>
**Vilka filer och mappar undantas automatiskt av Azure File Sync?**  
    Som standard omfattar Azure File Sync inte följande filer:
    * Desktop.ini
    * thumbs.DB
    * ehthumbs.DB
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Följande mappar undantas också som standard:

    * \System volume Information
    * \$PAPPERSKORGEN. BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Kan jag använda Azure File Sync med Windows Server 2008 R2, Linux eller enheten nätverksansluten lagring (NAS)?**  
    Azure File Sync stöder för närvarande endast Windows Server 2016 och Windows Server 2012 R2. Vi har inte några andra planer kan vi dela just nu, men vi är öppen för stöd för ytterligare plattformar baserat på kundernas behov. Berätta för oss på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) vilka plattformar du vill bli kontaktad för.

* <a id="afs-tiered-files-out-of-endpoint"></a>
**Varför finns nivåindelade filer utanför den slutpunkten namnrymden?**  
    Innan Azure File Sync-agentversion 3 blockeras Azure File Sync flytt av nivåindelade filer utanför Serverslutpunkten men på samma volym som Serverslutpunkten. Kopieringsåtgärder, flyttas av icke-nivåindelade filer och flyttas av nivåindelade andra volymer har påverkas inte. Orsaken till detta var det underförstådda antagandet som Utforskaren och andra Windows-API: er har som flyttar åtgärder på samma volym är (nästan) instanenous Byt namn på åtgärder. Det innebär att flytta gör Utforskaren eller andra move-metoder (till exempel från kommandoraden eller PowerShell) visas inte svarar medan Azure File Sync återställer data från molnet. Från och med [Azure File Sync-agentversion 3.0.12.0](storage-files-release-notes.md#agent-version-30120), Azure File Sync kan du flytta en nivåindelad fil utanför Serverslutpunkten. Vi undvika negativa effekter som vi nämnde tidigare genom att nivåindelade filen finns som en nivåindelad fil utanför Serverslutpunkten och sedan återställa filen i bakgrunden. Det innebär som flyttas på samma volym är instaneous och vi gör allt arbete för att återställa filen till disk när förflyttningen har slutförts. 

* <a id="afs-do-not-delete-server-endpoint"></a>
**Jag har problem med Azure File Sync på Min server (sync molnet lagringsnivåer, etc). Ta bort och återskapa min serverslutpunkt**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="security-authentication-and-access-control"></a>Säkerhet, autentisering och åtkomstkontroll
* <a id="ad-support"></a>
**Är Active Directory-baserad autentisering och åtkomstkontroll som stöds av Azure Files?**  
    Azure Files erbjuder två metoder för att hantera åtkomstkontroll:

    - Du kan använda signaturer för delad åtkomst (SAS) för att generera token som har specifika behörigheter och som är giltiga för ett angivet tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en specifik fil som har en giltighetstid för 10: e minut. Alla som har token när token är giltig har skrivskyddad åtkomst till filen under dessa 10 minuter. Nycklar för signatur för delad åtkomst stöds för närvarande endast via REST API eller klientbibliotek. Med hjälp av lagringskontonycklar måste du montera Azure-filresursen via SMB.

    - Azure File Sync bevarar och replikerar alla godtyckliga ACL: er eller DACL: er och (om Active Directory-baserad eller lokal) till alla serverslutpunkter som synkroniseras med. Eftersom Windows Server kan redan autentisera med Active Directory, Azure File Sync är ett effektivt stoppa gap-alternativ tills fullständigt stöd för Active Directory-baserad autentisering och ACL-stöd anländer.

    För närvarande stöder Azure Files inte Active Directory direkt.

* <a id="encryption-at-rest"></a>
**Hur kan jag kontrollera att min Azure-filresurs är krypterade i vila?**  
    Azure Storage Service Encryption håller håller på att aktiveras som standard i alla regioner. För dessa regioner behöver du inte vidta några åtgärder för att aktivera kryptering. Andra regioner finns [Server side encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hur kan jag ge åtkomst till en viss fil med hjälp av en webbläsare?**  
    Du kan använda signaturer för delad åtkomst för att generera token som har specifika behörigheter och som är giltiga för ett angivet tidsintervall. Du kan till exempel generera en token som ger läsbehörighet till en specifik fil eller för en angiven tidsperiod. Alla som har tillgång till URL: en kan komma åt filen direkt från en webbläsare medan token är giltig. Du kan enkelt skapa en signaturnyckel för delad åtkomst från ett användargränssnitt som Storage Explorer.

* <a id="file-level-permissions"></a>
**Är det möjligt att ange skrivskyddad eller lässkyddad behörighet för mappar inom resursen?**  
    Om du monterar filresursen med SMB kan du inte mappnivå kontroll över behörigheter. Om du skapar en signatur för delad åtkomst med hjälp av REST API eller klientbibliotek, kan du dock ange skrivskyddad eller lässkyddad behörighet för mappar inom resursen.

* <a id="ip-restrictions"></a>
**Kan jag implementera IP-begränsningar för en Azure-filresurs?**  
    Ja. Åtkomst till din Azure-filresurs kan begränsas på lagringskontonivån. Mer information finns i [konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Vilka data efterlevnadsprinciper har stöd för Azure Files?**  
   Azure Files körs ovanpå samma lagringsarkitektur som används i andra lagringstjänster i Azure Storage. Azure Files gäller efterlevnadsprinciper för samma data som används i andra Azure-lagringstjänster. Mer information om kompatibilitet för Azure Storage-data som du kan hämta och referera till den [dataskydd för Microsoft Azure-dokumentet](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409), och gå till den [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Lokal åtkomst
* <a id="expressroute-not-required"></a>
**Måste jag använda Azure ExpressRoute för att ansluta till Azure Files eller om du vill använda Azure File Sync lokalt?**  
    Nej. ExpressRoute är inte kräver åtkomst till en Azure-filresurs. Om du monterar en Azure-filresurs direkt lokalt, alla som krävs är att ha port 445 (TCP utgående) öppna för internet-åtkomst (detta är den port som använder SMB för att kommunicera). Om du använder Azure File Sync allt som krävs är port 443 (TCP utgående) för HTTPS-åtkomst (utan SMB krävs). Men du *kan* använda ExpressRoute med något av dessa åtkomstalternativ.

* <a id="mount-locally"></a>
**Hur kan jag montera en Azure-filresurs på datorn lokalt?**  
    Du kan montera filresursen med SMB-protokollet om port 445 (TCP utgående) är öppen och klienten stöder SMB 3.0-protokollet (t.ex, om du använder Windows 10 eller Windows Server 2016). Om port 445 blockeras av organisationens princip eller av Leverantören, kan du använda Azure File Sync för att få åtkomst till din Azure-filresurs.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hur delar jag säkerhetskopiera min Azure?**  
    Du kan använda periodiska [resursögonblicksbilder](storage-snapshots-files.md) för skydd mot oavsiktliga borttagningar. Du kan också använda AzCopy, Robocopy eller ett verktyg för säkerhetskopiering från tredje part som kan säkerhetskopiera en monterad filresurs. Azure Backup erbjuder säkerhetskopiering av Azure Files. Läs mer om [säkerhetskopiera Azure-filresurser med Azure Backup](https://docs.microsoft.com/en-us/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Ögonblicksbilder av filresurser
### <a name="share-snapshots-general"></a>Dela ögonblicksbilder: Allmänt
* <a id="what-are-snaphots"></a>
**Vad är ögonblicksbilder av filresurser?**  
    Du kan använda Azure ögonblicksbilder av filresurser för att skapa en skrivskyddad version av dina filresurser. Du kan också använda Azure Files för att kopiera en tidigare version av säkerhetskopian innehåll till samma resurs, till en annan plats i Azure eller lokalt för flera ändringar. Läs mer om ögonblicksbilder av filresurser i den [dela ögonblicksbild översikt](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Var lagras mina resursögonblicksbilder?**  
    Ögonblicksbilder av filresurser lagras i samma lagringskonto som filresursen.

* <a id="snapshot-perf-impact"></a>
**Finns det några konsekvenser för prestanda för att använda ögonblicksbilder?**  
    Ögonblicksbilder av filresurser har inte någon prestanda försämras.

* <a id="snapshot-consistency"></a>
**Är resursögonblicksbilder programkonsekventa?**  
    Nej, ögonblicksbilder av filresurser är inte konsekventa. Användaren måste tömma skrivningarna från programmet till resursen innan du tar ögonblicksbilden för resursen.

* <a id="snapshot-limits"></a>
**Finns det någon gräns för antalet ögonblicksbilder av filresurser kan jag använda?**  
    Ja. Azure Files kan behålla upp till 200 ögonblicksbilder. Ögonblicksbilder av filresurser räknas inte in i resurs-kvot så det finns ingen gräns totalt utrymme som används av alla ögonblicksbilder per filresurs. Lagringskontogränser gäller fortfarande. Efter 200 ögonblicksbilder, måste du ta bort äldre ögonblicksbilder för att skapa nya resursögonblicksbilder.
* <a id="snapshot-cost"></a>
**Hur mycket delar kostnaden för ögonblicksbilden?**  
    Standard transaktion och standardlagring kostnaden gäller för ögonblicksbild. Ögonblicksbilder är inkrementell sin natur. Den grundläggande ögonblicksbilden är resursen själva. Alla efterföljande ögonblicksbilder är inkrementell och lagras endast diff från tidigare ögonblicksbild. Det innebär att deltaändringar som visas på fakturan blir minimal om omsättning av arbetsbelastning är minimal. Se [prissidan](https://azure.microsoft.com/pricing/details/storage/files/) för Standard Azure Files information om priser. Idag är sättet att titta på storleken som används av ögonblicksbild av en resurs är genom att jämföra faktureras kapacitet med kapacitet som används. Vi arbetar med verktyg för att förbättra reporting.


### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder
* <a id="file-snaphsots"></a>
**Kan jag skapa ögonblicksbild av enskilda filer?**  
    Ögonblicksbilder av filresurser skapas på filnivå för resursen. Du kan återställa enskilda filer från ögonblicksbilden av filresursen, men du kan inte skapa ögonblicksbilder av filresurser på filnivå. Men om du har utfört en resursnivå resursögonblicksbild och du vill visa en lista över ögonblicksbilder av filresurser där en viss fil har ändrats, kan du göra det under **tidigare versioner** på en Windows-monterad resurs. 
    
    Om du behöver en funktion för ögonblicksbild av filen kan berätta för oss på [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Kan jag skapa ögonblicksbilder av en krypterad filresurs?**  
    Du kan ta en ögonblicksbild av Azure-filresurser som har kryptering i vila aktiverat. Du kan återställa filer från en ögonblicksbild av en resurs till en krypterad filresurs. Om din filresurs krypteras krypteras dina resursögonblicksbild också.

* <a id="geo-redundant-snaphsots"></a>
**Är Mina resursögonblicksbilder geo-redundant?**  
    Ögonblicksbilder av filresurser har samma redundans som Azure-filresursen som de togs. Om du har valt geo-redundant lagring för ditt konto, lagras din filresurs ögonblicksbild också redundant i den parade regionen.

### <a name="manage-share-snapshots"></a>Hantera ögonblicksbilder
* <a id="browse-snapshots-linux"></a>
**Kan jag Bläddra min ögonblicksbilder av filresurser från Linux?**  
    Du kan använda Azure CLI 2.0 för att skapa, visa, bläddra och återställa ögonblicksbilder i Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan jag kopiera ögonblicksbilder till ett annat lagringskonto?**  
    Du kan kopiera filer från resursögonblicksbilder till en annan plats, men du kan inte kopiera ögonblicksbilder själva.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från resursögonblicksbilder
* <a id="promote-share-snapshot"></a>
**Kan jag flytta upp en ögonblicksbild till grundläggande resursen?**  
    Du kan kopiera data från en ögonblicksbild till andra mål. Du kan inte befordra en ögonblicksbild till grundläggande resursen.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan jag återställa data från ögonblicksbilden för resursen till ett annat lagringskonto?**  
    Ja. Filer från en ögonblicksbild kan kopieras till den ursprungliga platsen eller till en annan plats som innehåller samma lagringskonto eller ett annat lagringskonto i samma region eller i olika regioner. Du kan också kopiera filer till en lokal plats eller i andra moln.    
  
### <a name="clean-up-share-snapshots"></a>Rensa resursögonblicksbilder
* <a id="delete-share-keep-snapshots"></a>
**Kan jag ta bort min resursen men inte ta bort min resursögonblicksbilder?**  
    Om du har active resursögonblicksbilder på nätverksresursen, kan du ta bort din resurs. Du kan använda ett API för att ta bort resursögonblicksbilder, tillsammans med resursen. Du kan också radera både ögonblicksbilder och resursen i Azure-portalen.

* <a id="delete-share-with-snapshots"></a>
**Vad händer med Mina resursögonblicksbilder om jag tar bort mitt lagringskonto?**  
    Om du tar bort ditt lagringskonto tas ögonblicksbilder också bort.

## <a name="billing-and-pricing"></a>Fakturering och priser
* <a id="vm-file-share-network-traffic"></a>
**Räknas nätverkstrafik mellan en Azure-dator och en Azure filresurs som extern bandbredd som debiteras till prenumerationen?**  
    Om filresursen och den virtuella datorn finns i samma Azure-region, finns det inga ytterligare avgifter för trafik mellan filresursen och den virtuella datorn. Om filresursen och den virtuella datorn finns i olika regioner, debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>
**Hur mycket delar ögonblicksbilder kostnad?**  
     I förhandsversionen är gratis för kapacitet för ögonblicksbilder för resursen. Standardlagring utgång och transaktion kan tillkomma. Efter allmän tillgänglighet debiteras prenumerationer för kapacitet och transaktioner på resursögonblicksbilder.
     
     Resursögonblicksbilder är inkrementell sin natur. Den grundläggande resursögonblicksbild är resursen själva. Alla efterföljande resursögonblicksbilder är inkrementell och lagra bara skillnad från föregående ögonblicksbilden för resursen. Du debiteras bara för det ändrade innehållet. Om du har en resurs med 100 GiB data men endast 5 GiB har ändrats sedan den senaste ögonblicksbilden för resursen, ögonblicksbilden för resursen förbrukar endast 5 ytterligare GiB och du debiteras för 105 GiB. Mer information om transaktioner och kostnader för standard utgående trafik finns i den [prissidan](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skala och prestanda
* <a id="files-scale-limits"></a>
**Vilka är gränserna för skalning av Azure Files?**  
    Information om skalbarhets- och prestandamål för Azure Files finns i [skalbarhets- och prestandamål i Azure Files](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Jag behöver en större filresurs än Azure Files erbjuder för närvarande. Kan jag öka storleken på min Azure-filresurs?**  
    Nej. Den maximala storleken för en Azure-filresurs är 5 TiB. Det här är för närvarande en hård gräns som vi kan justera. Vi arbetar på en lösning för att öka resursstorleken på till 100 TiB, men vi har inte tidslinjer för att dela just nu.

* <a id="open-handles-quota"></a>
**Hur många klienter har åtkomst till samma fil samtidigt?**   
    Det finns en kvot på 2 000 öppna referenser i en enda fil. När du har 2 000 öppna referenser visas ett felmeddelande som säger kvoten har uppnåtts.

* <a id="zip-slow-performance"></a>
**Min går långsamt när jag packa upp filer i Azure Files. Vad ska jag göra?**  
    För att överföra stora mängder filer till Azure Files, rekommenderar vi att du använder AzCopy (för Windows, i förhandsversion för Linux och UNIX) eller Azure PowerShell. Dessa verktyg har optimerats för nätverksöverföring.

* <a id="slow-perf-windows-81-2012r2"></a>
**Varför är min långsamma prestanda när du har monterat min Azure-filresurs på Windows Server 2012 R2 eller Windows 8.1?**  
    Det finns ett känt problem när du monterar en Azure-filresurs på Windows Server 2012 R2 och Windows 8.1. Problemet har korrigerade i April 2014 kumulativ uppdatering för Windows 8.1 och Windows Server 2012 R2. Se till att alla instanser av Windows Server 2012 R2 och Windows 8.1 har korrigeringen tillämpas för optimala prestanda. (Du bör alltid ta emot Windows-uppdateringar via Windows Update.) Mer information finns i den tillhörande Microsoft Knowledge Base-artikeln [långsamma prestanda när du har åtkomst till Azure Files från Windows 8.1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och samverkan med andra tjänster
* <a id="cluster-witness"></a>
**Kan jag använda min Azure-filresurs som en *filresursvittne* för min Windows Server Failover-kluster?**  
    Den här konfigurationen stöds för närvarande inte för en Azure-filresurs. Mer information om hur du anger detta för Azure Blob storage finns i [distribuera ett Molnvittne för ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Kan jag montera en Azure-filresurs på en Azure Container-instans?**  
    Ja, Azure-filresurser är ett bra alternativ när du vill bevara information utöver livslängden för en behållarinstans. Mer information finns i [montera en Azure-filresurs med Azure Container instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Finns det en åtgärd för namnbyten i REST API?**  
    Inte just nu.

* <a id="nested-shares"></a>
**Kan jag konfigurera kapslade resurser? Med andra ord en resurs under en resurs?**  
    Nej. Filresursen *är* den virtuella drivrutin som du kan montera, så kapslade resurser inte stöds.

* <a id="ibm-mq"></a>
**Hur kan jag använda Azure Files med IBM MQ?**  
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder konfigurera Azure Files med IBM-tjänsten. Mer information finns i [hur du ställer in en IBM MQ flera instanser köhanteraren med Microsoft Azure Files-tjänsten](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se också
* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsök Azure File Sync](storage-sync-files-troubleshoot.md)
