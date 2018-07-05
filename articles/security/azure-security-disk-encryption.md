---
title: Azure Disk Encryption för Windows och Linux IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure Disk Encryption för Windows och Linux IaaS-datorer.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: devtiw
ms.openlocfilehash: f350716d0ca906376f3eadce9e117694ff14515c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2018
ms.locfileid: "35756404"
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption för Windows och Linux IaaS-datorer
Microsoft Azure värnar starkt din datasekretess, datasuveränitet och kan du styra dina Azure-värdbaserade data via en rad avancerade tekniker för att kryptera, styra och hantera krypteringsnycklar, kontroll och granskning åtkomst av data. Detta ger Azure-kunder flexibilitet att välja den lösning som bäst uppfyller sina affärsbehov. I det här dokumentet beskrivs vi i den nya tekniken lösningen ”Azure Disk Encryption för Windows och Linux IaaS VMS” för att skydda och skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. Dokumentet innehåller detaljerad information om hur du använder Azure disk encryption-funktioner, inklusive scenarierna som stöds och användaren inträffar.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Översikt
Azure Disk Encryption är en ny funktion som hjälper dig att kryptera din Windows- och Linux IaaS VM-diskar. Azure Disk Encryption utnyttjar branschstandard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer för Operativsystemet och datadiskarna. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Lösningen innebär också att alla data på diskar i virtuella datorer krypteras i vila i din Azure-lagring.

Azure disk encryption för Windows och Linux IaaS-datorer är nu i **allmänt tillgängliga** i alla offentliga Azure-regioner och AzureGov regioner för Standard virtuella datorer och virtuella datorer med premium storage.

> [!NOTE]
> Vissa rekommendationerna kan öka data, nätverk eller Resursanvändning för beräkning, vilket resulterar i ytterligare kostnader för licens eller prenumeration.


### <a name="encryption-scenarios"></a>Krypteringsscenarier
Azure Disk Encryption-lösningen har stöd för följande kundscenarier:

* Aktivera kryptering på den nya virtuella IaaS-datorer skapas från förkrypterade VHD och krypteringsnycklar
* Aktivera kryptering på den nya virtuella IaaS-datorer skapas från stöds galleriavbildningar för Azure
* Aktivera kryptering på befintliga virtuella IaaS-datorer som körs i Azure
* Inaktivera kryptering på Windows virtuella IaaS-datorer
* Inaktivera kryptering på enheter för virtuella Linux IaaS-datorer
* Aktivera kryptering av virtuella datorer med hanterade diskar
* Uppdatera krypteringsinställningarna för en befintlig krypterade premium- och icke-premium storage VM
* Säkerhetskopiering och återställning av krypterade virtuella datorer

Lösningen stöder följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault
* Standard-nivån virtuella datorer: [A, D, DS, G, GS, F och så vidare serien virtuella IaaS-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Aktivera kryptering på Windows och Linux IaaS-datorer och virtuella datorer med hanterade diskar från stöds avbildningarna i Azure-galleriet
* Inaktivera kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer och virtuella datorer med hanterade diskar
* Inaktivera kryptering på enheter för virtuella Linux IaaS-datorer och virtuella datorer med hanterade diskar
* Aktivera kryptering på den virtuella IaaS-datorer som kör Windows klient-OS
* Aktivera kryptering på volymer med monteringssökvägar
* Aktivera kryptering på Linux-datorer som konfigurerats med disk striping (RAID) med hjälp av mdadm
* Aktivera kryptering på virtuella Linux-datorer med hjälp av LVM för datadiskar
* Aktivera kryptering på Linux LVM 7.3 för operativsystem och datadiskar 
* Aktivera kryptering på Windows virtuella datorer som konfigurerats med lagringsutrymmen
* Uppdatera krypteringsinställningarna för en befintlig krypterade premium- och icke-premium storage VM
* Säkerhetskopiering och återställning av krypterade virtuella datorer för både utan KEK och KEK-scenarier (KEK - krypteringsnyckel)
* Alla offentliga Azure och AzureGov regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och -teknik:

* Basic-nivån virtuella IaaS-datorer
* Inaktivering av kryptering på en OS-enhet för virtuella Linux IaaS-datorer
* Inaktivera kryptering på en dataenhet om Operativsystemets disk är krypterad för virtuella Linux Iaas-datorer
* Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator
* Aktivera kryptering på Windows och Linux IaaS-VM kund anpassade avbildningar inte stöds.
* Integrering med din lokala nyckelhanteringstjänst
* Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och Windows-datorer som är konfigurerade med programvarubaserade RAID-system

### <a name="encryption-features"></a>Krypteringsfunktioner
När du aktiverar och distribuera Azure Disk Encryption för virtuella Azure IaaS-datorer, aktiveras följande funktioner, beroende på konfigurationen som tillhandahålls:

* Kryptering av OS-volymen för att skydda startvolymen i vila i lagring
* Kryptering av datavolymer för att skydda datavolymer i vila i lagring
* Inaktivering av kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer
* Inaktivera kryptering av data enheter för virtuella Linux IaaS-datorer (endast om operativsystemenhet är inte krypterad)
* Skydda krypteringsnycklar och hemligheter i key vault-prenumeration
* Rapportering krypteringsstatus för krypterade IaaS VM
* Borttagning av diskkryptering konfigurationsinställningar från IaaS-dator
* Säkerhetskopiering och återställning av krypterade virtuella datorer med hjälp av Azure Backup-tjänsten

Azure Disk Encryption för IaaS VMS för Windows och Linux-lösningen innehåller:

* Disk-encryption-tillägget för Windows.
* Disk-encryption-tillägget för Linux.
* Diskkryptering PowerShell-cmdlets.
* Disk-kryptering cmdlets för Azure-kommandoradsgränssnittet (CLI).
* Diskkryptering – Azure Resource Manager-mallar.

Azure Disk Encryption-lösningen stöds för virtuella IaaS-datorer som kör Windows eller Linux-operativsystem. Mer information om operativsystem som stöds finns i avsnittet ”förutsättningar”.

> [!NOTE]
> Det finns ingen extra kostnad för att kryptera Virtuella diskar med Azure Disk Encryption.

### <a name="value-proposition"></a>Förslagsvärde
När du använder Azure Disk Encryption-hanteringslösningen så kan du uppfylla följande affärsbehov:

* Virtuella IaaS-datorer är skyddade i vila, eftersom du kan använda vanliga krypteringsteknik för att uppfylla organisationens krav för säkerhet och efterlevnad.
* Start för virtuella IaaS-datorer under kund-kontrollerade nycklar och principer och du kan granska deras användning i ditt nyckelvalv.

### <a name="encryption-workflow"></a>Arbetsflöde för kryptering
Om du vill aktivera diskkryptering för Windows och Linux-datorer, gör du följande:

1. Välj ett scenario för kryptering bland de föregående kryptering-scenarierna.
2. Välj att aktivera diskkryptering via Azure Disk Encryption Resource Manager-mall, PowerShell-cmdletar eller CLI-kommando och ange krypteringskonfiguration.

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange kryptering konfigurationen för att aktivera kryptering på en ny IaaS-VM.
   * Tillhandahåller kryptering konfigurationen för att aktivera kryptering på IaaS-VM för nya virtuella datorer som har skapats från Marketplace och befintliga virtuella datorer som redan körs i Azure.

3. Bevilja åtkomst till Azure-plattformen för att läsa krypteringsnyckeln material (BitLocker-krypteringsnycklar för Windows-System) och lösenfras för Linux från ditt nyckelvalv och aktivera kryptering på IaaS-VM.

4. Ange Programidentitet Azure Active Directory (Azure AD) för att skriva krypteringsnyckeln material till ditt nyckelvalv. Detta aktiverar kryptering på IaaS-VM för scenarier som nämns i steg 2.

5. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.

 ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Arbetsflöde för dekryptering
Om du vill inaktivera disk encryption för virtuella IaaS-datorer, utför du följande anvisningar:

1. Välja att inaktivera kryptering (dekryptering) på en pågående IaaS-VM i Azure via Azure Disk Encryption Resource Manager-mall eller PowerShell-cmdletar och anger önskad konfiguration för dekryptering.

 Det här steget inaktiverar kryptering av Operativsystemet datavolym och/eller som kör Windows virtuella IaaS-datorer. Men som nämns i föregående avsnitt, stöds inaktivering av OS-diskkryptering för Linux inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer som OS-disken inte är krypterad.
2. Azure uppdaterar modellen för VM-service och IaaS VM markeras dekrypterade. Innehållet i den virtuella datorn är inte längre krypterade i vila.

> [!NOTE]
> Inaktivera kryptering åtgärden tar inte bort nyckelvalvet och nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) eller en lösenfras för Linux.
 > Inaktivering av OS-diskkryptering för Linux stöds inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer.
