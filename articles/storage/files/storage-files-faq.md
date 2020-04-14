---
title: Vanliga frågor och svar (Vanliga frågor) för Azure-filer | Microsoft-dokument
description: Hitta svar på vanliga frågor om Azure-filer.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 9398820c88120400d97fbaf8cb0da100c2bdbf81
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261762"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Vanliga frågor och svar om Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via [SMB-protokollet (Industry-Standard Server Message Block).](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) Du kan montera Azure-filresurser samtidigt i molnet eller lokala distributioner av Windows, Linux och macOS. Du kan också cachelagra Azure-filresurser på Windows Server-datorer med hjälp av Azure File Sync för snabb åtkomst nära där data används.

Den här artikeln besvarar vanliga frågor om Azure Files-funktioner, inklusive användning av Azure File Sync med Azure Files. Om du inte ser svaret på din fråga kan du kontakta oss via följande kanaler (i eskalerande ordning):

1. Kommentarerna i den här artikeln.
2. [Azure Storage Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure-filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft-support. Om du vill skapa en ny supportbegäran väljer du hjälp **+ supportknappen** på fliken Azure på **fliken Hjälp** + och väljer sedan Ny **supportbegäran**.

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>
  **Hur är Azure-filer användbara?**  
   Du kan använda Azure Files för att skapa filresurser i molnet, utan att vara ansvarig för att hantera omkostnaderna för en fysisk server, enhet eller en installation. Vi gör det monotona arbetet för dig, inklusive att tillämpa OS-uppdateringar och ersätta dåliga diskar. Mer information om de scenarier som Azure Files kan hjälpa dig med finns i [Varför Azure-filer är användbara](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Vilka olika sätt kan du komma åt filer i Azure-filer?**  
    Du kan montera filresursen på den lokala datorn med hjälp av SMB 3.0-protokollet eller använda verktyg som [Storage Explorer](https://storageexplorer.com/) för att komma åt filer i filresursen. Från ditt program kan du använda lagringsklientbibliotek, REST-API:er, PowerShell eller Azure CLI för att komma åt dina filer i Azure-filresursen.

* <a id="what-is-afs"></a>
  **Vad är Azure File Sync?**  
    Du kan använda Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver bevaras. Azure File Sync omvandlar dina Windows Server-datorer till en snabb cache av din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive NFS (Network File System) och FTPS (File Transfer Protocol Service). Du kan ha så många cachar som du behöver över hela världen.

* <a id="files-versus-blobs"></a>
  **Varför skulle jag använda en Azure-filresurs jämfört med Azure Blob-lagring för mina data?**  
    Azure-filer och Azure Blob-lagring erbjuder båda sätt att lagra stora mängder data i molnet, men de är användbara för lite olika ändamål. 
    
    Azure Blob-lagring är användbar för storskaliga, molnbaserade program som behöver lagra ostrukturerade data. För att maximera prestanda och skala är Azure Blob-lagring en enklare lagringsabstraktion än ett sant filsystem. Du kan bara komma åt Azure Blob-lagring via REST-baserade klientbibliotek (eller direkt via REST-baserade protokollet).

    Azure Files är specifikt ett filsystem. Azure Files har alla filabstrakt som du känner till och älskar från år av arbete med lokala operativsystem. Precis som Azure Blob-lagring erbjuder Azure Files ett REST-gränssnitt och REST-baserade klientbibliotek. Till skillnad från Azure Blob-lagring erbjuder Azure Files SMB-åtkomst till Azure-filresurser. Genom att använda SMB kan du montera en Azure-filresurs direkt på Windows, Linux eller macOS, antingen lokalt eller i virtuella datorer i molnet, utan att skriva någon kod eller bifoga några speciella drivrutiner till filsystemet. Du kan också cachelagra Azure-filresurser på lokala filservrar med hjälp av Azure File Sync för snabb åtkomst, nära där data används. 
   
    Mer detaljerad beskrivning av skillnaderna mellan Azure-filer och Azure Blob-lagring finns i [Introduktion till de grundläggande Azure Storage-tjänsterna](../common/storage-introduction.md). Mer information om Azure Blob-lagring finns i [Introduktion till Blob-lagring](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Varför skulle jag använda en Azure-filresurs i stället för Azure Disks?**  
    En disk i Azure Disks är helt enkelt en disk. För att få värde från Azure Disks måste du bifoga en disk till en virtuell dator som körs i Azure. Azure Disks kan användas för allt som du använder en disk för på en lokal server. Du kan använda den som en OS-systemdisk, som växlingsutrymme för ett operativsystem eller som dedikerad lagring för ett program. En intressant användning för Azure Disks är att skapa en filserver i molnet för att använda på samma platser där du kan använda en Azure-filresurs. Att distribuera en filserver i Virtuella Azure-datorer är ett högpresterande sätt att få fillagring i Azure när du behöver distributionsalternativ som för närvarande inte stöds av Azure-filer (till exempel NFS-protokollstöd eller premiumlagring). 

    Att köra en filserver med Azure Disks som backend-lagring är dock vanligtvis mycket dyrare än att använda en Azure-filresurs, av några skäl. Först, förutom att betala för disklagring, måste du också betala för kostnaden för att köra en eller flera Virtuella Azure-datorer. För det andra måste du också hantera de virtuella datorer som används för att köra filservern. Du ansvarar till exempel för OS-uppgraderingar. Slutligen, om du i slutändan kräver att data cachelagras lokalt, är det upp till dig att konfigurera och hantera replikeringstekniker, till exempel DFSR (Distributed File System Replication), för att göra det möjligt.

    En metod för att få ut det bästa av både Azure-filer och en filserver som finns i Virtuella Azure-datorer (förutom att använda Azure Diskar som serverdelslagring) är att installera Azure File Sync på en filserver som finns på en virtuell moln.One approach to getting the best of both Azure Files and a file server that's hosted in Azure Virtual Machines (förutom att använda Azure Disks as back-end storage) är att installera Azure File Sync på en filserver som finns på en virtuell moln.One approach to getting the best of both Azure Files and a file server that's hosted in Azure Virtual Machines (förutom att använda Azure Disks as back-end storage) är att installera Azure File Sync på en filserver som finns på en virtuell moln.One approach to getting the best of both Azure Files and a file Om Azure-filresursen finns i samma region som filservern kan du aktivera molnnivådelning och ange volymen för ledigt utrymme till maximalt (99 %). Detta säkerställer minimal dubblering av data. Du kan också använda alla program du vill med filservrarna, till exempel program som kräver NFS-protokollstöd.

    Information om ett alternativ för att konfigurera en högpresterande och högtillgänglig filserver i Azure finns [i Distribuera gästkluster för IaaS VM i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer detaljerad beskrivning av skillnaderna mellan Azure-filer och Azure-diskar finns i [Introduktion till de grundläggande Azure Storage-tjänsterna](../common/storage-introduction.md). Mer information om Azure-diskar finns i [översikt över hanterade hårddiskar i Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Hur kommer jag igång med Azure Files?**  
   Det är enkelt att komma igång med Azure Files. Skapa först [en filresurs](storage-how-to-create-file-share.md)och sedan montera den i det önskade operativsystemet: 

  * [Montera i Windows](storage-how-to-use-files-windows.md)
  * [Montera i Linux](storage-how-to-use-files-linux.md)
  * [Montera i macOS](storage-how-to-use-files-mac.md)

    En mer djupgående guide om hur du distribuerar en Azure-filresurs för att ersätta produktionsfilresurser i organisationen finns i [Planera för en Azure Files-distribution](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Vilka lagringsredundansalternativ stöds av Azure Files?**  
    För närvarande stöder Azure Files lokalt redundant lagring (LRS), zon redundant lagring (ZRS), geo-redundant lagring (GRS) och geo-zon-redundant lagring (GZRS) (förhandsversion). Vi planerar att stödja geo redundant lagring (RA-GRS) i framtiden, men vi har inga tidslinjer att dela just nu.

* <a id="tier-options"></a>
  **Vilka lagringsnivåer stöds i Azure Files?**  
    Azure Files stöder två lagringsnivåer: premium och standard. Standardfilresurser skapas i allmänna syften (GPv1- eller GPv2) lagringskonton och premiumfilresurser skapas i FileStorage-lagringskonton. Läs mer om hur du skapar [standardfilresurser](storage-how-to-create-file-share.md) och [premiumfilresurser](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Du kan inte skapa Azure-filresurser *premium* från Blob storage-konton eller premium-lagringskonton (GPv1 eller GPv2). Standard Azure-filresurser måste skapas endast i *standardkonton* för allmänt ändamål och premium Azure-filresurser måste bara skapas i FileStorage-lagringskonton. *Lagringskonton* för premium allmänt ändamål (GPv1 och GPv2) är endast för premium-sidblobar. 

* <a id="give-us-feedback"></a>
  **Jag vill verkligen se en specifik funktion som läggs till i Azure-filer. Kan du lägga till den?**  
    Azure Files-teamet är intresserade av att höra all feedback du har om vår tjänst. Rösta på funktionsförfrågningar på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi ser fram emot att glädja dig med många nya funktioner.

  **Stöder Azure Files fillåsning?**  
    Ja, Azure Files har fullt stöd för fillåsning i SMB/Windows-stil, [se information](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks). 
    
## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Vilka regioner stöds för Azure File Sync?**  
    Listan över tillgängliga regioner finns i avsnittet [Regiontillgänglighet](storage-sync-files-planning.md#azure-file-sync-region-availability) i planeringsguiden för Azure File Sync. Vi kommer kontinuerligt att lägga till stöd för ytterligare regioner, inklusive icke-offentliga regioner.

* <a id="cross-domain-sync"></a>
  **Kan jag ha domänanslutna och icke-domänanslutna servrar i samma synkroniseringsgrupp?**  
    Ja. En synkroniseringsgrupp kan innehålla serverslutpunkter som har olika Active Directory-medlemskap, även om de inte är domänanslutna. Även om den här konfigurationen fungerar tekniskt rekommenderar vi inte detta som en typisk konfiguration eftersom åtkomstkontrollistor (ACL: er) som har definierats för filer och mappar på en server kanske inte kan tillämpas av andra servrar i synkroniseringsgruppen. För bästa resultat rekommenderar vi synkronisering mellan servrar som finns i samma Active Directory-skog, mellan servrar som finns i olika Active Directory-skogar men som har upprättat förtroenderelationer eller mellan servrar som inte finns i en domän. Vi rekommenderar att du undviker att använda en blandning av dessa konfigurationer.

* <a id="afs-change-detection"></a>
  **Jag skapade en fil direkt i min Azure-filresurs med hjälp av SMB eller i portalen. Hur lång tid tar det för filen att synkroniseras med servrarna i synkroniseringsgruppen?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Om samma fil ändras på två servrar vid ungefär samma tidpunkt, vad händer då?**  
    Azure File Sync använder en enkel konfliktlösningsstrategi: vi behåller båda ändringarna i filer som ändras på två servrar samtidigt. Den senast skrivna ändringen behåller det ursprungliga filnamnet. Den äldre filen har "source"-datorn och konfliktnumret läggs till namnet. Det följer denna taxonomi: 
   
    \<\>-\<FilnamnUtanutextension\>\[-#\]MachineName . \<ext (\>  

    Den första konflikten i CompanyReport.docx blir till exempel CompanyReport-CentralServer.docx om CentralServer är där den äldre skrivningen inträffade. Den andra konflikten skulle heta CompanyReport-CentralServer-1.docx. Azure File Sync stöder 100 konfliktfiler per fil. När det maximala antalet konfliktfiler har uppnåtts synkroniseras filen inte förrän antalet konfliktfiler är mindre än 100.

* <a id="afs-storage-redundancy"></a>
  **Stöds geo redundant lagring för Azure File Sync?**  
    Ja, Azure Files stöder både lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). Om du initierar en redundans för lagringskonto mellan parkopplade regioner från ett konto som konfigurerats för GRS rekommenderar Microsoft att du endast behandlar den nya regionen som en säkerhetskopia av data. Azure File Sync börjar inte automatiskt synkronisera med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>
  **Varför matchar inte egenskapen *Storlek på disk* för en fil egenskapen *Size* när du har använt Azure File Sync?**  
  Se [Förstå molnnivådelning](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Hur vet jag om en fil har nivåats upp?**  
  Se [Förstå molnnivådelning](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**En fil som jag vill använda har nivåindelats. Hur kan jag återkalla filen till disken för att använda den lokalt?**  
  Se [Förstå molnnivådelning](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Hur tvingar jag en fil eller katalog att nivåindelad?**  
  Se [Förstå molnnivådelning](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Hur tolkas *volymfritt utrymme* när jag har flera serverslutpunkter på en volym?**  
  Se [Förstå molnnivådelning](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **Vilka filer eller mappar utesluts automatiskt av Azure File Sync?**  
  Se [Filer som hoppas över](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Kan jag använda Azure File Sync med antingen Windows Server 2008 R2, Linux eller min NAS-enhet (Network-attached storage) ?**  
    För närvarande stöder Azure File Sync endast Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2. Just nu har vi inga andra planer som vi kan dela, men vi är öppna för att stödja ytterligare plattformar baserat på kundernas efterfrågan. Meddela oss på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) vilka plattformar du vill att vi ska stödja.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Varför finns nivåindelad filer utanför serverslutpunktsnamnområdet?**  
    Före Azure File Sync agent version 3 blockerade Azure File Sync flytten av nivåindelade filer utanför serverslutpunkten men på samma volym som serverslutpunkten. Kopieringsåtgärder, flyttningar av filer som inte är nivåer och flyttningar av nivåindelad till andra volymer påverkades inte. Anledningen till detta var det implicita antagandet att Utforskaren och andra Windows API:er har som flyttar åtgärder på samma volym är (nästan) momentana byt namnåtgärder. Det innebär att flyttar gör att Utforskaren eller andra flyttmetoder (till exempel kommandoraden eller PowerShell) inte svarar medan Azure File Sync återkallar data från molnet. Från och med [Azure File Sync agent version 3.0.12.0](storage-files-release-notes.md#supported-versions)kan du flytta en nivåindelad fil utanför serverslutpunkten. Vi undviker de negativa effekter som tidigare nämnts genom att låta den nivåindelade filen finnas som en nivåindelad fil utanför serverslutpunkten och sedan återkalla filen i bakgrunden. Detta innebär att rör sig på samma volym är ögonblickliga, och vi gör allt arbete för att återkalla filen till disken när flytten har slutförts. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Jag har problem med Azure File Sync på min server (synkronisering, molnnivådelning osv.). Ska jag ta bort och återskapa serverslutpunkten?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kan jag flytta lagringssynkroniseringstjänsten och/eller lagringskontot till en annan resursgrupp eller prenumeration?**  
   Ja, lagringssynkroniseringstjänsten och/eller lagringskontot kan flyttas till en annan resursgrupp eller prenumeration inom den befintliga Azure AD-klienten. Om lagringskontot flyttas måste du ge Hybrid File Sync Service åtkomst till lagringskontot (se [Se till att Azure File Sync har åtkomst till lagringskontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync stöder inte att flytta prenumerationen till en annan Azure AD-klientorganisation.
    
* <a id="afs-ntfs-acls"></a>
  **Bevarar Azure File Sync NTFS-ACL:er på katalog-/filnivå tillsammans med data som lagras i Azure Files?**

    Från och med den 24 februari 2020 sparas nya och befintliga ACL-enheter som nivåindelats av Azure-filsynkronisering i NTFS-format och ACL-ändringar som görs direkt till Azure-filresursresursen synkroniseras med alla servrar i synkroniseringsgruppen. Alla ändringar på ACL:er som görs i Azure-filer synkroniseras ned via Azure-filsynkronisering. När du kopierar data till Azure-filer ska du se till att du använder SMB för att komma åt resursen och bevara dina ACL:er. Befintliga REST-baserade verktyg, till exempel AzCopy eller Storage Explorer, finns inte kvar i ACL:er.

    Om du har aktiverat Azure Backup på filsynkronisering hanterade filresurser, kan fil-ÅTKOMSTkontrollistorna fortsätta att återställas som en del av arbetsflödet för säkerhetskopieringsåterställning. Detta fungerar antingen för hela resursen eller enskilda filer / kataloger.

    Om du använder ögonblicksbilder som en del av den självhanterade säkerhetskopieringslösningen för filresurser som hanteras av filsynkronisering, kanske dina ACL:er inte återställs korrekt till NTFS-ACL:er om ögonblicksbilderna togs före den 24 februari 2020. Om detta inträffar bör du överväga att kontakta Azure Support.
    
## <a name="security-authentication-and-access-control"></a>Säkerhets-, autentiserings- och åtkomstkontroll
* <a id="ad-support"></a>
**Stöds identitetsbaserad autentisering och åtkomstkontroll av Azure Files?**  
    
    Ja, Azure Files stöder identitetsbaserad autentisering och åtkomstkontroll. Du kan välja ett av två sätt att använda identitetsbaserad åtkomstkontroll: Active Directory (AD) (förhandsversion) eller Azure Active Directory Domain Services (Azure AD DS) (GA). AD stöder autentisering med AD-domänsassteraddatorer, antingen lokalt eller i Azure, för att komma åt Azure-filresurser via SMB. Azure AD DS-autentisering över SMB för Azure-filer gör det möjligt för Azure AD DS-domänen anslöt till virtuella datorer i Windows att komma åt resurser, kataloger och filer med Azure AD-autentiseringsuppgifter. Mer information finns i [Översikt över Azure Files identitetsbaserade autentiseringsstöd för SMB-åtkomst](storage-files-active-directory-overview.md). 

    Azure Files erbjuder ytterligare två sätt att hantera åtkomstkontroll:

    - Du kan använda SAS (Shared Access Signatures) för att generera token som har specifika behörigheter och som är giltiga för ett angivet tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en viss fil som har en 10-minuters förfallode. Alla som har token medan token är giltig har skrivskyddad åtkomst till filen i dessa 10 minuter. Signaturnycklar för delad åtkomst stöds endast via REST API eller i klientbibliotek. Du måste montera Azure-filresursen över SMB med hjälp av lagringskontonycklarna.

    - Azure File Sync bevarar och replikerar alla diskretionära ÅTKOMSTKONTROLLLS, eller DACLs, (oavsett om Det är Active Directory-baserat eller lokalt) till alla serverslutpunkter som synkroniseras till. Eftersom Windows Server redan kan autentisera med Active Directory är Azure File Sync ett effektivt stoppgap tills fullt stöd för Active Directory-baserad autentisering och ACL-stöd anländer.
    
    Du kan referera till [Auktorisera åtkomst till Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för en omfattande representation av alla protokoll som stöds på Azure Storage-tjänster. 

* <a id="ad-support-devices"></a>
**Stöder Azure Files Azure Active Directory Domain Services (Azure AD DS) Autentisering SMB-åtkomst med Azure AD-autentiseringsuppgifter från enheter som är anslutna till eller registrerade med Azure AD?**

    Nej, det här scenariot stöds inte.

* <a id="ad-support-rest-apis"></a>
**Finns det REST API:er som stöder API:er för att hämta/ange/kopiera katalog/fil-ACL:er?**

    Ja, vi stöder REST-API:er som hämtar, anger eller kopierar NTFS-ACL:er för kataloger eller filer när du använder [REST-API:et för 2019-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (eller senare).

* <a id="ad-vm-subscription"></a>
**Kan jag komma åt Azure-filer med Azure AD-autentiseringsuppgifter från en virtuell dator under en annan prenumeration?**

    Om prenumerationen som filresursen distribueras under är associerad med samma Azure AD-klient som Azure AD Domain Services-distributionen som den virtuella datorn är domänansluten till, kan du sedan komma åt Azure-filer med samma Azure AD-autentiseringsuppgifter. Begränsningen åläggs inte för prenumerationen utan på den associerade Azure AD-klienten.
    
* <a id="ad-support-subscription"></a>
**Kan jag aktivera antingen Azure Files Azure AD DS eller AD-autentisering med en Azure AD-klient som skiljer sig från den primära klienten som filresursen är associerad med?**

    Nej, Azure-filer stöder endast Azure AD DS- eller AD-integrering med en Azure AD-klient som finns i samma prenumeration som filresursen. Endast en prenumeration kan associeras med en Azure AD-klientorganisation. Den här begränsningen gäller både Azure AD DS- och AD-autentiseringsmetoder. När AD-autentiseringen ska användas för autentisering måste AD-autentiseringsuppgifterna synkroniseras med Azure AD som lagringskontot är associerat med.

* <a id="ad-linux-vms"></a>
**Stöder Azure Files Azure AD DS- eller AD-autentisering virtuella linux-datorer?**

    Nej, autentisering från virtuella Linux-datorer stöds inte.

* <a id="ad-multiple-forest"></a>
**Stöder Azure Files AD-autentisering integrering med en AD-miljö med flera skogar?**    

    Azure Files AD-autentisering integreras endast med skogen för AD-domäntjänsten som lagringskontot är registrerat till. För att stödja autentisering från en annan AD-skog måste din miljö ha skogsförtroende konfigurerat korrekt. Hur Azure Files registrerar sig till en AD-domäntjänst är oftast detsamma som en vanlig filserver, där den skapar ett identitets-(dator- eller tjänstinloggningskonto) i AD för autentisering. Den enda skillnaden är att det registrerade SPN för lagringskontot slutar med "file.core.windows.net" som inte matchar med domänsuffixet. Kontakta domänadministratören för att se om någon uppdatering av DNS-routningsprincipen krävs för att aktivera flera skogsautentiseringar på grund av de olika domänsuffixet.

* <a id=""></a>
**Vilka regioner är tillgängliga för Azure Files AD-autentisering (förhandsversion)?**

    Mer information finns i [AD:s regionala tillgänglighet.](storage-files-identity-auth-active-directory-enable.md#regional-availability)

* <a id="ad-aad-smb-afs"></a>
**Kan jag utnyttja Azure Files Azure AD DS-autentisering eller förhandsversion av Active Directory (AD) på filresurser som hanteras av Azure File Sync?**

    Ja, du kan aktivera Azure AD DS- eller AD-autentisering på en filresurs som hanteras av Azure-filsynkronisering. Ändringar i katalogen/filenS-ACL:er för lokala filservrar kommer att nivåindelad till Azure-filer och vice versa.

* <a id="ad-aad-smb-files"></a>
**Hur kan jag kontrollera om jag har aktiverat AD-autentisering på mitt lagringskonto och AD-domäninformation?**

    Du kan läsa instruktionerna [här](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account) för att verifiera om AZURE Files AD-autentisering är aktiverad på ditt lagringskonto och hämta AD-domäninformationen.
    
* <a id="encryption-at-rest"></a>
**Hur kan jag se till att min Azure-filresurs krypteras i vila?**  

    Ja. Mer information finns i [Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hur kan jag ge åtkomst till en viss fil med hjälp av en webbläsare?**  

    Du kan använda signaturer för delad åtkomst för att generera token som har specifika behörigheter och som är giltiga för ett angivet tidsintervall. Du kan till exempel generera en token som ger skrivskyddad åtkomst till en viss fil under en viss tidsperiod. Alla som har webbadressen kan komma åt filen direkt från vilken webbläsare som helst medan token är giltig. Du kan enkelt generera en signaturnyckel för delad åtkomst från ett användargränssnitt som Storage Explorer.

* <a id="file-level-permissions"></a>
**Är det möjligt att ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen?**  

    Om du monterar filresursen med SMB har du inte kontroll över behörigheter på mappnivå. Om du skapar en signatur för delad åtkomst med rest-API:et eller klientbiblioteken kan du ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen.

* <a id="ip-restrictions"></a>
**Kan jag implementera IP-begränsningar för en Azure-filresurs?**  

    Ja. Åtkomsten till din Azure-filresurs kan begränsas på lagringskontonivå. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Vilka principer för dataefterlevnad stöder Azure Files?**  

   Azure-filer körs ovanpå samma lagringsarkitektur som används i andra lagringstjänster i Azure Storage. Azure-filer tillämpar samma dataefterlevnadsprinciper som används i andra Azure-lagringstjänster. Mer information om Azure Storage-dataefterlevnad finns i [Azure Storage compliance offer och](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)gå till Microsoft Trust [Center](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Lokal åtkomst

* <a id="port-445-blocked"></a>
**Min Internet-leverantören eller IT blockerar Port 445 som inte azure-filer monteras. Vad ska jag göra?**

    Du kan läsa om [olika sätt att lösa blockerad port 445 här](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Azure-filer tillåter endast anslutningar med SMB 3.0 (med krypteringsstöd) utanför regionen eller datacentret. SMB 3.0 protokoll har introducerat många trygghet dragen inklusive kanal krypteringen vilken är mycket befästa till använda över internet. Men det är möjligt att port 445 har blockerats på grund av historiska orsaker till sårbarheter som finns i lägre SMB-versioner. I ideala fall bör porten blockeras endast för SMB 1.0-trafik och SMB 1.0 ska stängas av på alla klienter.

* <a id="expressroute-not-required"></a>
**Måste jag använda Azure ExpressRoute för att ansluta till Azure-filer eller för att använda Azure File Sync lokalt?**  

    Nej. ExpressRoute krävs inte för att komma åt en Azure-filresurs. Om du monterar en Azure-filresurs direkt lokalt är allt som krävs för att port 445 (TCP-utgående) ska vara öppen för internetåtkomst (det här är porten som SMB använder för att kommunicera). Om du använder Azure File Sync, allt som krävs är port 443 (TCP utgående) för HTTPS-åtkomst (ingen SMB krävs). Du *kan* dock använda ExpressRoute med något av dessa åtkomstalternativ.

* <a id="mount-locally"></a>
**Hur monterar jag en Azure-filresurs på min lokala dator?**  

    Du kan montera filresursen med SMB-protokollet om port 445 (TCP-utgående) är öppen och klienten stöder SMB 3.0-protokollet (till exempel om du använder Windows 10 eller Windows Server 2016). Om port 445 blockeras av organisationens princip eller av Internet-leverantören kan du använda Azure File Sync för att komma åt din Azure-filresurs.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hur säkerhetskopierar jag min Azure-filresurs?**  
    Du kan använda periodiska [ögonblicksbilder av resurs](storage-snapshots-files.md) för skydd mot oavsiktliga borttagningar. Du kan också använda AzCopy, Robocopy eller ett säkerhetskopieringsverktyg från tredje part som kan säkerhetskopiera en monterad filresurs. Azure Backup erbjuder säkerhetskopiering av Azure-filer. Läs mer om [säkerhetskopiering av Azure-filresurser från Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Resursögonblicksbilder

### <a name="share-snapshots-general"></a>Dela ögonblicksbilder: Allmänt
* <a id="what-are-snaphots"></a>
**Vad är ögonblicksbilder av filresurs?**  
    Du kan använda ögonblicksbilder av Azure-filresurs för att skapa en skrivskyddad version av filresurserna. Du kan också använda Azure Files för att kopiera en tidigare version av ditt innehåll tillbaka till samma resurs, till en alternativ plats i Azure eller lokalt för fler ändringar. Mer information om ögonblicksbilder av resurs finns i [översikten Dela ögonblicksbild](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Var lagras mina ögonblicksbilder av resursen?**  
    Ögonblicksbilder av resurser lagras i samma lagringskonto som filresursen.

* <a id="snapshot-consistency"></a>
**Är ögonblicksbilder av resursögonblicksbilder konsekventa?**  
    Nej, ögonblicksbilder av resurs är inte programkonsekventa. Användaren måste tömma skrivningar från programmet till resursen innan du tar ögonblicksbilden av resursen.

* <a id="snapshot-limits"></a>
**Finns det gränser för hur många ögonblicksbilder av delar jag kan använda?**  
    Ja. Azure-filer kan behålla högst 200 ögonblicksbilder av resurs. Ögonblicksbilder av resurs räknas inte in i resurskvoten, så det finns ingen gräns per aktie för det totala utrymmet som används av alla ögonblicksbilder av resursen. Begränsningar för lagringskonto gäller fortfarande. Efter 200 ögonblicksbilder av resurs måste du ta bort äldre ögonblicksbilder för att skapa nya ögonblicksbilder av resursen.

* <a id="snapshot-cost"></a>
**Hur mycket kostar ögonblicksbilder av resurs?**  
    Standardtransaktions- och standardlagringskostnaden gäller för ögonblicksbilder. Ögonblicksbilder är inkrementella till sin natur. Basögonblicksbilden är själva resursen. Alla efterföljande ögonblicksbilder är inkrementella och lagrar bara diff från föregående ögonblicksbild. Det innebär att deltaändringarna som visas i fakturan blir minimala om din arbetsbelastningsomsättning är minimal. Se [prissidan](https://azure.microsoft.com/pricing/details/storage/files/) för standardprisinformation för Azure Files. Idag är sättet att se på storlek som förbrukas av ögonblicksbild av resursen genom att jämföra den fakturerade kapaciteten med använd kapacitet. Vi arbetar med verktyg för att förbättra rapporteringen.

* <a id="ntfs-acls-snaphsots"></a>
**Finns NTFS-ACL:er på kataloger och filer som sparats i ögonblicksbilder av resurs?**  
    NTFS-ACL:er på kataloger och filer sparas i ögonblicksbilder av delning.

### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder
* <a id="file-snaphsots"></a>
**Kan jag skapa ögonblicksbild av enskilda filer?**  
    Ögonblicksbilder av resurs skapas på filresursnivå. Du kan återställa enskilda filer från ögonblicksbilden av filresursen, men du kan inte skapa ögonblicksbilder av filnivådelning. Om du har tagit en ögonblicksbild av resursnivå och vill lista ögonblicksbilder av resurs där en viss fil har ändrats, kan du göra detta under **Tidigare versioner** på en Windows-monterad resurs. 
    
    Kontakta oss på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)om du behöver en funktion för ögonblicksbilder av filer.

* <a id="encrypted-snapshots"></a>
**Kan jag skapa ögonblicksbilder av en krypterad filresurs?**  
    Du kan ta en ögonblicksbild av Azure-filresurser som har kryptering i vila aktiverat. Du kan återställa filer från en ögonblicksbild av resurs till en krypterad filresurs. Om din resurs är krypterad krypteras även ögonblicksbilden av resursen.

* <a id="geo-redundant-snaphsots"></a>
**Är mina ögonblicksbilder geo-redundanta för min resurs?**  
    Ögonblicksbilder av resurs har samma redundans som azure-filresursen som de togs för. Om du har valt geoundundant lagring för ditt konto lagras även din ögonblicksbild av resursen redundant i den parade regionen.

### <a name="manage-share-snapshots"></a>Hantera ögonblicksbilder av resurs
* <a id="browse-snapshots-linux"></a>
**Kan jag bläddra bland mina ögonblicksbilder av delar från Linux?**  
    Du kan använda Azure CLI för att skapa, lista, bläddra bland och återställa ögonblicksbilder av resurs i Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan jag kopiera ögonblicksbilder av resursen till ett annat lagringskonto?**  
    Du kan kopiera filer från ögonblicksbilder av delar till en annan plats, men du kan inte själva kopiera ögonblicksbilderna av resursen.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från ögonblicksbilder av resurs
* <a id="promote-share-snapshot"></a>
**Kan jag marknadsföra en ögonblicksbild av resursen till basresursen?**  
    Du kan kopiera data från en ögonblicksbild av resursen till alla andra mål. Du kan inte befordra en ögonblicksbild av resursen till basresursen.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan jag återställa data från ögonblicksbilden av min resurs till ett annat lagringskonto?**  
    Ja. Filer från en ögonblicksbild av resursen kan kopieras till den ursprungliga platsen eller till en annan plats som innehåller antingen samma lagringskonto eller ett annat lagringskonto, antingen i samma region eller i olika regioner. Du kan också kopiera filer till en lokal plats eller till något annat moln.    
  
### <a name="clean-up-share-snapshots"></a>Rensa ögonblicksbilder av resurs
* <a id="delete-share-keep-snapshots"></a>
**Kan jag ta bort min resurs men inte ta bort mina ögonblicksbilder av resursen?**  
    Om du har aktiva ögonblicksbilder av resursen på din resurs kan du inte ta bort resursen. Du kan använda ett API för att ta bort ögonblicksbilder av resurs, tillsammans med resursen. Du kan också ta bort både ögonblicksbilder av resursen och resursen i Azure-portalen.

* <a id="delete-share-with-snapshots"></a>
**Vad händer med mina ögonblicksbilder av resursen om jag tar bort mitt lagringskonto?**  
    Om du tar bort ditt lagringskonto tas även ögonblicksbilder av resursen bort.

## <a name="billing-and-pricing"></a>Fakturering och prissättning
* <a id="vm-file-share-network-traffic"></a>
**Räknas nätverkstrafiken mellan en Virtuell Azure-dator och en Azure-filresurs som extern bandbredd som debiteras prenumerationen?**  
    Om filresursen och den virtuella datorn finns i samma Azure-region, finns det ingen extra kostnad för trafiken mellan filresursen och den virtuella datorn. Om filresursen och den virtuella datorn finns i olika regioner debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>
**Hur mycket kostar ögonblicksbilder av resurs?**  
     Under förhandsgranskningen debiteras ingen kostnad för resursögonblicksbildskapacitet. Standardlagringsutgång och transaktionskostnader gäller. Efter allmän tillgänglighet debiteras prenumerationer för kapacitet och transaktioner på ögonblicksbilder av resurs.
     
     Ögonblicksbilder av resurs är inkrementella till sin natur. Ögonblicksbilden av basresursen är själva resursen. Alla efterföljande ögonblicksbilder av resursen är inkrementella och lagrar endast skillnaden från den föregående ögonblicksbilden av resursen. Du faktureras bara för det ändrade innehållet. Om du har en resurs med 100 GiB data men bara 5 GiB har ändrats sedan din senaste ögonblicksbild av resursen, förbrukar resursögonblicksbilden bara 5 ytterligare GiB och du faktureras för 105 GiB. Mer information om transaktions- och standardrutgångsavgifter finns på [sidan Prissättning](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skala och prestanda
* <a id="files-scale-limits"></a>
**Vilka är skalbegränsningarna för Azure-filer?**  
    Information om skalbarhet och prestandamål för Azure-filer finns i [Azure Files skalbarhet och prestandamål](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Vilka storlekar är tillgängliga för Azure-filresurser?**  
    Azure-filresursstorlekar (premium och standard) kan skala upp till 100 TiB. Se avsnittet [Inbyggda till större filresurser (standardnivå)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) i planeringsguiden för introduktionsinstruktioner till de större filresurserna för standardnivån.

* <a id="lfs-performance-impact"></a>
**Påverkar expanderande kvot för fildelning mina arbetsbelastningar eller Azure File Sync?**
    
    Nej. Om du utökar kvoten påverkas inte dina arbetsbelastningar eller Azure File Sync.

* <a id="open-handles-quota"></a>
**Hur många klienter kan komma åt samma fil samtidigt?**   
    Det finns en kvot på 2 000 öppna handtag på en enda fil. När du har 2 000 öppna handtag visas ett felmeddelande som säger att kvoten har uppnåtts.

* <a id="zip-slow-performance"></a>
**Min prestanda är långsam när jag packar upp filer i Azure Files. Vad ska jag göra?**  
    Om du vill överföra ett stort antal filer till Azure-filer rekommenderar vi att du använder AzCopy (för Windows, i förhandsversion för Linux och UNIX) eller Azure PowerShell. Dessa verktyg har optimerats för nätverksöverföring.

* <a id="slow-perf-windows-81-2012r2"></a>
**Varför går det långsamt när jag har monterat min Azure-filresurs på Windows Server 2012 R2 eller Windows 8.1?**  
    Det finns ett känt problem när du monterar en Azure-filresurs på Windows Server 2012 R2 och Windows 8.1. Problemet korrigerades i den kumulativa uppdateringen i april 2014 för Windows 8.1 och Windows Server 2012 R2. För bästa prestanda, se till att alla instanser av Windows Server 2012 R2 och Windows 8.1 har den här korrigeringsfilen tillämpad. (Du bör alltid få Windows-korrigeringar via Windows Update.) Mer information finns i den associerade Microsoft Knowledge Base-artikeln [Långsam prestanda när du öppnar Azure Files från Windows 8.1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och interoperabilitet med andra tjänster
* <a id="cluster-witness"></a>
**Kan jag använda min Azure-filresurs som *filresursvittne* för mitt Windows Server Redundanskluster?**  
    För närvarande stöds inte den här konfigurationen för en Azure-filresurs. Mer information om hur du konfigurerar detta för Azure Blob-lagring finns i [Distribuera ett molnvittne för ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Kan jag montera en Azure-filresurs på en Azure Container-instans?**  
    Ja, Azure-filresurser är ett bra alternativ när du vill spara information utöver livstiden för en behållarinstans. Mer information finns i [Montera en Azure-filresurs med Azure Container-instanser](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Finns det en namnbytesåtgärd i REST API?**  
    Inte just nu.

* <a id="nested-shares"></a>
**Kan jag ställa in kapslade resurser? Med andra ord, en aktie under en aktie?**  
    Nej. Filresursen *är* den virtuella drivrutin som du kan montera, så kapslade resurser stöds inte.

* <a id="ibm-mq"></a>
**Hur använder jag Azure Files med IBM MQ?**  
    IBM har släppt ett dokument som hjälper IBM MQ-kunder att konfigurera Azure Files med IBM-tjänsten. Mer information finns i [Så här konfigurerar du en IBM MQ-köhanterare med flera instanser med Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se även
* [Felsöka Azure-filer i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure-filer i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsök Azure File Sync](storage-sync-files-troubleshoot.md)
