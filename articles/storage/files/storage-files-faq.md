---
title: Vanliga frågor och svar (FAQ) för Azure Files | Microsoft Docs
description: Få svar på vanliga frågor om Azure Files.
author: roygara
ms.service: storage
ms.date: 07/30/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: e5b1880a12cda440a5772de80b8ec67b8f7ed5c3
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665381"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Vanliga frågor och svar om Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via [SMB-protokollet (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)som är bransch standard. Du kan montera Azure-filresurser samtidigt i molnet eller lokala distributioner av Windows, Linux och macOS. Du kan också cachelagra Azure-filresurser på Windows Server-datorer med hjälp av Azure File Sync för snabb åtkomst nära var data används.

I den här artikeln besvaras vanliga frågor om Azure Files funktioner och funktioner, inklusive användning av Azure File Sync med Azure Files. Om du inte ser svaret på din fråga kan du kontakta oss genom följande kanaler (i eskalerad ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [Azure Storage-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Support. Om du vill skapa en ny supportbegäran går du till fliken **Hjälp** i Azure Portal, väljer **Hjälp + Support** -knappen och väljer sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>
  **hur är Azure Files användbart?**  
   Du kan använda Azure Files för att skapa fil resurser i molnet utan att du ansvarar för att hantera omkostnader för en fysisk server, enhet eller apparat. Vi gör det enfärgade arbetet åt dig, inklusive att tillämpa OS-uppdateringar och ersätta felaktiga diskar. Mer information om scenarier som Azure Files kan hjälpa dig med finns i [varför Azure Files är användbart](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Vad är olika sätt att komma åt filer i Azure Files?**  
    Du kan montera fil resursen på den lokala datorn med hjälp av SMB 3,0-protokollet, eller så kan du använda verktyg som [Storage Explorer](https://storageexplorer.com/) för att komma åt filer i fil resursen. Från ditt program kan du använda lagrings klient bibliotek, REST-API: er, PowerShell eller Azure CLI för att komma åt dina filer i Azure-filresursen.

* <a id="what-is-afs"></a>
  **Vad är Azure File Sync?**  
    Du kan använda Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Azure File Sync omvandlar dina Windows Server-datorer till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, Network File System (NFS) och File Transfer Protocol-tjänsten (FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

* <a id="files-versus-blobs"></a>
  **Varför ska jag använda en Azure-filresurs jämfört med Azure Blob Storage för mina data?**  
    Azure Files och Azure Blob Storage erbjuder båda sätt att lagra stora mängder data i molnet, men de är användbara i något annat syfte. 
    
    Azure Blob Storage är användbart för storskaliga molnbaserade program som behöver lagra ostrukturerade data. För att maximera prestanda och skalning är Azure Blob Storage en enklare lagrings abstraktion än ett faktiskt fil system. Du kan bara komma åt Azure Blob Storage via REST-baserade klient bibliotek (eller direkt via det REST-baserade protokollet).

    Azure Files är särskilt ett fil system. Azure Files har alla fil sammandrag som du känner till och älskar från flera år i arbetet med lokala operativ system. Precis som Azure Blob Storage erbjuder Azure Files ett REST-gränssnitt och REST-baserade klient bibliotek. Till skillnad från Azure Blob Storage erbjuder Azure Files SMB-åtkomst till Azure-filresurser. Genom att använda SMB kan du montera en Azure-filresurs direkt på Windows, Linux eller macOS, antingen lokalt eller i virtuella datorer i molnet, utan att behöva skriva någon kod eller bifoga särskilda driv rutiner till fil systemet. Du kan också cachelagra Azure-filresurser på lokala fil servrar genom att använda Azure File Sync för snabb åtkomst, nära var data används. 
   
    En mer detaljerad beskrivning av skillnaderna mellan Azure Files och Azure Blob Storage finns i [bestämma när du ska använda Azure Blob Storage, Azure Files eller Azure-diskar](../common/storage-decide-blobs-files-disks.md). Mer information om Azure Blob Storage finns i [Introduktion till Blob Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Varför ska jag använda en Azure-filresurs i stället för Azure-diskar?**  
    En disk på Azure-diskar är helt enkelt en disk. För att hämta värde från Azure-diskar måste du koppla en disk till en virtuell dator som körs i Azure. Azure-diskar kan användas för allt som du skulle använda för att använda en disk för på en lokal server. Du kan använda den som en OS-systemdisk, som växlings utrymme för ett operativ system eller som dedikerat lagrings utrymme för ett program. En intressant användning för Azure-diskar är att skapa en fil server i molnet som ska användas på samma platser där du kan använda en Azure-filresurs. Att distribuera en fil server i Azure Virtual Machines är ett högpresterande sätt att hämta fil lagring i Azure när du behöver distributions alternativ som för närvarande inte stöds av Azure Files (t. ex. NFS-protokoll stöd eller Premium lagring). 

    Att köra en fil server med Azure-diskar som backend-lagring är vanligt vis mycket dyrare än att använda en Azure-filresurs, av några skäl. Förutom att betala för disk lagring måste du också betala för kostnaden för att köra en eller flera virtuella Azure-datorer. För det andra måste du också hantera de virtuella datorer som används för att köra fil servern. Du ansvarar till exempel för uppgraderingar av operativ systemet. Slutligen, om du slutligen kräver att data cachelagras lokalt, är det upp till dig att konfigurera och hantera teknik för replikering, till exempel Distributed File System replikering (DFSR), för att göra det.

    En metod för att få det bästa av både Azure Files och en fil server som är värd för Azure Virtual Machines (förutom att använda Azure-diskar som backend-lagring) är att installera Azure File Sync på en fil server som är värd för en virtuell dator i molnet. Om Azure-filresursen finns i samma region som fil servern kan du aktivera moln skiktning och ange volymen ledigt utrymme i procent till maximalt (99%). Detta säkerställer minimal dubblering av data. Du kan också använda alla program som du vill ha med dina fil servrar, t. ex. program som kräver stöd för NFS-protokollet.

    Information om ett alternativ för att konfigurera en fil server med hög prestanda och hög tillgänglighet i Azure finns i [distribuera IaaS-gäst kluster för virtuella datorer i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer djupgående Beskrivning av skillnaderna mellan Azure Files och Azure-diskar finns i [bestämma när du ska använda Azure Blob Storage, Azure Files eller Azure-diskar](../common/storage-decide-blobs-files-disks.md). Mer information om Azure-diskar finns i [Översikt över azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Hur gör jag för att kom igång med Azure Files?**  
   Det är enkelt att komma igång med Azure Files. Först [skapar du en fil resurs](storage-how-to-create-file-share.md)och monterar den sedan i önskat operativ system: 

  * [Montera i Windows](storage-how-to-use-files-windows.md)
  * [Montera i Linux](storage-how-to-use-files-linux.md)
  * [Montera i macOS](storage-how-to-use-files-mac.md)

    En mer djupgående guide om hur du distribuerar en Azure-filresurs för att ersätta produktions fil resurser i organisationen finns i [Planera för en Azure Files distribution](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **vilka alternativ för lagring av redundans stöds av Azure Files?**  
    För närvarande har Azure Files stöd för lokalt redundant lagring (LRS), zon redundant lagring (ZRS), Geo-redundant lagring (GRS) och geo-Zone-redundant lagring (GZRS) (för hands version). Vi planerar att stödja lagring med Läs åtkomst till Geo-redundant (RA-GRS) i framtiden, men det finns inga tids linjer att dela just nu.

* <a id="tier-options"></a>
  **vilka lagrings nivåer stöds i Azure Files?**  
    Azure Files stöder två lagrings nivåer: Premium och standard. Standard fil resurser skapas i lagrings konton för generell användning (GPv1 eller GPv2) och Premium-filresurser skapas i FileStorage Storage-konton. Lär dig mer om hur du skapar [standard fil resurser](storage-how-to-create-file-share.md) och [Premium-filresurser](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Du kan inte skapa Azure-filresurser från Blob Storage-konton eller *Premium* -GPv1-eller GPv2-lagrings konton. Standard Azure-filresurser måste skapas i *standard* konton för generell användning och endast Premium Azure-filresurser måste skapas i FileStorage lagrings konton. GPv1-och GPv2-lagrings konton ( *Premium* General Purpose) är endast för-blobar för Premium sidor. 

* <a id="give-us-feedback"></a>
  **jag verkligen vill se en speciell funktion som har lagts till i Azure Files. Kan du lägga till det?**  
    Azure Filess teamet är intresserade av att höra och all feedback om vår tjänst. Rösta på en funktions förfrågan på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi ser fram emot att se om det finns många nya funktioner.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **vilka regioner stöds för Azure File Sync?**  
    Du hittar listan över tillgängliga regioner i avsnittet [region tillgänglighet](storage-sync-files-planning.md#region-availability) i Azure File Sync planerings guide. Vi lägger kontinuerligt till stöd för ytterligare regioner, inklusive icke-offentliga regioner.

* <a id="cross-domain-sync"></a>
  **kan jag ha domänanslutna och icke-domänanslutna servrar i samma Sync-grupp?**  
    Ja. En Sync-grupp kan innehålla Server slut punkter som har olika Active Directory medlemskap, även om de inte är domänanslutna. Även om den här konfigurationen tekniskt fungerar rekommenderar vi inte detta som en typisk konfiguration eftersom åtkomst kontrol listor (ACL: er) som har definierats för filer och mappar på en server kanske inte kan tillämpas av andra servrar i Sync-gruppen. För bästa resultat rekommenderar vi att du synkroniserar mellan servrar som finns i samma Active Directory skog, mellan servrar som finns i olika Active Directory skogar, men som har upprättat förtroende relationer, eller mellan servrar som inte finns i en domän. Vi rekommenderar att du inte använder en blandning av de här konfigurationerna.

* <a id="afs-change-detection"></a>
  **Jag skapade en fil direkt i min Azure-filresurs med hjälp av SMB eller i portalen. Hur lång tid tar det för filen att synkronisera till servrarna i Sync-gruppen?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Vad händer om samma fil ändras på två servrar på ungefär samma tid?**  
    Azure File Sync använder en enkel lösning för konflikt lösning: vi behåller båda ändringarna i filer som ändras på två servrar på samma gång. Den senast skrivna ändringen behåller det ursprungliga fil namnet. Den äldre filen har "källa"-datorn och konflikt numret som lagts till i namnet. Den följer denna taxonomi: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Den första konflikten i CompanyReport. docx skulle till exempel bli CompanyReport-CentralServer. docx om CentralServer är där den äldre skrivningen ägde rum. Den andra konflikten skulle ha namnet CompanyReport-CentralServer-1. docx. Azure File Sync stöder 100-konfliktskapande filer per fil. När det maximala antalet konfliktskapande filer har nåtts kommer filen inte att synkroniseras förrän antalet konfliktskapande filer är mindre än 100.

* <a id="afs-storage-redundancy"></a>
  **stöds Geo-redundant lagring för Azure File Sync?**  
    Ja, Azure Files stöder både lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS). Om du initierar ett lagrings konto med redundans mellan kopplade regioner från ett konto som har kon figurer ATS för GRS, rekommenderar Microsoft att du behandlar den nya regionen som en säkerhets kopia av enbart data. Azure File Sync börjar inte synkronisera automatiskt med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>
  **Varför stämmer inte *storleken på disk* egenskapen för en fil med egenskapen *size* efter att du använt Azure File Sync?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Hur kan jag se om en fil har flyttats på nivå?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**En fil som jag vill använda har flera nivåer. Hur kan jag återkalla filen till disk för att använda den lokalt?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>**vill du 
  hur gör jag för att tvinga en fil eller katalog att vara i nivå av?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **hur *mycket ledigt volym utrymme* som tolkas när jag har flera slut punkter för server på en volym?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **vilka filer eller mappar som ska undantas automatiskt av Azure File Sync?**  
  Se [filer som hoppats över](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **kan jag använda Azure File Sync med antingen Windows Server 2008 R2, Linux eller min nätverksanslutna lagrings enhet (NAS)?**  
    För närvarande stöder Azure File Sync endast Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2. Vi har just nu inte några andra planer som vi kan dela, men vi är öppna för att stödja ytterligare plattformar baserat på kundens efter frågan. Berätta för oss på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) vilka plattformar som vi skulle vilja stödja.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Varför finns det skiktade filer utanför Server slut punktens namn område?**  
    Innan Azure File Sync agent version 3, Azure File Sync blockerade flyttningen av skiktade filer utanför Server slut punkten men på samma volym som server slut punkten. Kopierings åtgärder, flyttning av filer som inte är på nivå, och flyttningar av nivå till andra volymer påverkades inte. Orsaken till detta är att det implicita antagandet att Utforskaren och andra Windows-API: er har den här flytt åtgärden på samma volym är (nästan) momentant namnbytes åtgärder. Det innebär att flyttningar gör att Utforskaren eller andra flyttnings metoder (t. ex. kommando rad eller PowerShell) inte svarar medan Azure File Sync kommer att anropa data från molnet igen. Från och med [Azure File Sync agent version 3.0.12.0](storage-files-release-notes.md#supported-versions)kan Azure File Sync flytta en nivå fil utanför Server slut punkten. Vi undviker de negativa effekter som tidigare nämnts genom att tillåta att den skiktade filen finns som en nivå fil utanför Server slut punkten och sedan anropar filen i bakgrunden. Det innebär att flytten av samma volym är omedelbar och vi gör allt arbete för att återkalla filen till disken när flytten har slutförts. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Jag har problem med Azure File Sync på min server (synkronisering, moln nivå osv.). Bör jag ta bort och återskapa min server slut punkt?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **kan jag flytta synkroniseringstjänsten för lagring och/eller lagrings konto till en annan resurs grupp eller prenumeration?**  
   Ja, tjänsten Storage Sync och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.
    
* <a id="afs-ntfs-acls"></a>
  kan **Azure File Sync bevara NTFS-ACL: er för katalog-/fil nivå tillsammans med data som lagras i Azure Files?**

    NTFS-ACL: er som överförs från lokala fil servrar behålls genom Azure File Sync som metadata. Azure Files stöder inte autentisering med Azure AD-autentiseringsuppgifter för åtkomst till fil resurser som hanteras av tjänsten Azure File Sync.
    
## <a name="security-authentication-and-access-control"></a>Säkerhet, autentisering och åtkomst kontroll
* <a id="ad-support"></a>
**är identitets baserad autentisering och åtkomst kontroll som stöds av Azure Files?**  
    
    Ja, Azure Files har stöd för identitetsbaserade autentiserings-och åtkomst kontroll som utnyttjar Azure AD Domain Service (Azure AD DS). Azure AD DS-autentisering över SMB för Azure Files gör det möjligt för Azure AD DS-domänanslutna virtuella Windows-datorer att få åtkomst till resurser, kataloger och filer med hjälp av autentiseringsuppgifter för Azure AD. Mer information finns i [Översikt över stöd för Azure AD DS-autentisering (Azure Files Azure Active Directory Domain Service) för SMB-åtkomst](storage-files-active-directory-overview.md). 

    Azure Files erbjuder ytterligare två sätt att hantera åtkomst kontroll:

    - Du kan använda signaturer för delad åtkomst (SAS) för att skapa token som har specifika behörigheter och som är giltiga för ett visst tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en bestämd fil som har en giltighet på 10 minuter. Alla som har token när token är giltiga har skrivskyddad åtkomst till filen under 10 minuter. Signaturer för delad åtkomst stöds endast via REST API eller i klient bibliotek. Du måste montera Azure-filresursen över SMB genom att använda lagrings konto nycklarna.

    - Azure File Sync bevarar och replikerar alla godtyckliga ACL: er, eller DACL: er (oavsett om de är Active Directory-baserade eller lokala) till alla Server slut punkter som den synkroniserar till. Eftersom Windows Server redan kan autentiseras med Active Directory, är Azure File Sync ett effektivt Stop-intervall-alternativ tills fullt stöd för Active Directory-baserad autentisering och ACL-stöd tas emot.
    
    Du kan referera till att [auktorisera åtkomst till Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för en omfattande åter givning av alla protokoll som stöds på Azure Storage Services. 

* <a id="ad-support-devices"></a>
stöder **Azure Files Azure AD DS-autentisering SMB-åtkomst med Azure AD-autentiseringsuppgifter från enheter som är anslutna till eller registrerade med Azure AD?**

    Nej, det här scenariot stöds inte.

* <a id="ad-support-rest-apis"></a>
**finns REST-API: er för att ge stöd åt/ange/kopiera katalog/fil NTFS ACL: er?**

    För tillfället har vi inte stöd för REST-API: er för att hämta, ange eller kopiera NTFS ACL: er för kataloger eller filer.

* <a id="ad-vm-subscription"></a>
**kan jag komma åt Azure Files med Azure AD-autentiseringsuppgifter från en virtuell dator under en annan prenumeration?**

    Om prenumerationen som fil resursen distribueras till är kopplad till samma Azure AD-klient som den Azure AD Domain Services distribution som den virtuella datorn är domänansluten till, kan du sedan komma åt Azure Files med samma autentiseringsuppgifter för Azure AD. Begränsningen införs inte i prenumerationen, men på den associerade Azure AD-klienten.    
    
* <a id="ad-support-subscription"></a>
**kan jag aktivera Azure Files Azure AD DS-autentisering med en Azure AD-klient som skiljer sig från den primära klient organisation som fil resursen är kopplad till?**

    Nej, Azure Files stöder bara Azure AD DS-integrering med en Azure AD-klient som finns i samma prenumeration som fil resursen. Det går bara att koppla en prenumeration till en Azure AD-klient.

* <a id="ad-linux-vms"></a>
**har Azure Files Azure AD DS-autentisering stöd för virtuella Linux-datorer?**

    Nej, autentisering från virtuella Linux-datorer stöds inte.

* <a id="ad-aad-smb-afs"></a>
**kan jag utnyttja Azure Files Azure AD DS-autentisering på fil resurser som hanteras av Azure File Sync?**

    Nej, Azure Files stöder inte bevarande av NTFS ACL: er på fil resurser som hanteras av Azure File Sync. De ACL: er för filer som överförs från lokala fil servrar behålls av Azure File Sync. Alla NTFS ACL: er som kon figurer ATS internt mot Azure Files skrivs över av Azure File Syncs tjänsten. Dessutom stöder Azure Files inte autentisering med Azure AD-autentiseringsuppgifter för åtkomst till fil resurser som hanteras av tjänsten Azure File Sync.

* <a id="encryption-at-rest"></a>
**Hur kan jag se till att min Azure-filresurs är krypterad i vila?**  

    Ja. Mer information finns i [Azure Storage tjänst kryptering](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hur kan jag ge åtkomst till en speciell fil med hjälp av en webbläsare?**  

    Du kan använda signaturer för delad åtkomst för att skapa token som har specifika behörigheter och som är giltiga för ett visst tidsintervall. Du kan till exempel generera en token som ger skrivskyddad åtkomst till en viss fil under en angiven tids period. Alla som har URL: en kan komma åt filen direkt från valfri webbläsare medan token är giltig. Du kan enkelt skapa en signatur för delad åtkomst från ett användar gränssnitt som Storage Explorer.

* <a id="file-level-permissions"></a>
**är det möjligt att ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen?**  

    Om du monterar fil resursen med hjälp av SMB, saknar du kontroll över behörigheter på mappnivå. Men om du skapar en signatur för delad åtkomst med hjälp av REST API-eller klient bibliotek kan du ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen.

* <a id="ip-restrictions"></a>
**kan jag implementera IP-begränsningar för en Azure-filresurs?**  

    Ja. Åtkomst till Azure-filresursen kan begränsas på lagrings konto nivå. Mer information finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**vilka policyer för efterlevnadsprinciper stöder Azure Files?**  

   Azure Files körs ovanpå samma lagrings arkitektur som används i andra lagrings tjänster i Azure Storage. Azure Files använder samma policyer för efterlevnadsprinciper som används i andra Azure Storage-tjänster. För mer information om hur du Azure Storage data, kan du referera till [Azure Storage Compliance-erbjudanden](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)och gå till [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).

## <a name="on-premises-access"></a>Lokal åtkomst

* <a id="port-445-blocked"></a>
**min Internet leverantör eller blockerar Port 445 som inte Azure Files monteras. Vad ska jag göra?**

    Du kan lära dig om [olika sätt att lösa den blockerade port 445 här](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Azure Files tillåter endast anslutningar med SMB 3,0 (med krypterings stöd) utanför regionen eller data centret. SMB 3,0-protokollet har infört många säkerhetsfunktioner, inklusive kanal kryptering som är mycket säkert att använda via Internet. Men det är möjligt att port 445 har blockerats på grund av historiska orsaker till de lägre SMB-versionerna. I det idealiska fallet bör porten blockeras endast för SMB 1,0-trafik och SMB 1,0 bör stängas av på alla klienter.

* <a id="expressroute-not-required"></a>
**måste jag använda Azure-ExpressRoute för att ansluta till Azure Files eller använda Azure File Sync lokalt?**  

    Nej. ExpressRoute krävs inte för att få åtkomst till en Azure-filresurs. Om du monterar en Azure-filresurs direkt lokalt, är allt det som krävs att port 445 (TCP utgående) är öppen för Internet åtkomst (detta är den port som SMB använder för att kommunicera). Om du använder Azure File Sync är allt det som krävs port 443 (TCP utgående) för HTTPS-åtkomst (ingen SMB krävs). Du *kan* dock använda ExpressRoute med något av dessa åtkomst alternativ.

* <a id="mount-locally"></a>
**Hur kan jag montera en Azure-filresurs på min lokala dator?**  

    Du kan montera fil resursen med hjälp av SMB-protokollet om Port 445 (TCP utgående) är öppen och klienten stöder SMB 3,0-protokollet (till exempel om du använder Windows 10 eller Windows Server 2016). Om Port 445 blockeras av din organisations princip eller av din Internet leverantör kan du använda Azure File Sync för att få åtkomst till Azure-filresursen.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hur gör jag för att säkerhetskopiera min Azure-filresurs?**  
    Du kan använda periodiska [resurs ögonblicks bilder](storage-snapshots-files.md) för att skydda mot oavsiktlig borttagning. Du kan också använda AzCopy, Robocopy eller ett säkerhets kopierings verktyg från tredje part som kan säkerhetskopiera en monterad fil resurs. Azure Backup erbjuder säkerhets kopiering av Azure Files. Läs mer om hur du [säkerhetskopierar Azure-filresurser genom att Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Resursögonblicksbilder

### <a name="share-snapshots-general"></a>Dela ögonblicks bilder: allmänt
* <a id="what-are-snaphots"></a>
**Vad är ögonblicks bilder av fil resurser?**  
    Du kan använda Azures ögonblicks bilder av fil resurser för att skapa en skrivskyddad version av dina fil resurser. Du kan också använda Azure Files för att kopiera en tidigare version av innehållet tillbaka till samma resurs, till en annan plats i Azure eller lokalt för fler ändringar. Mer information om resurs ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**var lagras mina resurs ögonblicks bilder?**  
    Resurs ögonblicks bilder lagras i samma lagrings konto som fil resursen.

* <a id="snapshot-consistency"></a>
**delar ögonblicks bilder program-konsekventa?**  
    Nej, resurs ögonblicks bilder är inte programkonsekventa. Användaren måste tömma Skriv åtgärder från programmet till resursen innan du tar bort ögonblicks bilden.

* <a id="snapshot-limits"></a>
finns **det några begränsningar för hur många resurs-ögonblicksbilder jag kan använda?**  
    Ja. Azure Files kan behålla maximalt 200 resurs ögonblicks bilder. Resurs ögonblicks bilder räknas inte mot resurs kvoten, så det finns ingen gräns per resurs för det totala utrymmet som används av alla resurs ögonblicks bilder. Lagrings konto gränser gäller fortfarande. Efter 200 resurs ögonblicks bilder måste du ta bort äldre ögonblicks bilder för att skapa nya ögonblicks bilder av resursen.

* <a id="snapshot-cost"></a>
**hur mycket kostar det att dela ögonblicks bilder?**  
    Standard transaktioner och standard lagrings kostnader gäller för ögonblicks bilder. Ögonblicks bilder är stegvisa. Den grundläggande ögonblicks bilden är själva resursen. Alla efterföljande ögonblicks bilder är stegvisa och kommer bara att lagra differensen från den tidigare ögonblicks bilden. Det innebär att delta ändringarna som visas på fakturan blir minimala om din arbets belastnings omsättning är minimal. Se [prissättnings sida](https://azure.microsoft.com/pricing/details/storage/files/) för standard Azure Files pris information. Idag är det dags att titta på den storlek som används av resurs ögonblicks bilden genom att jämföra den fakturerade kapaciteten med den använda kapaciteten. Vi arbetar på verktyg för att förbättra rapporteringen.

* <a id="ntfs-acls-snaphsots"></a>
**är NTFS ACL: er på kataloger och filer som behålls i resurs ögonblicks bilder?**  
    NTFS ACL: er för kataloger och filer finns kvar i resurs ögonblicks bilder.

### <a name="create-share-snapshots"></a>Skapa resurs ögonblicks bilder
* <a id="file-snaphsots"></a>
**kan jag skapa en resurs ögonblicks bild av enskilda filer?**  
    Resurs ögonblicks bilder skapas på fil resurs nivå. Du kan återställa enskilda filer från ögonblicks bilden av fil resursen, men du kan inte skapa resurs ögonblicks bilder på filnivå. Men om du har tagit en ögonblicks bild på resurs nivå och vill visa en lista över resurs ögonblicks bilder där en fil har ändrats, kan du göra detta under **tidigare versioner** på en Windows-monterad resurs. 
    
    Om du behöver en fil ögonblicks bilds funktion kan du meddela oss på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**kan jag skapa resurs ögonblicks bilder av en krypterad fil resurs?**  
    Du kan ta en ögonblicks bild av Azure-filresurser som har kryptering i vila aktiverat. Du kan återställa filer från en resurs ögonblicks bild till en krypterad fil resurs. Om din resurs är krypterad krypteras även din resurs ögonblicks bild.

* <a id="geo-redundant-snaphsots"></a>
**är mina resurs ögonblicks bilder geo-redundanta?**  
    Resurs ögonblicks bilder har samma redundans som den Azure-filresurs som de vidtogs för. Om du har valt Geo-redundant lagring för ditt konto lagras även din resurs ögonblicks bild i den kopplade regionen.

### <a name="manage-share-snapshots"></a>Hantera resurs ögonblicks bilder
* <a id="browse-snapshots-linux"></a>
**kan jag bläddra i mina resurs ögonblicks bilder från Linux?**  
    Du kan använda Azure CLI för att skapa, Visa, bläddra i och återställa resurs ögonblicks bilder i Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**kan jag kopiera resurs ögonblicks bilderna till ett annat lagrings konto?**  
    Du kan kopiera filer från resurs ögonblicks bilder till en annan plats, men du kan inte kopiera själva resurs ögonblicks bilderna.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från resurs ögonblicks bilder
* <a id="promote-share-snapshot"></a>
**kan jag befordra en resurs ögonblicks bild till bas resursen?**  
    Du kan kopiera data från en resurs ögonblicks bild till andra mål. Du kan inte befordra en resurs ögonblicks bild till bas resursen.

* <a id="restore-snapshotted-file-to-other-share"></a>
**kan jag återställa data från min resurs ögonblicks bild till ett annat lagrings konto?**  
    Ja. Filer från en resurs ögonblicks bild kan kopieras till den ursprungliga platsen eller till en annan plats som innehåller antingen samma lagrings konto eller ett annat lagrings konto, antingen i samma region eller i olika regioner. Du kan också kopiera filer till en lokal plats eller till ett annat moln.    
  
### <a name="clean-up-share-snapshots"></a>Rensa resurs ögonblicks bilder
* <a id="delete-share-keep-snapshots"></a>
**kan jag ta bort min resurs men inte ta bort mina resurs ögonblicks bilder?**  
    Om du har ögonblicks bilder av aktiva resurser på resursen kan du inte ta bort resursen. Du kan använda ett API för att ta bort resurs ögonblicks bilder, tillsammans med resursen. Du kan också ta bort både resurs ögonblicks bilder och resursen i Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Vad händer med mina resurs ögonblicks bilder om jag tar bort mitt lagrings konto?**  
    Om du tar bort ditt lagrings konto tas även resurs ögonblicks bilderna bort.

## <a name="billing-and-pricing"></a>Fakturering och priser
* <a id="vm-file-share-network-traffic"></a>
är **nätverks trafiken mellan en virtuell Azure-dator och en Azure-filresurs räknas som extern bandbredd som debiteras för prenumerationen?**  
    Om fil resursen och den virtuella datorn finns i samma Azure-region finns det ingen extra kostnad för trafiken mellan fil resursen och den virtuella datorn. Om fil resursen och den virtuella datorn finns i olika regioner debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>
**hur mycket kostar det att dela ögonblicks bilder?**  
     Under för hands versionen kostar det inget att dela ögonblicks bilds kapacitet. Utgående och transaktionskostnader gäller för standard lagring. Efter den allmänna tillgängligheten debiteras prenumerationer för kapacitet och transaktioner för resurs ögonblicks bilder.
     
     Resurs ögonblicks bilder är stegvisa. Bas resursens ögonblicks bild är själva resursen. Alla efterföljande ögonblicks bilder av resurser är stegvisa och lagrar bara skillnaden från föregående ögonblicks bild. Du faktureras bara för det ändrade innehållet. Om du har en resurs med 100 GiB av data men bara 5 GiB har ändrats sedan den senaste ögonblicks bilden av en resurs, förbrukar resurs ögonblicks bilden bara fem ytterligare GiB och du debiteras för 105 GiB. Mer information om transaktions-och standard avgifter för utgående trafik finns på [sidan med priser](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skala och prestanda
* <a id="files-scale-limits"></a>
**vilka skalnings gränser Azure Files?**  
    Information om skalbarhets-och prestanda mål för Azure Files finns i [Azure Files skalbarhets-och prestanda mål](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**vilka storlekar är tillgängliga för Azure-filresurser?**  
    Azures fil resurs storlekar (Premium och standard) kan skala upp till 100 TiB. Se avsnittet [Publicera till större fil resurser (standard nivå)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) i planerings guiden för att integrera instruktioner till de större fil resurserna för standard nivån.

* <a id="lfs-performance-impact"></a>
**utökas kvoten för fil resurser för mina arbets belastningar eller Azure File Sync?**
    
    Nej. Att utöka kvoten påverkar inte arbets belastningarna eller Azure File Sync.

* <a id="open-handles-quota"></a>
**hur många klienter kan komma åt samma fil samtidigt?**    
    Det finns en kvot på 2 000 öppna referenser på en enskild fil. När du har 2 000 öppna handtag visas ett fel meddelande om att kvoten har nåtts.

* <a id="zip-slow-performance"></a>
**min prestanda är långsam när jag packar upp filer i Azure Files. Vad ska jag göra?**  
    Om du vill överföra ett stort antal filer till Azure Files rekommenderar vi att du använder AzCopy (för Windows, i för hands versionen för Linux och UNIX) eller Azure PowerShell. Dessa verktyg har optimerats för nätverks överföring.

* <a id="slow-perf-windows-81-2012r2"></a>
**varför minskar prestandan när jag har monterat min Azure-filresurs på Windows Server 2012 R2 eller windows 8,1?**  
    Det finns ett känt problem när du monterar en Azure-filresurs på Windows Server 2012 R2 och Windows 8,1. Problemet har korrigerats i den kumulativa uppdateringen från april 2014 för Windows 8,1 och Windows Server 2012 R2. För bästa prestanda bör du se till att alla instanser av Windows Server 2012 R2 och Windows 8,1 har den här korrigerings filen tillämpad. (Du bör alltid ta emot Windows-korrigeringsfiler via Windows Update.) Mer information finns i den tillhör ande Microsoft Knowledge Base-artikeln [långsamma prestanda när du kommer åt Azure Files från Windows 8,1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och samverkan med andra tjänster
* <a id="cluster-witness"></a>
**kan jag använda min Azure-filresurs som ett *fil resurs vittne* för mitt Windows Server-redundanskluster?**  
    Den här konfigurationen stöds för närvarande inte för en Azure-filresurs. Mer information om hur du konfigurerar detta för Azure Blob Storage finns i [distribuera ett moln vittne för ett redundanskluster](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**kan jag montera en Azure-filresurs på en Azure Container instance?**  
    Ja, Azure-filresurser är ett bra alternativ när du vill spara information utanför livs längden för en behållar instans. Mer information finns i [montera en Azure-filresurs med Azure Container instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
finns **det en namnbytes åtgärd i REST API?**  
    Nej, inte just nu.

* <a id="nested-shares"></a>
**kan jag konfigurera kapslade resurser? Med andra ord, en resurs under en resurs?**  
    Nej. Fil resursen *är* den virtuella driv rutin som du kan montera, så kapslade resurser stöds inte.

* <a id="ibm-mq"></a>
**Hur gör jag för att använda Azure Files med IBM MQ?**  
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder att konfigurera Azure Files med IBM-tjänsten. Mer information finns i [så här konfigurerar du en IBM MQ multi-instance Queue Manager med Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se också
* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)