Inaktivera data-diskkryptering för Linux stöds inte om den OS-enheten är krypterad.

## <a name="prerequisites"></a>Förutsättningar
Innan du aktiverar Azure Disk Encryption på virtuella Azure IaaS-datorer för de scenarier som stöds som beskrivs i avsnittet ”Översikt”, se följande krav:

* Du måste ha en giltig aktiv Azure-prenumeration att skapa resurser i Azure i regionerna som stöds.
* Azure Disk Encryption stöds i följande Windows Server-versioner: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016.
* Azure Disk Encryption stöds på följande Windows-klientversioner: klienten för Windows 8 och Windows 10-klient.

> [!NOTE]
> Du måste ha .NET Framework 4.5 installerat innan du aktiverar kryptering i Azure för Windows Server 2008 R2. Du kan installera det från Windows Update genom att installera valfri Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Azure Disk Encryption är bara stöds på specifika Azure-galleriet bygger Linux server-distributioner och versioner.  Listan över versioner som stöds för närvarande finns i den [Azure Disk Encryption vanliga frågor och svar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq).

* Azure Disk Encryption kräver att dina nyckelvalv och virtuella datorer finns i samma Azure-region och prenumeration.

> [!NOTE]
> Konfigurera resurserna i olika områden orsakar ett fel i Azure Disk Encryption-funktionen aktiveras.

