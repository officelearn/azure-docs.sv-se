---
title: "Vanliga frågor och svar om Azure-filer | Microsoft Docs"
description: "Få svar på vanliga frågor och svar om Azure-filer."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Vanliga frågor och svar om Azure-filer
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via branschstandard [Server Message Block (SMB) protokollet](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (även kallat Common Internet File System eller CIFS). Azure-filresurser kan monteras samtidigt av molndistributioner eller lokala distributioner av Windows, Linux och macOS. Azure-filresurser kan dessutom cachelagras på Windows-servrar med Azure filsynkronisering (förhandsversion) för snabb åtkomst nära där data används.

Den här artikeln beskriver några vanliga frågor om Azure Files funktioner och funktionalitet, inklusive Azure filsynkronisering. Om du inte hittar svar på din fråga här kan Tveka inte att nå oss via följande kanaler (i växande ordning):

1. I avsnittet kommentarer i den här artikeln.
2. [Azure Storage-Forum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft Support: Om du vill skapa ett nytt supportärende, gå till ”hjälp + support” på Azure portal och klicka på ”ny supportbegäran”.

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>**Varför är Azure Files användbar?**  
   Azure Files kan du skapa filresurser i molnet utan att behöva hantera kostnader för en fysisk server eller enhet-enhet. Det innebär att du kan lägga mindre tid tillämpa operativsystemuppdateringar och ersätta skadade diskar – vi gör allt arbete monotont för dig. Läs mer om Azure-filer kan hjälpa med scenarier i [varför Azure-filer är användbart](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>**Vad är olika sätt att komma åt filer i Azure-filer?**  
    Du kan montera filresursen på den lokala datorn med hjälp av SMB 3.0-protokollet eller använda verktyg som [Lagringsutforskaren](http://storageexplorer.com/) kan komma åt filer i filresursen. Från programmet, kan du använda lagringsklientbiblioteken REST API: er, PowerShell eller CLI att komma åt dina filer i Azure-filresurs.

* <a id="what-is-afs"></a>**Vad är Azure filsynkronisering?**  
    Azure filsynkronisering kan du centralisera din organisations filresurser i Azure-filer utan att ge flexibilitet, prestanda och kompatibilitet för en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

* <a id="files-versus-blobs"></a>**Varför ska jag använda en Azure-filresurs jämfört med Azure Blob storage för Mina data?**  
    Azure Files och Azure Blob storage både ger dig ett sätt att lagra stora mängder data i molnet, men är användbara i något annat syfte. Azure Blob storage är användbart för massiv skala, moln-intern program som behöver lagra Ostrukturerade data. Om du vill maximera prestanda och skalning är Azure Blob storage en enklare lagring abstraktion än ett true filsystem. Azure Blob storage kan dessutom endast nås via REST-baserade klientbibliotek (eller direkt via REST-baserat protokoll).

    Azure Files å andra sidan särskilt syftar till att vara ett filsystem, med alla filen sammanfattningar du känner till och från flera års lokalt operativsystem. Azure Files erbjuder ett REST-gränssnitt och REST-baserade klientbibliotek som Azure Blob storage, men till skillnad från Azure Blob storage erbjuder Azure-filer även SMB-åtkomst till Azure-filresurser. Det innebär att du direkt montera en filresurs på Azure på Windows, Linux eller macOS, lokalt eller i molnet virtuella datorer, utan att behöva skriva någon kod eller koppla några särskilda drivrutiner till filsystemet. Azure-filresurser kan cachelagras på lokala filservrar med hjälp av Azure filsynkronisering för Snabbåtkomst nära där data används. 
   
    En mer ingående diskussion om skillnaderna mellan Azure-filer och Azure Blob storage finns [avgöra när du behöver använda Azure Blob storage, Azure-filer eller Azure-diskar](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Läs mer om Azure Blob storage i [introduktion till Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Varför ska jag använda en Azure-filresurs respektive Azure-diskar?**  
    En Azure-disken är precis som en disk. En fristående disk, är inte användbar - att hämta ett värde utanför en Azure-disken måste du ansluta till den virtuella datorn körs i Azure. Azure-diskar som kan användas för allt och allt du använder en disk för på en lokal server: som en OS-systemdisken växlingsutrymme för ett operativsystem eller som dedikerad lagring för ett program. En intressant för Azure-diskar används för att skapa en filserver i molnet för användning i exakt samma placerar du kan använda en filresurs på Azure. Distribuera en filserver i virtuella Azure-datorer är en fantastisk, högpresterande sätt att hämta fillagring i Azure när du behöver distributionsalternativ som stöds för närvarande inte av Azure-filer (till exempel NFS-protokollet-stöd- eller premium-lagring). 

    Å andra sidan blir kör en filserver med Azure-diskar som backend lagring vanligtvis mycket dyrare än att använda en Azure-filresurs av flera skäl. Först, förutom att betala för disklagring, du måste också betalar för kostnaderna för att använda en eller flera virtuella Azure-datorer. Andra, måste du också hantera de virtuella datorerna som används för att köra filservern, till exempel som ansvarar för OS-uppgraderingar, osv. Slutligen om du behöver slutligen data cachelagras lokalt, är det till dig att konfigurera och hantera tekniker för replikering (till exempel Distributed File System Replication) så att det sker.

    Ett intressanta sätt att få bästa av både Azure och en filserver som finns i virtuella Azure-datorer med Azure-diskar som backend-lagring är att installera Azure filsynkronisering på molnet VM-värd för filservern. Om Azure-filresursen finns i samma region som din filserver, kan du aktivera molnet skiktning och ange volymen ledigt utrymme procent högsta (99%). Detta säkerställer minimal duplicering av data samtidigt som du kan använda de program som du vill mot dina filservrar sådana något som kräver NFS-protokollet stöder.

    Information på ett sätt att konfigurera en hög prestanda och en filserver med hög tillgänglighet i Azure finns [distribuera IaaS VM-gästkluster i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer ingående diskussion om skillnaderna mellan Azure-filer och Azure-diskar finns [avgöra när du behöver använda Azure Blob storage, Azure-filer eller Azure-diskar](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Läs mer om Azure-diskar i [hanterade diskar översikt över Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**Hur kommer jag igång med Azure-filer?**  
    Det är enkelt att komma igång med Azure-filer: bara [skapa en filresurs](storage-how-to-create-file-share.md) och montera i önskat operativsystem: 

    - [Montera i Windows](storage-how-to-use-files-windows.md)
    - [Montera på Linux](storage-how-to-use-files-linux.md)
    - [Montera på macOS](storage-how-to-use-files-mac.md)

    En mer detaljerad vägledning om hur du distribuera en Azure-filresurs ersätta produktion filresurser i din organisation, se [planera för distribution av en Azure-filer](storage-files-planning.md).

* <a id="redundancy-options"></a>**Vilka lagringsalternativ för redundans stöds av Azure-filer?**  
    Azure Files stöder för närvarande endast lokalt redundant lagring (LRS) och geo-redundant lagring (GRS) just nu. Vi planerar att stödja zonredundant lagring (ZRS) och geo-redundant lagring med läsbehörighet (RA-GRS) i framtiden, men har inte tidslinjer att dela just nu.

* <a id="tier-options"></a>**Vilka lagringsnivåer stöds av Azure-filer?**  
    Azure Files stöder för närvarande endast standard lagringsnivå. Vi har för närvarande inte tidslinjer att dela premium och kall support. Observera att det går inte att skapa Azure-filresurser från endast Blob storage-konton eller från Premium storage-konton.

* <a id="give-us-feedback"></a>**Jag verkligen vill se *x* funktion som lagts till i Azure-filer kan du lägga till den?**  
    Azure Files team vill absolut, höra alla feedback som du har om vår tjänst. Kontrollera rösta på funktioner som efterfrågas på den [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi söker vidarebefordra till delighting du med många nya funktioner.

## <a name="azure-file-sync"></a>Azure filsynkronisering
* <a id="afs-region-availability"></a>**Vilka regioner som stöds för närvarande för Azure filsynkronisering (förhandsgranskning)?**  
    Azure filsynkronisering är tillgänglig i västra USA, västra Europa, Östra Australien och Sydostasien. Vi lägger till stöd för fler regioner som vi sträva efter allmän tillgänglighet. För ytterligare information, se [regional tillgänglighet](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**Kan jag ha domänanslutna och icke-domänrelaterade domänanslutna servrar i gruppen samma synkronisering?**  
    Ja, en Sync-grupp kan innehålla Server-slutpunkter som har olika Active Directory-medlemskap, inklusive inte är ansluten till en domän. När konfigurationen fungerar tekniskt, rekommenderar vi inte detta som en vanlig konfiguration som ACL: er som har definierats för filer/mappar på en server kan inte eventuellt tillämpas av andra servrar i gruppen synkronisering. För bästa resultat rekommenderar vi synkroniserar mellan antingen servrar i samma Active Directory-skog, servrar i olika Active Directory-skogar med upprättade förtroenderelationer, eller servrar inte i en domän, men inte en blandning av samtliga av ovanstående.

* <a id="afs-change-detection"></a>**Jag har skapat en fil direkt i min Azure-filresursen via SMB eller via portalen. Hur lång tid tills filen har synkroniserats till servrar i gruppen synkronisering?** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Vad händer om samma fil ändras på två servrar på ungefär samma tillfälle?**  
    Azure filsynkronisering använder en enkel konflikt upplösning strategi: vi behåller båda ändringarna. Den mest nyskrivna behåller det ursprungliga filnamnet. Den äldre filen har på källdatorn och antalet konflikt läggs till i namnet med den här taxonomi: 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Till exempel första konflikter mellan `CompanyReport.docx` skulle bli `CompanyReport-CentralServer.docx` om `CentralServer` där den äldre skrivningen uppstod. Andra konflikten skulle identifieras som `CompanyReport-CentralServer-1.docx`.

* <a id="afs-storage-redundancy"></a>**Stöds geo-redundant lagring (GRS) för Azure filsynkronisering?**  
    Ja, både lokalt redundant lagring (LRS) och geo-redundant lagring (GRS) stöds av Azure filsynkronisering. När det gäller GRS växling mellan parad regioner rekommenderar vi att den nya regionen som en säkerhetskopia av endast data. Azure filsynkronisering startar automatiskt inte synkroniserar med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>**Varför inte den *storlek på disken* -egenskapen för en fil matchar den *storlek* egenskapen när du använder Azure filsynkronisering?**  
    Utforskaren exponerar två egenskaper **storlek** och **storlek på disken** som representerar storleken på en fil. Dessa egenskaper skiljer sig något i betydelse; **Storlek** representerar fullständig storleken på filen, medan **storlek på disken** representerar storleken på filström som lagras på disk. Dessa kan skilja sig av olika orsaker, till exempel komprimering, användning av Datadeduplicering eller i vårt fall, molnet skiktning med Azure filsynkronisering. Om en fil nivåer till en Azure-filresurs, storlek på disken kommer att vara noll eftersom filen dataströmmen inte lagras i din Azure-filresurs på disken. Det är också möjligt för en fil som ska vara delvis nivåindelade (eller delvis återkallade), vilket innebär att en del av filen finns på disken. Detta kan hända när delvis läses av program, till exempel multimedia spelare eller av komprimerar verktyg. 

* <a id="is-my-file-tiered"></a>**Hur vet jag om en fil har nivåindelade?**  
    Det finns flera sätt att kontrollera om en fil nivåer i Azure-filen delar:
    
    1. **Kontrollera filattribut på filen.** Gör du genom att högerklicka på en fil, navigera till **information** och rulla ned till den **attribut** egenskapen. En skiktad fil måste ställa in följande attribut:     
        
        | Attributet bokstav | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen bör säkerhetskopieras av programvara för säkerhetskopiering. Det här attributet anges alltid oavsett om filen är nivåer eller fullständigt lagras på disk. |
        | P | Sparse-fil | Anger att filen är en sparse-fil som är en särskild typ av fil erbjuder NTFS för effektiv användning när filen på disken ström främst är tom. Azure filsynkronisering använder sparse-filer eftersom en fil är fullständigt nivåer, vilket innebär att dess filström bara lagras i molnet eller delvis återkallade, vilket innebär att en del av filen finns redan på disken. Om en fil är fullständigt återkallas till disk Azure filsynkronisering ska konverteras från en sparse-fil till en vanlig fil. |
        | L | Referenspunkt | Anger att filen har en referenspunkt är en särskild pekare för användning av en Filsystemfilter. Azure filsynkronisering använder referenspunkter definiera för Azure filsynkronisering Filsystemfilter (StorageSync.sys) där i molnet filen lagras. Detta gör att sömlös åtkomst utan slutanvändaren även behöva veta Azure filsynkronisering som används och hur du kan få åtkomst till filen i din Azure-filresurs. När en fil återställs fullständigt bort Azure filsynkronisering referenspunkten från filen. |
        | O | Offline | Anger att vissa eller alla av filens innehåll lagras på disk. När en fil återställs fullständigt Azure filsynkronisering tar du bort det här attributet. |

        ![Dialogrutan Egenskaper för en fil med markerad på fliken information](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Det är också möjligt att visa attributen för alla filer i en mapp genom att lägga till den **attribut** till tabellen visningen av filen Exporer. Om du vill göra det, högerklickar du på en befintlig kolumn (exempelvis storlek), Välj **mer** och välj **attribut** från den nedrullningsbara listan.
        
    2. **Använd `fsutil` att söka efter referenspunkter på en fil.** Som nämns i föregående alternativ, har en skiktad fil alltid en referenspunkt eller en särskild pekare för Azure filsynkronisering Filsystemfilter (StorageSync.sys). Du kan kontrollera om en fil har en referenspunkt med den `fsutil` utility på en upphöjd kommandotolk eller PowerShell-session:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen är en referenspunkt, bör du se **referenspunkter Taggvärde: 0x8000001e**. Den här hexadecimalt värde är referenspunkter punktvärdet ägs av Azure filsynkronisering. Utdata innehåller också referenspunkter dela data som representerar sökvägen till filen i din Azure-fil.

        > [!Warning]  
        > Den `fsutil reparsepoint` verktyget kommandot innehåller också möjlighet att ta bort en referenspunkt. Kör inte det här kommandot om du uppmanas av Azure filsynkronisering teknikteamet - kan resultatet förlust av data. 

* <a id="afs-recall-file"></a>**En fil som du vill använda har tagits nivåindelade... hur kan jag återkalla det till disk för användning lokalt?**  
    Det enklaste sättet att återställa till en fil är att öppna den. Azure filsynkronisering Filsystemfilter (StorageSync.sys) kommer sömlöst att hämta filen från Azure-filresurs utan att utföra arbete. För filtyper som kan vara delvis leder lästes från, till exempel multimedia eller zip-filer, öppna en fil inte till nedladdningen av hela filen.

    Det är också möjligt framtvinga en fil som ska återställas med hjälp av PowerShell. Det kan till exempel vara användbart om du vill återkalla många filer samtidigt (till exempel alla filer i en mapp). Öppna ett PowerShell-session till servernod där Azure filsynkronisering är installerat och kör följande PowerShell-kommandon:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**Hur kan jag tvinga en fil eller katalog till nivåindelas?**  
    När aktiverat funktionen molnet lagringsnivåer automatiskt tjänstnivån filer baserat på senaste åtkomst och ändra gånger för att uppnå den volymen ledigt utrymme i procent som anges på slutpunkten moln, men vill ibland du kanske tvinga en fil till tjänstnivån manuellt. Detta kan exempelvis vara användbart om du sparar en stor fil som du inte tänker använda igen under en längre tid och nu vill ledigt utrymme på volymen för andra filer/mappar. Du kan tvinga skiktning med följande PowerShell-kommandon:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Vilka filer och mappar undantas automatiskt av Azure filen synkronisering?**
    Som standard omfattar Azure filsynkronisering inte följande filer:
    
    - Desktop.ini
    - thumbs.DB
    - ehthumbs.DB
    - ~$\*.\*
    - \*.laccdb
    - \*tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    Följande mappar undantas också som standard:

    - \System volume Information
    - \$ÅTERANVÄND. BIN
    - \SyncShareState

* <a id="afs-os-support"></a>**Jag skulle vilja använda Azure filsynkronisering med Windows Server 2008 R2 med Linux eller med enheten NAS - Azure filsynkronisering stöder som?**
    Idag Azure filsynkronisering endast har stöd för Windows Server 2016 och Windows Server 2012 R2. Vi har inte de scheman som vi kan dela just nu, men vi har öppen och intresserad av att stöda ytterligare plattformar baserat på kundernas behov. Låt oss veta vilka plattformar som du skulle vilja oss för att stödja i [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Säkerhet, autentisering och åtkomstkontroll
* <a id="ad-support"></a>**Är Active Directory-baserad autentisering och åtkomstkontroll som stöds av Azure-filer?**  
    Azure-filer kan du hantera åtkomstkontroll på två sätt:

    - Med SAS kan generera du token med särskilda behörigheter som gäller för ett angivet tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en viss fil med 10 minuter upphör att gälla. Alla som har denna token när den är giltig har skrivskyddad åtkomst till filen för de 10 minuterna. SAS-nycklar stöds endast via REST API: et eller klientbiblioteken idag, måste du montera Azure filresursen via SMB med nycklar till lagringskontot.

    - Azure filsynkronisering bevara och replikera alla ACL: er (AD-baserad eller lokala) till alla server-slutpunkter som synkroniseras med. Eftersom Windows Server redan kan autentisera med Active Directory, Azure filsynkronisering kan ge ett bra stoppa lucka mått tills fullt stöd för AD-baserad autentisering och anländer ACL-stöd.

    Azure Files stöder inte Active Directory direkt just nu.

* <a id="encryption-at-rest"></a>**Hur kan jag säkerställa min Azure-filresursen är krypterat i vila?**  
    Kryptering i vila håller på att aktiveras som standard i alla regioner. Du behöver inte göra något för att aktivera kryptering för dessa regioner. Andra regioner finns [kryptering på serversidan](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**Hur ger åtkomst till en viss fil i en webbläsare?**  
    Med SAS kan generera du token med särskilda behörigheter som gäller för ett angivet tidsintervall. Du kan till exempel generera ett token med skrivskyddad åtkomst till en viss fil under en viss tidsperiod. Alla som har denna url kan komma åt filen direkt från en webbläsare när den är giltig. SAS-nycklar går lätt att generera från användargränssnitt som Storage Explorer.

* <a id="file-level-permissions"></a>**Är det möjligt att ange skrivskyddad eller lässkyddad behörighet för mapparna på resursen?**  
    Du har inte den här kontrollnivån över behörigheterna om du monterar filresursen via SMB. Du kan dock åstadkomma detta genom att skapa en signatur för delad åtkomst (SAS) via REST-API:et eller klientbiblioteken.

* <a id="ip-restrictions"></a>**Kan jag implementera IP-begränsningar för en filresurs på Azure?**  
    Ja, åtkomst till din Azure-filresurs kan begränsas på en nivå för storage-konto. Mer information finns i avsnittet [konfigurera brandväggar för Azure-lagring och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**Vilka är de principer för efterlevnad som stöd för Azure-filer?**  
   Azure Files körs ovanpå samma lagringsarkitekturen som andra storage-tjänster i Azure Storage och tillämpar principer för efterlevnad av samma data. Mer information om kompatibilitet för Azure Storage-data som du kan hämta och referera till [dataskydd i Microsoft Azure-dokumentet](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) och [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Lokal åtkomst
* <a id="expressroute-not-required"></a>**Måste jag använda Azure ExpressRoute för att ansluta till Azure-filer eller om du vill använda Azure filsynkronisering lokalt?**  
    Nej, ExpressRoute inte krävs för att få åtkomst till en filresurs på Azure. Om du montera en filresurs på Azure direkt är lokalt, allt som krävs att du har port 445 (TCP utgående) öppen för Internetåtkomst (detta är den port som SMB kommunicerar på). Om du använder Azure filsynkronisering allt som krävs är port 443 (TCP utgående) för HTTPS-åtkomst (ingen SMB krävs). ExpressRoute kan dock användas med antingen åtkomstalternativet om du vill.

* <a id="mount-locally"></a>**Hur kan jag montera en filresurs på Azure på min dator?**  
    Du kan montera filresursen via SMB-protokollet som port 445 (TCP utgående) är öppen och klienten stöder SMB 3.0-protokollet (till exempel du använder Windows 10 eller Windows Server 2016). Om port 445 blockeras av organisationens princip eller av din Internetleverantör, kan du använda Azure filsynkronisering åtkomst till din Azure-filresurs.

## <a name="backup"></a>Säkerhetskopiering
* <a id="backup-share"></a>**Hur gör jag säkerhetskopiera min Azure File dela?**  
    Du kan använda periodiska [dela ögonblicksbilder (förhandsgranskning)](storage-how-to-use-files-snapshots.md) för skydd mot oavsiktliga borttagningar. Du kan använda AzCopy RoboCopy, eller en 3 part säkerhetskopieringsverktyg som kan säkerhetskopiera en monterad filresurs. 

## <a name="share-snapshots"></a>Dela ögonblicksbilder
### <a name="share-snapshots---general"></a>Dela ögonblicksbilder - Allmänt
* <a id="what-are-snaphots"></a>**Vad är resursen filögonblicksbilden?**  
    Azure File share ögonblicksbilder kan du skapa en skrivskyddad versioner av dina filresurser. Du kan också kopiera en äldre version av säkerhetskopian innehåll till samma resurs eller en annan plats i Azure eller lokalt för ändringar. Mer information om resursen ögonblicksbild finns på [dela ögonblicksbild översikt](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**Var lagras mina resursen ögonblicksbilder?**  
    Dela ögonblicksbilder som lagras i samma lagringskonto som filresursen.

* <a id="snapshot-perf-impact"></a>**Finns det några konsekvenser för prestanda?**  
    Dela ögonblicksbilder har inte någon prestanda försämras.

* <a id="snapshot-consistency"></a>**Stämmer dela ögonblicksbilder program?**  
    Nej. Dela ögonblicksbilder stöds inte programmet konsekvent. Användaren måste rensa skrivningar från programmet till resursen innan dela ögonblicksbild.

* <a id="snapshot-limits"></a>**Finns det några gränser för antalet ögonblicksbilder för resursen?**  
    Det finns en gräns på 200 resursen ögonblicksbilder som Azure-filer kan behålla. Dela ögonblicksbilder räknas inte mot kvoten för filresursen så att det finns ingen per resurs gränsen för det totala utrymmet som används av alla ögonblicksbilder för resursen. Lagringskontogränser gäller fortfarande. Efter 200 resursen ögonblicksbilder har äldre ögonblicksbilder som ska tas bort för att skapa nya ögonblicksbilder som resursen.

### <a name="create-share-snapshots"></a>Skapa resursen ögonblicksbilder
* <a id="file-snaphsots"></a>**Kan jag skapa resursen ögonblicksbild av enskilda filer?**  
    Dela ögonblicksbilder skapas på filnivå för resursen. Du kan återställa enskilda filer från filen dela ögonblicksbild men skapa inte ögonblicksbilder på filnivå resursen. Om du har utfört en ögonblicksbild av en resurs på resursen och du vill visa resursen ögonblicksbilder där en viss fil har ändrats, kan du dock göra det från ”tidigare versioner” upplevelse på en Windows-monterade resursen. Berätta om du har behov av en ögonblicksbild funktionen [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>**Kan jag skapa resursen ögonblicksbilder av krypterade filresursen?**  
    Du kan ta en ögonblicksbild av Azure-filresurser resurs som har kryptering i vila aktiverad. Du kan återställa filer från en ögonblicksbild av en resurs till en krypterad filresurs. Om resursen är krypterad krypteras också din dela ögonblicksbild.

* <a id="geo-redundant-snaphsots"></a>**Är Mina resursen ögonblicksbilder geo-redundant?**
    Dela ögonblicksbild har samma redundans som Azure filresursen de togs. Om du har valt geo-redundant lagring (GRS) för ditt konto lagras din resurs ögonblicksbild också redundant i parad region.

### <a name="manage-share-snapshots"></a>Hantera resursen ögonblicksbilder
* <a id="browse-snapshots-linux"></a>**Kan jag Bläddra min resursen ögonblicksbilder från Linux?**  
    Du kan använda Azure CLI 2.0 för att skapa, visa, bläddra och återställa på Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>**Kan jag kopiera ögonblicksbilderna resurs till ett annat lagringskonto?**  
    Du kan kopiera filer från resursen ögonblicksbilder till en annan plats men inte själva ögonblicksbilderna resursen.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från resursen ögonblicksbilder
* <a id="promote-share-snapshot"></a>**Kan jag uppgradera en ögonblicksbild av en resurs till grundläggande resursen?**  
    Du kan bara kopiera data från en ögonblicksbild av en resurs till alla önskade mål. Men du kan inte befordra en ögonblicksbild av en resurs till grundläggande resursen.

* <a id="restore-snapshotted-file-to-other-share"></a>**Kan jag återställa data från min dela ögonblicksbild till ett annat lagringskonto?**  
    Ja. Filer från en ögonblicksbild av en resurs kan kopieras till ursprungliga eller en annan plats som innehåller samma/annat lagringskonto i samma eller olika regioner. Du kan också kopiera filer till lokala eller andra molntjänster.    
  
### <a name="cleanup-share-snapshot"></a>Rensa dela ögonblicksbild
* <a id="delete-share-keep-snapshots"></a>**Kan jag ta bort min resursen, men inte dela ögonblicksbilder?**
    Du kommer inte att ta bort resursen om du har active resursen ögonblicksbilder på nätverksresursen. Det finns en API för att ta bort resursen ögonblicksbilder tillsammans med resursen och du kan uppnå samma från Azure portal samt.

* <a id="delete-share-with-snapshots"></a>**Vad händer med min resursen ögonblicksbilder om jag tar bort mitt Lagringskonto?**
    Om du tar bort storage-konto raderas resursen ögonblicksbilder samt.

## <a name="billing-and-pricing"></a>Fakturering och priser
* <a id="vm-file-share-network-traffic"></a>**Räknas nätverkstrafik mellan en Azure VM och en Azure-filresurs som extern bandbredd som debiteras genom prenumerationen?**  
    Om filresursen och den virtuella datorn finns i samma Azure-region, är trafiken mellan dem kostnadsfri. Om de finns i olika områden debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>**Hur mycket delar ögonblicksbilder kostnaden?**
     Under förhandsgranskning resursen kommer inte att debiteras ögonblicksbild kapacitet. Standard lagring utgång och transaktionen kostnader fönsterbräda gäller. Efter allmän tillgänglighet kommer både kapacitet och transaktioner på resursen ögonblicksbilden att debiteras.
     
     Dela ögonblicksbilder är inkrementell till sin natur. Grundläggande resursen ögonblicksbilden är resursen sig själv. Alla efterföljande resursen ögonblicksbilder är inkrementell och kommer bara att lagra diff från den tidigare ögonblicksbilden av resursen. Du debiteras endast för det ändrade innehållet. Om du har en resurs med 100 GiB av data, men endast 5 GiB har ändrats efter din senaste dela ögonblicksbild, dela ögonblicksbild tas förbrukar endast 5 ytterligare GiB och du kommer att fakturerade endast 105 GiB. Se [priser sidan](https://azure.microsoft.com/pricing/details/storage/files/) mer information om transaktionen och standard utgång kostnader.

## <a name="scale-and-performance"></a>Skalning och prestanda
* <a id="files-scale-limits"></a>**Vad är Azure Files skala gränser?**  
    Mer information om skalbarhets- och prestandamål för Azure-filer finns [skalbarhets- och prestandamål för Azure-filer](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>**En större filresurs än Azure-filer finns för närvarande... kan jag öka storleken på min Azure-filresurs måste?**  
    Nej, är den maximala storleken för en Azure-filresurs 5 TiB. Det här är en hård gräns som vi inte kan ändras. Vi arbetar på en lösning för att öka storleken på filresursen till 100 TiBs, men har inte tidslinjer att dela just nu.

* <a id="open-handles-quota"></a>**Hur många klienter har åtkomst till samma fil samtidigt?**  
    Det finns en 2000 öppna referenser kvot på en enskild fil. När du har 2000 öppna referenser du får ett fel som har nåtts.

* <a id="zip-slow-performance"></a>**Min prestanda var låga när försök att packa upp filer till Azure-filer. Vad ska jag göra?**  
    Om du vill överföra stort antal filer i Azure-filer, rekommenderar vi att du använder AzCopy (Windows, förhandsgranskning för Linux/Unix) eller Azure Powershell som dessa verktyg har optimerats för nätverksöverföring.

* <a id="slow-perf-windows-81-2012r2"></a>**Jag har monterats min Azure-filresurs på Windows Server 2012 R2 eller Windows 8.1 och min går långsamt - varför?**  
    Det finns ett känt problem när du monterar en Azure-filresurs på Windows Server 2012 R2 och Windows 8.1 som var korrigeras i April 2014 kumulativ uppdatering för Windows 8.1 och Windows Server 2012 R2. Kontrollera att alla instanser av Windows Server 2012 R2 och Windows 8.1 har denna uppdatering har tillämpats för optimal prestanda (självklart alltid rekommenderar vi alla Windows-korrigeringsfiler via Windows Update). Mer information finns i KB-artikeln, [låga prestanda när du har åtkomst till Azure-filer från Windows 8.1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och samverkan med andra tjänster
* <a id="cluster-witness"></a>**Kan jag använda min Azure-filresurs som en *filresursvittne* för min redundanskluster för Windows Server?**  
    Detta stöds inte för närvarande till en filresurs på Azure. Mer information om hur du anger detta för Azure Blob storage finns [distribuera ett moln vittne för ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>**Kan jag montera en Azure-filresurs på en Azure-behållare instans?** ?  
    Ja, Azure-filresurser är fantastiska alternativet att spara information utöver livslängden för en instans i behållaren. Mer information finns [montera en filresurs som Azure med Azure Container instanser](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**Finns det en åtgärd för namnbyten i REST API?**  
    Inte just nu.

* <a id="nested-shares"></a>**Kan du använda kapslade resurser, med andra ord en resurs under en annan resurs?**  
    Nej. Filresursen är den virtuella drivrutin som du kan montera, så kapslade resurser stöds inte.

* <a id="ibm-mq"></a>**Med hjälp av Azure filer med IBM MQ**  
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder när du konfigurerar Azure-filer med deras tjänst. Mer information finns i [Konfigurera IBM MQ MIQM (Multi Instance Queue Manager) med Microsoft Azures filtjänst](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se även
* [Felsökning av problem med Azure-filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsökning av problem med Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure filsynkronisering (förhandsgranskning)](storage-sync-files-troubleshoot.md)