* Om du vill installera och konfigurera ditt nyckelvalv för Azure Disk Encryption, i avsnittet **ange upp och konfigurera ditt nyckelvalv för Azure Disk Encryption** i den *krav* i den här artikeln.
* Om du vill installera och konfigurera Azure AD-program i Azure Active directory för Azure Disk Encryption, i avsnittet **konfigurera Azure AD-program i Azure Active Directory** i den *krav* delen av den här artikeln.
* Om du vill installera och konfigurera nyckelvalvets åtkomstprincip för Azure AD-programmet, i avsnittet **konfigurera nyckelvalvets åtkomstprincip för Azure AD-programmet** i den *krav* i den här artikeln.
* För att förbereda en förkrypterade Windows virtuell Hårddisk finns i avsnittet **Förbered en förkrypterade Windows virtuell Hårddisk** i den *bilaga*.
* För att förbereda en förkrypterade Linux-VHD, i avsnittet **förbereda en VHD för Linux-förkrypterade** i den *bilaga*.
* Azure-plattformen behöver åtkomst till krypteringsnycklar och hemligheter i ditt nyckelvalv och göra dem tillgängliga för den virtuella datorn när den startas och dekrypterar operativsystemvolymen virtuell dator. Om du vill tilldela behörigheter till Azure-plattformen, ange den **EnabledForDiskEncryption** -egenskapen i nyckelvalvet. Mer information finns i **ange upp och konfigurera ditt nyckelvalv för Azure Disk Encryption** i tillägget.
* Din hemlighet i nyckelvalvet och KEK URL: er måste vara en ny version. Azure tillämpar den här begränsningen för versionshantering. Giltigt hemlighet och KEK URL: er finns i följande exempel:

  * Exempel på en giltig hemlig URL:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig KEK-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption stöder inte att ange portnummer som en del av key vault-hemligheter och KEK URL: er. Exempel på URL: er inte stöds och stöds key vault finns i följande:

  * Oacceptabel key vault-Webbadress  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Godkända key vault-Webbadress:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Om du vill aktivera Azure Disk Encryption måste funktion, den virtuella IaaS-datorer uppfylla följande nätverk endpoint konfigurationskrav:
  * Om du vill hämta en token för att ansluta till ditt nyckelvalv, IaaS VM måste vara kan ansluta till en Azure Active Directory-slutpunkt \[login.microsoftonline.com\].
  * Om du vill skriva dem till ditt nyckelvalv kunna IaaS VM ansluta till slutpunkten för nyckelvalvet.
  * IaaS VM måste kunna ansluta till en Azure storage-slutpunkt som är värd för lagringsplatsen Azure-tillägget och ett Azure storage-konto som är värd för VHD-filer.

  > [!NOTE]
  > Om din säkerhetsprincip begränsar åtkomst från virtuella Azure-datorer till Internet, kan du matcha den föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutning till IP-adresserna.
  >
  >Konfigurera och komma åt Azure Key Vault bakom en brandvägg (https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Använd den senaste versionen av Azure PowerShell SDK-version för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell-version](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Azure Disk Encryption stöds inte på [Azure PowerShell SDK version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Om du får ett fel som rör med Azure PowerShell 1.1.0, se [Azure Disk Encryption fel relaterade till Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Om du vill köra alla Azure CLI-kommandon och associera den med din Azure-prenumeration, måste du först installera Azure CLI:
  * Om du vill installera Azure CLI och associera den med din Azure-prenumeration, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md).
  * Om du vill använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager, se [Azure CLI-kommandon i Resource Manager-läge](../virtual-machines/azure-cli-arm-commands.md).

* När du krypterar en hanterad disk, är det obligatoriskt krav för att kunna ta en ögonblicksbild av den hantera disken eller en säkerhetskopia av disken utanför Azure Disk Encryption innan du aktiverar krypteringen.  Utan en säkerhetskopia på plats, kan ett oväntat fel under krypteringen kanske den disken och VM användas utan ett återställningsalternativ.  Set-AzureRmVMDiskEncryptionExtension för närvarande inte säkerhetskopiering av hanterade diskar och kommer fel om användas mot en hanterad disk om de inte parametern - skipVmBackup har angetts.  Den här parametern är inte säkert att använda såvida inte en säkerhetskopia har redan gjorts utanför Azure Disk Encryption.   När du anger parametern - skipVmBackup cmdleten inte att skapa en säkerhetskopia av den hanterade disken före kryptering.  Därför anses en obligatorisk förutsättning för att se till att en säkerhetskopia av den hantera disken virtuell dator är på plats innan du kan aktivera Azure Disk Encryption om återställning krävs senare.  
> [!NOTE]
 > Parametern - skipVmBackup bör aldrig användas om inte en ögonblicksbild eller säkerhetskopiering har redan gjorts utanför Azure Disk Encryption. 

* Azure Disk Encryption-lösningen använder de externa nyckelskyddet för BitLocker för Windows virtuella IaaS-datorer. Skicka alla grupprinciper som tillämpar TPM-skydd för domän domänanslutna virtuella datorer, inte. Läs om hur en grupprincip för ”Tillåt BitLocker utan en kompatibel TPM” [gruppolicy referens för BitLocker](https://technet.microsoft.com/library/ee706521).
* BitLocker-principen på domänanslutna virtuella datorer med anpassade Grupprincip måste innehålla följande inställning: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption misslyckas om anpassade grupprincipinställningarna för Bitlocker är inte kompatibla. På datorer som inte har rätt princip kan inställningen, tillämpar den nya principen, att tvinga den nya principen för att uppdatera (gpupdate.exe/Force) och sedan omstart krävas.  
* Om du vill skapa ett Azure AD-program, skapa ett nyckelvalv, eller konfigurera ett befintligt nyckelvalv och aktivera kryptering kan du läsa den [PowerShell-skript för Azure Disk Encryption nödvändiga](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Om du vill konfigurera krav för disk-kryptering med Azure CLI finns i [Bash-skript](https://github.com/ejarvi/ade-cli-getting-started).
* Kryptera dina virtuella datorer för att använda Azure Backup-tjänsten för att säkerhetskopiera och återställa krypterade virtuella datorer, när kryptering är aktiverat med Azure Disk Encryption, med hjälp av Azure Disk Encryption key konfigurationen. Backup-tjänsten har stöd för virtuella datorer som är krypterade med nr KEK eller KEK-konfigurationer. Se [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup kryptering](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* När du krypterar en Linux OS-volym, Observera att en virtuell dator startas för närvarande i slutet av processen. Detta kan göras via portalen, powershell eller CLI.   Om du vill spåra förloppet för kryptering, regelbundna avsökningar det statusmeddelande som returneras av Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  När kryptering är klar kan anger det statusmeddelande som returneras av det här kommandot detta. Till exempel ”ProgressMessage: operativsystemdisk har krypterats, starta om den virtuella datorn” nu kan den virtuella datorn startas om och används.  

* Azure Disk Encryption för Linux kräver datadiskar att ha ett anslutet filsystem i Linux före kryptering

* Rekursivt monterade diskar stöds inte av Azure Disk Encryption för Linux. Om exempelvis målsystemet har monterats en disk/foo /-fältet och sedan en annan på /foo/bar/baz, kryptering av /foo/bar/baz lyckas, men kryptering/foo/fältets misslyckas. 

* Azure Disk Encryption stöds bara på Azure-galleriet stöds avbildningar som uppfyller de tidigare nämnda förutsättningarna. Kunden anpassade avbildningar stöds inte på grund av anpassade partitionsscheman och processen beteenden som kan finnas på de här avbildningarna. Dessutom kan baserat även galleriet bilden Virtuella datorer som ursprungligen uppfyllda förutsättningar men har ändrats efter att skapa en kan vara inkompatibla.  För som är därför den föreslagna proceduren för att kryptera en Linux VM att starta från en ren galleriet bilden, kryptera den virtuella datorn och sedan lägga till anpassad programvara eller data i den virtuella datorn efter behov.  

* Azure Disk Encryption och lokala datavolym - Bek volymen för Windows och/mnt/azure_bek_disk för Linux IaaS-datorer att på ett säkert sätt lagra krypteringsnyckeln. Ta bort eller inte redigera något innehåll i den här disken. Demontera inte disken eftersom viktiga förekomsten kryptering krävs för krypteringsåtgärder på IaaS-VM. Viktigt-fil som ingår i volymen innehåller ytterligare information.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Konfigurera Azure AD-program i Azure Active Directory
När du behöver kryptering är aktiverat på en virtuell dator som körs i Azure, Azure Disk Encryption genererar och skriver dem till ditt nyckelvalv. Hantera krypteringsnycklar i ditt nyckelvalv kräver Azure AD-autentisering.

Skapa ett Azure AD-program för detta ändamål. Du kan hitta detaljerade anvisningar för att registrera ett program i avsnittet ”hämta en identitet för programmet” i det här blogginlägget [Azure Key Vault - steg-för-steg](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Den här artikeln innehåller även ett antal användbara exempel för att installera och konfigurera ditt nyckelvalv. Du kan använda klient-hemlighet-baserad autentisering eller klientautentisering certifikatbaserad Azure AD för autentisering.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Klient-hemlighet-baserad autentisering för Azure AD
Avsnitten som följer kan hjälpa dig att konfigurera en klient-hemlighet-baserad autentisering för Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Skapa ett Azure AD-program med hjälp av Azure PowerShell
Använd följande PowerShell-cmdlet för att skapa ett Azure AD-program:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId är Azure AD-ClientID och $aadClientSecret är den klienthemlighet som du ska använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemlighet på rätt sätt.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Konfigurera Azure AD-klient-ID och hemlighet från Azure portal
Du kan också ställa in din Azure AD-klient-ID och hemlighet med hjälp av Azure-portalen. Om du vill utföra den här uppgiften, gör du följande:

1. Välj **alla tjänster > Azure Active Directory**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-service.png)

2. Välj **appregistreringar > Ny programregistrering**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-app-registration.png)

3. Ange den begärda informationen och skapa programmet:

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-app.png)

4. Välj den nyligen skapade programmet för att visa dess egenskaper, inklusive program-ID.  Om du vill skapa en nyckel för programmet, Välj **Inställningar > nycklar**, lägga till en beskrivning och giltighetstid för nyckeln och på **spara**

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-pw.png)

5. Kopiera det genererade hemliga värdet och skyddar den på rätt sätt.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>Använda ett befintligt program
För att köra följande kommandon för att skaffa och använda den [Azure AD PowerShell-modulen](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Följande kommandon måste köras från en ny PowerShell-fönster. Använd inte Azure PowerShell eller Azure Resource Manager-fönstret för att köra kommandona. Vi rekommenderar den här metoden eftersom dessa cmdlets finns i MSOnline-modulen eller Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Certifikatbaserad autentisering för Azure AD
> [!NOTE]
> Azure AD-certifikatbaserad autentisering stöds för närvarande inte på virtuella Linux-datorer.

I de följande avsnitten visar hur du konfigurerar en certifikatbaserad autentisering för Azure AD.

##### <a name="create-an-azure-ad-application"></a>Skapa ett Azure AD-program
Kör följande PowerShell-cmdletar för att skapa ett Azure AD-program:

> [!NOTE]
> Ersätt följande `yourpassword` sträng med lösenordet säkra och skydda lösenordet.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

När du har slutfört det här steget ladda upp en PFX-fil till ditt nyckelvalv och aktivera den åtkomstprincip som krävs för att distribuera certifikatet till en virtuell dator.

##### <a name="use-an-existing-azure-ad-application"></a>Använda ett befintligt Azure AD-program
Om du konfigurerar certifikatbaserad autentisering för ett befintligt program, använder du PowerShell-cmdlets som visas här. Glöm inte att köra dem från ett nytt PowerShell-fönster.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

När du har slutfört det här steget ladda upp en PFX-fil till ditt nyckelvalv och aktivera åtkomstprincipen som behövs för att distribuera certifikatet till en virtuell dator.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Ladda upp en PFX-fil till ditt nyckelvalv
En detaljerad förklaring av den här processen finns i [The officiella Azure Key Vault-teamets blogg](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Följande PowerShell-cmdletar är allt du behöver för uppgiften. Glöm inte att köra dem från Azure PowerShell-konsolen.

> [!NOTE]
> Ersätt följande `yourpassword` sträng med lösenordet säkra och skydda lösenordet.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Distribuera ett certifikat i nyckelvalvet till en befintlig virtuell dator
När du är klar med att ladda upp PFX kan du distribuera ett certifikat i nyckelvalvet till en befintlig virtuell dator med följande:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Konfigurera nyckelvalvets åtkomstprincip för Azure AD-programmet
Azure AD-program behöver behörighet att komma åt nycklar eller hemligheter i valvet. Använd den [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) cmdlet för att bevilja behörighet för programmet med klient-ID (som skapades när programmet registrerades) som den _– ServicePrincipalName_ parametervärde. Mer information finns i bloggposten [Azure Key Vault - steg-för-steg](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Här är ett exempel på hur du utför den här uppgiften via PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption måste du konfigurera följande principer för åtkomst till ditt Azure AD-klientprogram: _WrapKey_ och _ange_ behörigheter.

## <a name="terminology"></a>Terminologi
För att förstå några av de vanliga termer som används av den här tekniken kan använda tabellen nedan terminologi:

| Terminologi | Definition |
| --- | --- |
| Azure AD | Azure AD är [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). En Azure AD-konto är en förutsättning för att autentisera, lagra och hämta hemligheter från key vault. |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som baseras på Federal Information Processing Standards FIPS-verifierade HSM: er, vilket hjälper att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| ARM | Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en bransch identifieras Windows volym krypteringsteknik som används för att aktivera diskkryptering på Windows virtuella IaaS-datorer. |
| BEK | BitLocker-nycklar för kryptering används för att kryptera startvolymen för Operativsystemet och datavolymer. BitLocker-nycklar skyddas i ett nyckelvalv som hemligheter. |
| CLI | Se [kommandoradsgränssnittet](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) är undersystemet för Linux-baserade och transparent diskkryptering som används för att aktivera diskkryptering på virtuella Linux IaaS-datorer. |
| KEK | Nyckelkrypteringsnyckel är en asymmetrisk nyckel (RSA 2048) som du kan använda för att skydda eller packa in hemligheten. Du kan ange en säkerhets-och maskinvara moduler (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| PS-cmdletar | Se [Azure PowerShell-cmdlets](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Installera och konfigurera ditt nyckelvalv för Azure Disk Encryption
Azure Disk Encryption hjälper att skydda diskkryptering nycklar och hemligheter i nyckelvalvet. Slutför stegen i var och en av följande avsnitt om du vill konfigurera ditt nyckelvalv för Azure Disk Encryption.

#### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Om du vill skapa ett nyckelvalv, använder du något av följande alternativ:

* [”101-Key-Vault-skapa” Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Azure PowerShell-cmdlets nyckelvalv](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Så här [säkra ditt nyckelvalv](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Om du redan har konfigurerat ett nyckelvalv för din prenumeration kan du gå vidare till nästa avsnitt.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Konfigurera en nyckelkrypteringsnyckel (valfritt)
Om du vill använda en KEK för ett extra lager av säkerhet för BitLocker-nycklar för kryptering, lägger du till en KEK till ditt nyckelvalv. Använd den [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) cmdlet för att skapa en nyckelkrypteringsnyckel i nyckelvalvet. Du kan också importera en KEK från din lokala nyckelhantering HSM. Mer information finns i [dokumentation om Key Vault](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Du kan lägga till KEK genom att gå till Azure Resource Manager eller genom att använda key vault-gränssnittet.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Ange behörigheter för nyckelvalvet
Azure-plattformen behöver åtkomst till krypteringsnycklar och hemligheter i ditt nyckelvalv och gör dem tillgängliga för den virtuella datorn för start och dekryptera volymerna. Om du vill tilldela behörigheter till Azure-plattformen, ange den **EnabledForDiskEncryption** -egenskapen i nyckeln valv med hjälp av PowerShell-cmdleten nyckelvalv:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Du kan också ange den **EnabledForDiskEncryption** egenskapen genom att besöka den [Azure Resource Explorer](https://resources.azure.com).

Såsom nämnts tidigare måste du ställa in den **EnabledForDiskEncryption** -egenskapen i ditt nyckelvalv. I annat fall misslyckas distributionen.

Du kan ställa in åtkomstprinciper för ditt Azure AD-program från key vault-gränssnitt som visas här:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

På den **avancerade åtkomstprinciper** fliken, se till att ditt nyckelvalv är aktiverat för Azure Disk Encryption:

![Azure-nyckelvalv](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Distributionsscenarier för disk-kryptering och användarupplevelser
Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Aktivera kryptering på den nya virtuella IaaS-datorer som har skapats från Marketplace
Du kan aktivera diskkryptering på nya virtuella IaaS Windows-dator från Marketplace i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Klicka på mallen Azure Snabbstart **distribuera till Azure**, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, juridiska villkor och avtal och klickar sedan på **skapa** att aktivera kryptering på en ny IaaS-VM.

> [!NOTE]
> Den här mallen skapar en ny krypterade Windows virtuell dator som använder Windows Server 2012 galleriet bilden.

Du kan aktivera diskkryptering på en ny IaaS Red Hat Linux 7.2 VM med en 200 GB RAID-0-matris med den här [Resource Manager-mall](https://aka.ms/fde-rhel). När du distribuerar mallen kan kontrollera status för VM-kryptering med hjälp av den `Get-AzureRmVmDiskEncryptionStatus` cmdlet, enligt beskrivningen i [kryptera operativsystemenhet på en som kör Linux VM](#encrypting-os-drive-on-a-running-linux-vm). När datorn returnerar statusvärdet _VMRestartPending_, starta om den virtuella datorn.

I följande tabell visas Resource Manager-mallens parametrar för nya virtuella datorer från Marketplace-scenario med hjälp av Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| adminUserName | Administratörsanvändarnamn för den virtuella datorn. |
| adminPassword | Administratörslösenord för den virtuella datorn. |
| newStorageAccountName | Namnet på lagringskontot för att lagra OS- och virtuella hårddiskar. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G-serien. |
| virtualNetworkName | Namnet på det virtuella nätverk som VM NIC ska tillhöra. |
| subnetName | Namnet på undernätet i det virtuella nätverket som VM NIC ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultURL | URL för nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln (valfritt). |
| keyVaultResourceGroup | Resursgrupp för nyckelvalvet. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |

> [!NOTE]
> _KeyEncryptionKeyURL_ är en valfri parameter. Du kan använda din egen KEK att ytterligare skydda krypteringsnyckeln för tjänstdata (hemlig lösenfras) i ditt nyckelvalv.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Aktivera kryptering på den nya virtuella IaaS-datorer som skapas från kund-krypterad VHD och krypteringsnycklar
Du kan aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs mer detaljerat Resource Manager-mall och CLI-kommandon.

Följ instruktionerna från någon av dessa avsnitt för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](#preparing-a-pre-encrypted-windows-vhd)
* [Förbereda en förkrypterade Linux-VHD](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Med hjälp av Resource Manager-mall
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Klicka på mallen Azure Snabbstart **distribuera till Azure**, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, juridiska villkor och avtal och klickar sedan på **skapa** att aktivera kryptering på den nya IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för en krypterad virtuell Hårddisk:

| Parameter | Beskrivning |
| --- | --- |
| newStorageAccountName | Namnet på lagringskontot för att lagra den krypterade OS-VHD. Det här lagringskontot ska redan har skapats i samma resursgrupp och samma plats som den virtuella datorn. |
| osVhdUri | URI för OS-VHD: N från storage-kontot. |
| osType | Produkttyp av operativsystem (Windows/Linux). |
| virtualNetworkName | Namnet på det virtuella nätverk som VM NIC ska tillhöra. Namnet bör redan har skapats i samma resursgrupp och samma plats som den virtuella datorn. |
| subnetName | Namnet på undernätet i det virtuella nätverket som VM NIC ska tillhöra. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G-serien. |
| keyVaultResourceID | ResourceID som identifierar resursen nyckelvalv i Azure Resource Manager. Du kan hämta den med hjälp av PowerShell-cmdleten `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | URL för disk-krypteringsnyckeln som har ställts in i nyckelvalvet. |
| keyVaultKekUrl | URL för nyckelkrypteringsnyckel för att kryptera den genererade diskkryptering nyckeln. |
| vmName | Namnet på den virtuella IaaS-datorer. |

#### <a name="using-powershell-cmdlets"></a>Med hjälp av PowerShell-cmdletar
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Med hjälp av CLI-kommandon
Om du vill aktivera diskkryptering för det här scenariot med hjälp av CLI-kommandon, gör du följande:

1. Ställa in åtkomstprinciper i ditt nyckelvalv:

   * Ange den **EnabledForDiskEncryption** flaggan:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ange behörigheter till Azure AD-program att skriva hemligheter till ditt nyckelvalv:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Om du vill aktivera kryptering på en virtuell dator som körs eller befintliga, skriver du:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Hämta krypteringsstatus:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Om du vill möjliggöra kryptering på en ny virtuell dator från en krypterad virtuell Hårddisk måste du använda följande parametrar med den `azure vm create` kommando:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Aktivera kryptering på befintliga eller köra IaaS Windows-datorer i Azure
Du kan aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs i detalj hur du aktiverar det med hjälp av Resource Manager-mall och CLI-kommandon.

#### <a name="using-the-resource-manager-template"></a>Med hjälp av Resource Manager-mall
Du kan aktivera diskkryptering på befintliga eller kör IaaS Windows-datorer i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Klicka på mallen Azure Snabbstart **distribuera till Azure**, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, juridiska villkor och avtal och klickar sedan på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller som kör virtuella datorer som använder en Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| volumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. |
| sequenceVersion | Sekvens-versionen av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |

> [!NOTE]
> _KeyEncryptionKeyURL_ är en valfri parameter. Du kan använda din egen KEK för att ytterligare skydda krypteringsnyckeln för tjänstdata (BitLocker-kryptering hemlighet) i nyckelvalvet.

#### <a name="using-powershell-cmdlets"></a>Med hjälp av PowerShell-cmdletar
Information om hur du aktiverar kryptering med Azure Disk Encryption med PowerShell-cmdlets finns i blogginläggen [utforska Azure Disk Encryption med Azure PowerShell – del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och [utforska Azure Disk Encryption med Azure PowerShell – del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Med hjälp av CLI-kommandon
Om du vill möjliggöra kryptering på befintliga eller köra IaaS Windows-datorer i Azure med hjälp av CLI-kommandon måste du göra följande:

1. Att ställa in åtkomstprinciper i key vault:
   * Ange den **EnabledForDiskEncryption** flaggan:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ange behörigheter till Azure AD-program att skriva hemligheter till ditt nyckelvalv:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Aktivera kryptering på en befintlig eller aktiv virtuell dator:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Hämta krypteringsstatus:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Om du vill möjliggöra kryptering på en ny virtuell dator från en krypterad virtuell Hårddisk måste du använda följande parametrar med den `azure vm create` kommando:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Aktivera kryptering på en befintlig eller körs IaaS Linux-dator i Azure
Du kan aktivera diskkryptering på en befintlig eller körs IaaS Linux-dator i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klicka på **distribuera till Azure** på mallen Azure-Snabbstart, anger du konfigurationen av kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, juridiska villkor och avtal och klickar sedan på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller som kör virtuella datorer som använder en Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i key vault. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter till ditt nyckelvalv. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| volumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden som stöds är _OS_ eller _alla_ (Se stöd för Linux-distributioner och deras versioner för Operativsystemet och datadiskarna i prerequisiteis avsnittet ovan). |
| sequenceVersion | Sekvens-versionen av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärd utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |
| Lösenfras | Ange ett starkt lösenord som krypteringsnyckeln för tjänstdata. |

> [!NOTE]
> _KeyEncryptionKeyURL_ är en valfri parameter. Du kan använda din egen KEK att ytterligare skydda krypteringsnyckeln för tjänstdata (hemlig lösenfras) i ditt nyckelvalv.

#### <a name="cli-commands"></a>CLI-kommandon
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk genom att installera och använda den [CLI-kommando](../cli-install-nodejs.md). Om du vill möjliggöra kryptering på befintliga eller kör IaaS virtuella Linux-datorer i Azure med hjälp av CLI-kommandon måste du göra följande:

1. Ställa in åtkomstprinciper i key vault:

 * Ange den **EnabledForDiskEncryption** flaggan:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Ange behörigheter till Azure AD-program att skriva hemligheter till ditt nyckelvalv:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Aktivera kryptering på en befintlig eller aktiv virtuell dator:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Hämta krypteringsstatus:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Om du vill möjliggöra kryptering på en ny virtuell dator från en krypterad virtuell Hårddisk måste du använda följande parametrar med den `azure vm create` kommando:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Hämta krypteringsstatus för en krypterad IaaS VM
Du kan hämta krypteringsstatus med hjälp av Azure Resource Manager [PowerShell-cmdletar](/powershell/azure/overview), eller CLI-kommandon. I följande avsnitt beskrivs hur du använder Azure Portal och CLI-kommandon för att hämta krypteringsstatus.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Hämta krypteringsstatus för en krypterad virtuell Windows-dator med hjälp av Azure Resource Manager
Du kan få krypteringsstatus för IaaS VM från Azure Resource Manager genom att göra följande:

1. Logga in på den [Azure-portalen](https://portal.azure.com/), och klicka sedan på **virtuella datorer** i det vänstra fönstret för att se en översikt över de virtuella datorerna i din prenumeration. Du kan filtrera vyn virtuella datorer genom att välja prenumerationens namn i den **prenumeration** listrutan.

2. Överst på den **virtuella datorer** klickar du på **kolumner**.

3. På den **Välj kolumn** bladet väljer **diskkryptering**, och klicka sedan på **uppdatering**. Du bör se kolumnen diskkryptering som visar krypteringsstatus _aktiverad_ eller _inte aktiverat_ för varje virtuell dator, enligt följande bild:

 ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Hämta krypteringsstatus för en krypterad (Windows/Linux) IaaS VM med hjälp av PowerShell-cmdlet diskkryptering
Du kan hämta krypteringsstatus för IaaS VM från PowerShell-cmdlet diskkryptering `Get-AzureRmVMDiskEncryptionStatus`. Om du vill ha krypteringsinställningar för den virtuella datorn, anger du följande:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Du kan granska resultatet av _Get-AzureRmVMDiskEncryptionStatus_ nyckeln URL: er för kryptering.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Värdet OSVolumeEncrypted och DataVolumesEncrypted inställningar är inställda på _krypterad_, som visar att båda volymerna är krypterade med Azure Disk Encryption. Information om hur du aktiverar kryptering med Azure Disk Encryption med PowerShell-cmdlets finns i blogginläggen [utforska Azure Disk Encryption med Azure PowerShell – del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och [utforska Azure Disk Encryption med Azure PowerShell – del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> På virtuella Linux-datorer, det tar tre och fyra minuter den `Get-AzureRmVMDiskEncryptionStatus` cmdlet för att rapportera krypteringsstatus.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Hämta krypteringsstatus för IaaS VM från CLI-kommando diskkryptering
Du kan hämta krypteringsstatus för IaaS VM med hjälp av CLI-kommando för diskkryptering `azure vm show-disk-encryption-status`. Om du vill ha krypteringsinställningar för den virtuella datorn, anger du Azure CLI-sessionen:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Inaktivera kryptering på som kör Windows virtuella IaaS-datorer
Du kan inaktivera kryptering på en som kör Windows eller Linux virtuella IaaS-datorer via Azure Disk Encryption Resource Manager-mall eller PowerShell-cmdletar och anger önskad konfiguration för dekryptering.

##### <a name="windows-vm"></a>Virtuell Windows-dator
Inaktivera kryptering steg inaktiverar kryptering av Operativsystemet, datavolymen eller båda körs Windows virtuella IaaS-datorer. Du kan inte inaktivera operativsystemvolymen och lämna den datavolym som krypterad. När inaktivera kryptering steg utförs den klassiska distributionsmodellen uppdaterar modellen för VM-service och Windows virtuella IaaS-datorer har markerats dekrypterade. Innehållet i den virtuella datorn är inte längre krypterade i vila. Dekrypteringen tar inte bort nyckelvalvet och kryptering nyckelmaterial (BitLocker-krypteringsnycklar för Windows och lösenfras för Linux).

##### <a name="linux-vm"></a>Virtuell Linux-dator
Inaktivera kryptering steg inaktiverar kryptering av datavolymen som kör Linux virtuella IaaS-datorer. Det här steget fungerar bara om OS-disken inte är krypterad.

> [!NOTE]
> Inaktivera kryptering på OS-disken är inte tillåtet på virtuella Linux-datorer.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Inaktivera kryptering på en befintlig eller körs IaaS-VM
Du kan inaktivera diskkryptering på kör Windows virtuella IaaS-datorer med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Klicka på mallen Azure Snabbstart **distribuera till Azure**, ange dekryptering konfigurationen på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumeration, resursgrupp, resursgruppens plats, juridiska villkor och avtal och klickar sedan på **skapa** att aktivera kryptering på en ny IaaS-VM.

För virtuella Linux-datorer kan du inaktivera kryptering med hjälp av den [inaktivera kryptering på en som kör Linux VM](https://aka.ms/decrypt-linuxvm) mall.

I följande tabell visas Resource Manager-mallens parametrar för att inaktivera kryptering på en pågående IaaS-VM:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på.
| volumeType | Typ av volym som en dekrypteringsåtgärd utförs på. Giltiga värden är _OS_, _Data_, och _alla_. Du kan inte inaktivera kryptering på den som kör Windows IaaS VM OS/startvolymen utan att inaktivera kryptering på den _Data_ volym. Observera också att inaktivera kryptering på OS-disken inte är tillåten på virtuella Linux-datorer. |
| sequenceVersion | Sekvens-versionen av BitLocker-åtgärden. Öka det här versionsnumret varje gång en disk dekrypteringsåtgärd genomförs på samma virtuella dator. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Inaktivera kryptering på en befintlig eller körs IaaS-VM
Om du vill inaktivera kryptering på en befintlig eller körs IaaS-VM med hjälp av PowerShell-cmdleten, se [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Denna cmdlet har stöd för både Windows och Linux-datorer. Om du vill inaktivera kryptering, installerar ett tillägg på den virtuella datorn. Om den _namn_ parametern inte anges ett tillägg med standardnamnet _AzureDiskEncryption för Windows virtuella datorer_ har skapats.

Tillägget azurediskencryptionforlinux har lagts används på virtuella Linux-datorer.

> [!NOTE]
> Denna cmdlet startar om den virtuella datorn.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Aktivera kryptering på förkrypterade IaaS VM med Azure Managed Disk
Använda Azure Managed Disk ARM-mall för att skapa en krypterad virtuell dator från en förkrypterade virtuell Hårddisk med hjälp av ARM-mallen finns i   
[Skapa en ny krypterade hanterad disk från en förkrypterade VHD-/ lagringsblob] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Aktivera kryptering på en ny Linux IaaS-dator med Azure Managed Disk
Använda Azure Managed Disk ARM-mall för att skapa en ny krypterade Linux IaaS-VM med hjälp av ARM-mallen finns i   
[Distribution av RHEL 7.2 med fullständig diskkryptering] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Aktivera kryptering på en ny Windows virtuella IaaS-datorer med Azure Managed Disk
 Använda Azure Managed Disk ARM-mall för att skapa en ny krypterade Linux IaaS-VM med hjälp av ARM-mallen finns i   
 [Skapa en ny krypterade Windows IaaS Managed Disk virtuell dator från galleriet bilden] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption.  En ögonblicksbild av den hantera disken kan tas från portalen eller Azure Backup kan användas.  Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen.  Cmdleten Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs.  Det här kommandot kan utföras mot hanterad disk baserat VM förrän en säkerhetskopia har gjorts och den här parametern har angetts.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Uppdatera krypteringsinställningarna för en befintlig krypterade icke-premium virtuell dator
  Använd befintliga Azure disk encryption stöds gränssnitt för att köra datorer [PS-cmdletar, CLI eller ARM-mallar] för att uppdatera krypteringsinställningarna som AAD-klient-ID/hemligheten krypteringsnyckel [KEK] BitLocker-krypteringsnyckeln för Windows-VM eller en lösenfras för Linux VM osv. Krypteringsinställningen uppdatering har stöd för både premium och virtuella datorer i icke-premium storage.

## <a name="appendix"></a>Bilaga
### <a name="connect-to-your-subscription"></a>Ansluta till din prenumeration
Innan du fortsätter måste du granska den *krav* i den här artikeln. När du har kontrollerat att alla krav har uppfyllts kan du ansluta till din prenumeration genom att göra följande:

1. Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

    `Connect-AzureRmAccount`

2. Om du har flera prenumerationer och vill ange jag ska använda, skriver du följande för att visa prenumerationerna för ditt konto:

    `Get-AzureRmSubscription`

3. Om du vill ange den prenumeration som du vill använda, skriver du:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Om du vill kontrollera att den prenumeration som har konfigurerats är korrekt, skriver du:

    `Get-AzureRmSubscription`

5. För att bekräfta Azure Disk Encryption-cmdletar har installerats, skriver du:

    `Get-command *diskencryption*`

6. Följande utdata bekräftar Azure Disk Encryption PowerShell-installationen:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Förbered en förkrypterade Windows virtuell Hårddisk
Avsnitten som följer är nödvändiga för att förbereda en förkrypterade Windows virtuell Hårddisk för distribution som en krypterad virtuell Hårddisk i Azure IaaS. Använd informationen för att förbereda och starta en ny Windows VM (VHD) på Azure Site Recovery eller Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Uppdatera grupprincipen för att tillåta utan TPM för OS-skydd
Konfigurera grupprincipinställningen BitLocker **BitLocker-diskkryptering**, som du hittar **lokal datorprincip** > **Datorkonfiguration**  >  **Administrationsmallar** > **Windows-komponenter**. Ändra inställningen till **operativsystemsenheter** > **kräver ytterligare autentisering vid start** > **Tillåt BitLocker utan en kompatibel TPM**, enligt följande bild:

![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Installera komponenter för BitLocker-funktion
För Windows Server 2012 och senare, använder du följande kommando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

För Windows Server 2008 R2, använder du följande kommando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Förbereda operativsystemvolymen för BitLocker med hjälp av `bdehdcfg`
Komprimera OS-partition och förbereda datorn för BitLocker genom att köra följande kommando:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Skydda systemvolymen med hjälp av BitLocker
Använd den [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) kommando för att aktivera kryptering på startvolymen med hjälp av en extern nyckelskydd. Placera även den externa nyckeln (.bek-fil) på extern enhet eller volym. Kryptering är aktiverat på system-/ startvolymen efter nästa omstart.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Förbered den virtuella datorn med en separat data/resurs VHD för att hämta den externa nyckeln med hjälp av BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Kryptera en OS-enhet på en som kör Linux VM

##### <a name="prerequisites-for-os-disk-encryption"></a>Krav för OS-diskkryptering

* Den virtuella datorn måste använda en distributionsplats som är kompatibel med OS-diskkryptering som anges i den [Azure Disk Encryption vanliga frågor och svar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support) 
* Den virtuella datorn måste skapas från Marketplace-avbildning i Azure Resource Manager.
* Azure virtuell dator med minst 4 GB RAM (rekommenderas är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Om du vill inaktivera SELinux, se ”4.4.2. Inaktivera SELinux ”i den [SELinux användar- och Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* Starta om den virtuella datorn på minst en gång när du har inaktiverat SELinux.

##### <a name="steps"></a>Steg
1. Skapa en virtuell dator med någon av de distributioner som angavs tidigare.

 OS-diskkryptering stöds för 7.2 CentOS, via en särskild avbildning. Om du vill använda den här bilden anger du ”7.2n” som SKU: N när du skapar den virtuella datorn:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurera den virtuella datorn efter dina behov. Om du kommer att kryptera alla (OS + data) enheter finns i dataenheter måste vara angivna och monteras från/etc/fstab.

 > [!NOTE]
 > Använd UUID =... Ange dataenheter för i/etc/fstab istället för att ange blockera enhetens namn (till exempel/dev/sdb1). Ordningen på enheter ändras på den virtuella datorn under krypteringen. Om den virtuella datorn är beroende av en viss ordning av blockenheterna, misslyckas den att montera dem efter kryptering.

3. Logga ut från SSH-sessioner.

4. För att kryptera Operativsystemet, ange volumeType som **alla** eller **OS** när du [Aktivera kryptering](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Alla Användarutrymmet processer som inte körs som `systemd` tjänster ska avslutas med en `SIGKILL`. Starta om den virtuella datorn. När du aktiverar OS-diskkryptering på en aktiv virtuell dator kan du planera på stilleståndstid på virtuella datorer.

5. Övervaka förloppet för kryptering med jämna mellanrum med hjälp av anvisningarna i den [nästa avsnitt](#monitoring-os-encryption-progress).

6. När Get-AzureRmVmDiskEncryptionStatus visar ”VMRestartPending”, startar du om den virtuella datorn genom att logga in till den eller med hjälp av portalen, PowerShell eller CLI.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Innan du startar om rekommenderar vi att du sparar [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) för den virtuella datorn.

#### <a name="monitoring-os-encryption-progress"></a>Övervaka förloppet för OS-kryptering
Du kan övervaka förloppet för OS-kryptering på tre sätt:

* Använd den `Get-AzureRmVmDiskEncryptionStatus` cmdlet och inspektera fältet ProgressMessage:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 När den virtuella datorn når ”OS diskkryptering igång”, det tar cirka 40 till 50 minuter på en Premium-lagring säkerhetskopieras VM.

 Grund av [utfärda #388](https://github.com/Azure/WALinuxAgent/issues/388) i WALinuxAgent, `OsVolumeEncrypted` och `DataVolumesEncrypted` visas som `Unknown` i vissa distributioner. Med WALinuxAgent version 2.1.5 och senare, det här problemet löses automatiskt. Om du ser `Unknown` utdata och du kan kontrollera status för diskkryptering med hjälp av Azure Resource Explorer.

 Gå till [Azure Resource Explorer](https://resources.azure.com/), och expandera sedan den här hierarkin i panelen för val av vänster:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 Rulla ned för att se krypteringsstatus för dina enheter i InstanceView.

 ![Instansvy för virtuell dator](./media/azure-security-disk-encryption/vm-instanceview.png)

* Titta på [startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från tillägget ADE ska föregås `[AzureDiskEncryption]`.

* Logga in på den virtuella datorn via SSH och hämta tilläggets logg från:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Vi rekommenderar att du inte loggar in till den virtuella datorn när OS-kryptering pågår. Kopiera loggarna bara när de två metoderna har misslyckats.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Förbereda en förkrypterade Linux-VHD
##### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurera kryptering under installationen av distributionsplatsen genom att göra följande:

1. Välj **konfigurera krypterade volymer** när du partitionera diskarna.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenheten som inte får vara krypterade. Kryptera din rotenhet.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du överför till nyckelvalvet.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slut partitionering.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av [instruktionerna](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

Konfigurera krypteringen ska fungera med Azure genom att göra följande:

1. Skapa en fil under /usr/local/sbin/azure_crypt_key.sh, med innehållet i skriptet nedan. Uppmärksam på KeyFileName, eftersom det är namnet på lösenfras-filen som används av Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Ändra crypt-konfigurationen i */etc/crypttab*. Det bör se ut så här:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Om du redigerar *azure_crypt_key.sh* i Windows och du har kopierat det till Linux, kör `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Lägg till körrättigheter i skriptet:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Redigera */etc/initramfs-tools/modules* genom att lägga till rader:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Kör `update-initramfs -u -k all` att uppdatera initramfs att göra den `keyscript` träder i kraft.

7. Nu kan du avetablera den virtuella datorn.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och [överföra en virtuell Hårddisk](#upload-encrypted-vhd-to-an-azure-storage-account) till Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. När du partitionera diskarna väljer **kryptera volymen grupp**, och sedan ange ett lösenord. Detta är det lösenord som du överför till ditt nyckelvalv.

 ![Konfigurera openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med ditt lösenord.

 ![Konfigurera openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Förbereda den virtuella datorn för att ladda upp till Azure genom att följa instruktionerna i [Förbered en SLES- eller openSUSE-dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:
1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Lägg till följande rad i början av filen /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```
    DRACUT_SYSTEMD=0
 ```
Och ändra alla förekomster av:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
till:
```
    if [ 1 ]; then
```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och lägger till dem i ”# öppna LUKS enhet”:

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Kör `/usr/sbin/dracut -f -v` att uppdatera initrd.

6. Nu kan du avetablera den virtuella datorn och [överföra en virtuell Hårddisk](#upload-encrypted-vhd-to-an-azure-storage-account) till Azure.

##### <a name="centos-7"></a>CentOS 7
För att konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. Välj **kryptera Mina data** när du partitionera diskar.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Se till att **Encrypt** har valts för rotpartitionen.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösenfras. Det här är det lösenord som du överför till ditt nyckelvalv.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och ange en lösenfras, använder du den lösenfras som du angav i steg 3.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Förbereda den virtuella datorn för att ladda upp till Azure med hjälp av anvisningarna i ”CentOS 7.0 +” [Förbered en CentOS-baserad virtuell dator för Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Kör inte det sista steget (avetablera den virtuella datorn) ännu.

6. Nu kan du avetablera den virtuella datorn och [överföra en virtuell Hårddisk](#upload-encrypted-vhd-to-an-azure-storage-account) till Azure.

För att konfigurera krypteringen ska fungera med Azure, gör du följande:

1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera ut följande rader i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Lägg till följande rad i början av filen /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```
    DRACUT_SYSTEMD=0
```
Och ändra alla förekomster av:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
till
```
    if [ 1 ]; then
```
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och lägga till detta efter ”# öppna LUKS enhet”:
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Kör den ”/ usr/sbin/dracut - f - v” att uppdatera initrd.

![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Ladda upp krypterade VHD till ett Azure storage-konto
När BitLocker-kryptering eller DM-Crypt kryptering har aktiverats, måste den lokala krypterade virtuella Hårddisken som ska överföras till ditt lagringskonto.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Ladda upp diskkryptering hemligheten för den förkrypterade virtuella datorn till ditt nyckelvalv
Diskkryptering hemligheten som du fick tidigare måste ska laddas upp som en hemlighet i nyckelvalvet. Nyckelvalvet måste ha diskkryptering och behörigheter som har aktiverats för din Azure AD-klient.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Diskkrypteringshemlighet som inte är krypterade med en KEK
Om du vill konfigurera hemlighet i nyckelvalvet, använda [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Om du har en Windows-dator kan filen bek kodas som en base64-sträng och sedan överförs till ditt nyckelvalv med hjälp av den `Set-AzureKeyVaultSecret` cmdlet. För Linux, lösenfrasen kodas som en base64-sträng och sedan överförs till nyckelvalvet. Kontrollera dessutom att följande taggar är inställda när du skapar hemligheten i nyckelvalvet.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Använd den `$secretUrl` i nästa steg för [koppla OS-disk utan att använda KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Diskkrypteringshemlighet som krypterats med en KEK
Innan du laddar upp hemligheten till nyckelvalvet kryptera du om du vill det med hjälp av en nyckelkrypteringsnyckel. Använd radbyte [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) att kryptera hemligheten med den viktiga krypteringsnyckeln först. Utdata från åtgärden radbyte är en base64-URL-kodad sträng som du kan sedan överföra som en hemlighet med hjälp av den [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) cmdlet.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för [koppla OS-disken med hjälp av KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du kopplar en OS-disk
#### <a name="without-using-a-kek"></a>Utan att använda en KEK
När du bifogar OS-disken, måste du skicka `$secretUrl`. URL: en har genererats i avsnittet ”diskkryptering hemlighet som inte är krypterade med en KEK”.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Med hjälp av en KEK
När du ansluter OS-disken, skicka `$KeyEncryptionKey` och `$secretUrl`. URL: en har genererats i avsnittet ”diskkryptering hemlighet som inte är krypterade med en KEK”.

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="for-more-information"></a>Mer information
[Utforska Azure Disk Encryption med Azure PowerShell – del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Utforska Azure Disk Encryption med Azure PowerShell – del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
