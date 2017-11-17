---
title: "Azure Disk Encryption för Windows och Linux-IaaS-VM | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över Microsoft Azure Disk Encryption för Windows och Linux virtuella IaaS-datorer."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: kakhan
ms.openlocfilehash: 15ed35ab3a082db3376890992be3a29b6e042a2f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption för Windows och Linux-IaaS-VM
Microsoft Azure värnar starkt din datasekretess, data suveränitet och aktiverar du att styra dina Azure värdbaserade data via ett intervall med avancerade tekniker för att kryptera, styra och hantera krypteringsnycklar kontroll & granska åtkomsten till data. Det ger Azure-kunder möjlighet att välja den lösning som bäst uppfyller deras behov av företag. I det här dokumentet, vi innehåller en introduktion till en ny tekniklösning ”Azure Disk Encryption för Windows och Linux IaaS VMS” om du vill skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden. Dokumentet ger detaljerad information om hur du använder Azure disk encryption-funktioner inklusive scenarierna som stöds och användaren inträffar.

> [!NOTE]
> Vissa rekommendationerna kan öka data, nätverk och beräkning Resursanvändning, vilket resulterar i ytterligare kostnader för licens eller prenumeration.

## <a name="overview"></a>Översikt
Azure Disk Encryption är en ny funktion som hjälper dig att kryptera din Windows- och Linux IaaS virtuella diskar. Azure Disk Encryption utnyttjar branschstandarden [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i nyckelvalvet-prenumeration. Lösningen betyder också att krypteras alla data på virtuella diskar i vila i ditt Azure storage.

Azure disk encryption för Windows och Linux IaaS-VM är nu i **allmän tillgänglighet** i alla Azure offentliga och AzureGov regioner för Standard virtuella datorer och virtuella datorer med premium-lagring.

### <a name="encryption-scenarios"></a>Scenarier för kryptering
Azure Disk Encryption-lösningen stöder följande kundscenarier:

* Aktivera kryptering på den nya virtuella IaaS-datorer skapas från förkrypterade VHD- och krypteringsnycklar
* Aktivera kryptering på den nya virtuella IaaS-datorer skapas från stöds avbildningar i Azure-galleriet
* Aktivera kryptering på befintliga virtuella IaaS-datorer körs i Azure
* Inaktivera kryptering på Windows IaaS-VM
* Inaktivera kryptering på dataenheter för Linux virtuella IaaS-datorer
* Aktivera kryptering för hanterade diskar virtuella datorer
* Uppdatera krypteringsinställningarna för en befintlig krypterade premium och icke-premium storage VM
* Säkerhetskopiering och återställning av krypterade virtuella datorer

Lösningen stöder följande scenarion för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault
* Standardnivån VMs: [A, D, DS, G, GS, F och så vidare serien IaaS-VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Aktivera kryptering på Windows- och Linux virtuella IaaS-datorer och hanterade diskar virtuella datorer från stöds avbildningar i Azure-galleriet
* Inaktivera kryptering på Operativsystemet och enheter för Windows IaaS-VM och hanterade diskar virtuella datorer
* Inaktivera kryptering på dataenheter för Linux virtuella IaaS-datorer och hanterade diskar virtuella datorer
* Aktivera kryptering på IaaS virtuella datorer som kör Windows klient-OS
* Aktivera kryptering på volymer med monteringssökvägar
* Aktivera kryptering på den virtuella Linux-datorer konfigurerade med disk striping (RAID) med hjälp av mdadm
* Aktivera kryptering på den virtuella Linux-datorer med hjälp av LVM för datadiskar
* Aktivera kryptering på Linux LVM 7.3 för Operativsystemet och datadiskarna 
* Aktivera kryptering på virtuella Windows-datorer konfigurerade med lagringsutrymmen
* Uppdatera krypteringsinställningarna för en befintlig krypterade premium och icke-premium storage VM
* Säkerhetskopiering och återställning av krypterade virtuella datorer, för både nr KEK och KEK-scenarier (KEK - krypteringsnyckeln)
* Alla offentliga Azure och AzureGov regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och teknik:

* Grundnivån IaaS-VM
* Om du inaktiverar kryptering på en OS-enhet för Linux virtuella IaaS-datorer
* Om du inaktiverar kryptering på en dataenhet om OS-enheten är krypterad för Linux virtuella Iaas-datorer
* Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator
* Aktivera kryptering på Windows och Linux-IaaS-VM kunden anpassade avbildningar inte stöds.
* Integrering med din lokala nyckelhanteringstjänst
* Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och virtuella Windows-datorer som är konfigurerade med programvarubaserad RAID-system

### <a name="encryption-features"></a>Krypteringsfunktioner
När du aktiverar och distribuera Azure Disk Encryption för Azure IaaS-VM är följande funktioner aktiverade beroende på konfigurationen som:

* Kryptering av systemvolymen att skydda startvolymen i vila i lagringsutrymmet
* Kryptering av datavolymer att skydda datavolymer i vila i lagringsutrymmet
* Om du inaktiverar kryptering på Operativsystemet och enheter för Windows IaaS-VM
* Om du inaktiverar kryptering för data-enheter för Linux virtuella IaaS-datorer (endast om OS är inte krypterad enhet)
* Skydda krypteringsnycklar och hemligheter i nyckelvalvet-prenumeration
* Rapportering krypteringsstatus av krypterade IaaS-VM
* Borttagning av disk-kryptering konfigurationsinställningar från virtuell IaaS-dator
* Säkerhetskopiering och återställning av krypterade virtuella datorer med hjälp av Azure Backup-tjänsten

Azure Disk Encryption för IaaS VMS för Windows och Linux-lösningen innehåller:

* Diskkryptering tillägget för Windows.
* Tillägget för Linux-diskkryptering.
* Disk-kryptering PowerShell-cmdlets.
* Disk-kryptering Azure-kommandoradsgränssnittet (CLI) cmdlets.
* Disk-kryptering Azure Resource Manager-mallar.

Lösning för Azure Disk Encryption stöds på virtuella IaaS-datorer som kör Windows eller Linux OS. Mer information om operativsystem som stöds finns i avsnittet ”förutsättningar”.

> [!NOTE]
> Det finns inga extra kostnad för att kryptera Virtuella diskar med Azure Disk Encryption.

### <a name="value-proposition"></a>Förslagsvärde
När du använder Azure Disk Encryption-hanteringslösningen kan du uppfylla följande affärsbehov:

* IaaS-VM är skyddade i vila, eftersom du kan använda branschstandardiserad krypteringsteknik för att adressera organisatoriska krav för säkerhet och efterlevnad.
* IaaS-VM start under kund-kontrollerade nycklar och principer, och du kan granska deras användning i nyckelvalvet.

### <a name="encryption-workflow"></a>Arbetsflöde för kryptering
Om du vill aktivera kryptering för Windows och Linux virtuella datorer, gör du följande:

1. Välj ett scenario för kryptering bland föregående kryptering scenarier.
2. Välja att aktivera disk kryptering via Azure Disk Encryption Resource Manager-mall, PowerShell-cmdlets eller CLI-kommando och ange konfigurationen för kryptering.

   * Överför krypterade VHD för scenariot kund-krypterad VHD till ditt lagringskonto och kryptering nyckelmaterial till nyckelvalvet. Ange hur kryptering för att aktivera kryptering på en ny IaaS-VM.
   * Ange den kryptering för att aktivera kryptering på IaaS-VM för nya virtuella datorer som skapas från Marketplace och befintliga virtuella datorer som redan körs i Azure.

3. Bevilja åtkomst till Azure-plattformen läsa krypteringsnyckel för material (krypteringsnycklar BitLocker för Windows-System) och lösenfrasen för Linux från nyckelvalvet att aktivera kryptering på IaaS-VM.

4. Ange Programidentitet Azure Active Directory (AD Azure) om du vill skriva krypteringsnyckeln materialet till nyckelvalvet. På så sätt kan kryptering på IaaS-VM för scenarier som nämns i steg 2.

5. Azure uppdaterar tjänstmodell VM med kryptering och nyckelvalv-konfigurationen och ställer in den kryptera virtuella datorn.

 ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Arbetsflöde för dekryptering
Om du vill inaktivera hårddiskkryptering för IaaS-VM utför du följande anvisningar:

1. Välja att inaktivera kryptering (dekryptering) på en körs IaaS-VM i Azure via Azure Disk Encryption Resource Manager-mall eller PowerShell-cmdlets och ange konfigurationen för dekryptering.

 Det här steget inaktiverar kryptering av Operativsystemet eller datavolym eller både på körs Windows IaaS-VM. Men som nämns i föregående avsnitt stöds om du inaktiverar kryptering för OS-disk för Linux inte. Steget dekryptering är endast tillåten för dataenheter på Linux virtuella datorer som OS-disken inte krypteras.
2. Azure uppdaterar VM tjänstmodell och IaaS VM markeras dekrypterade. Innehållet i den virtuella datorn är inte längre krypterat i vila.

> [!NOTE]
> Inaktivera kryptering åtgärden tar inte bort nyckelvalvet och nyckelmaterial för kryptering (krypteringsnycklar BitLocker för Windows-System) eller lösenfrasen för Linux.
 > Om du inaktiverar kryptering för OS-disk för Linux stöds inte. Steget dekryptering är endast tillåtna för dataenheter i virtuella Linux-datorer.
Inaktivera disk datakryptering för Linux stöds inte om OS-enheten är krypterad.

## <a name="prerequisites"></a>Krav
Innan du aktiverar Azure Disk Encryption på Azure IaaS-VM för de scenarier som stöds som beskrivs i avsnittet ”Översikt”, se följande krav:

* Du måste ha en giltig aktiv Azure-prenumeration att skapa resurser i Azure i regionerna som stöds.
* Azure Disk Encryption stöds på följande versioner av Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016.
* Azure Disk Encryption stöds på följande Windows-klientversioner: klienten för Windows 8 och Windows 10-klient.

> [!NOTE]
> Windows Server 2008 R2, måste du ha .NET Framework 4.5 installerat innan du aktiverar kryptering i Azure. Du kan installera det från Windows Update genom att installera valfria Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Azure Disk Encryption stöds på följande Azure-galleriet baserat Linux server-distributioner och versioner:

| Linux-Distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 16.04-VARJE DAG-LTS | OS- och disk |
| Ubuntu | 14.04.5-DAILY-LTS | OS- och disk |
| Ubuntu | 12.10 | Datadisk |
| Ubuntu | 12.04 | Datadisk |
| RHEL | 7.4 | OS- och disk |
| RHEL | 7.3 | OS- och disk |
| RHEL | LVM 7.3 | OS- och disk |
| RHEL | 7.2 | OS- och disk |
| RHEL | 6.8 | OS- och disk |
| RHEL | 6.7 | Datadisk |
| CentOS | 7.3 | OS- och disk |
| CentOS | 7.2n | OS- och disk |
| CentOS | 6.8 | OS- och disk |
| CentOS | 7.1 | Datadisk |
| CentOS | 7.0 | Datadisk |
| CentOS | 6.7 | Datadisk |
| CentOS | 6.6 | Datadisk |
| CentOS | 6.5 | Datadisk |
| openSUSE | 13.2 | Datadisk |
| SLES | 12 SP1 | Datadisk |
| SLES | 12-SP1 (Premium) | Datadisk |
| SLES | HPC 12 | Datadisk |
| SLES | 11 SP4 (Premium) | Datadisk |
| SLES | 11 SP4 | Datadisk |

* Azure Disk Encryption kräver att dina nyckelvalvet och virtuella datorer finns i samma Azure-region och prenumeration.

> [!NOTE]
> Konfigurera resurserna i olika områden orsakar ett fel i Azure Disk Encryption funktionen aktiveras.

* Om du vill installera och konfigurera nyckelvalvet för Azure Disk Encryption, finns i avsnittet **ställa upp och konfigurera nyckelvalvet för Azure Disk Encryption** i den *krav* i den här artikeln.
* Om du vill installera och konfigurera Azure AD-program i Azure Active directory för Azure Disk Encryption, finns i avsnittet **konfigurera Azure AD-program i Azure Active Directory** i den *krav* i den här artikeln.
* Om du vill installera och konfigurera nyckelvalv åtkomstprincip för Azure AD-program, finns i avsnittet **konfigurera nyckelvalv åtkomstprincip för Azure AD-programmet** i den *krav* i den här artikeln.
* För att förbereda en förkrypterade Windows VHD finns i avsnittet **förbereda en förkrypterade Windows VHD** i den *bilaga*.
* För att förbereda en förkrypterade Linux VHD finns i avsnittet **förbereda en förkrypterade Linux VHD** i den *bilaga*.
* Azure-plattformen behöver åtkomst till krypteringsnycklarna eller hemligheter i nyckelvalvet att göra dem tillgängliga för den virtuella datorn när den startar och dekrypterar systemvolymen virtuell dator. Om du vill tilldela behörigheter till Azure-plattformen, ange den **EnabledForDiskEncryption** egenskap i nyckelvalvet. Mer information finns i **ställa in och konfigurera nyckelvalvet för Azure Disk Encryption** i tillägget.
* Ditt nyckelvalv hemlighet och KEK URL: er måste vara en ny version. Azure tillämpar den här begränsningen för versionshantering. Giltig hemlighet och KEK URL: er, se följande exempel:

  * Exempel på en giltig hemliga URL: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig URL för KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption stöder inte att ange portnummer som en del av KEK URL: er och hemligheter i nyckelvalvet. Exempel på inte stöds och stöds nyckelvalv URL: er finns i följande avsnitt:

  * Oacceptabel key vault-URL *https://contosovault.vault.azure.net:443/hemligheter/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Godkända key vault-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Om du vill aktivera Azure Disk Encryption måste IaaS-VM-funktionen uppfylla följande slutpunkt configuration nätverkskrav:
  * För att få en token för att ansluta till nyckelvalvet, IaaS VM måste kunna ansluta till en Azure Active Directory-slutpunkt \[login.microsoftonline.com\].
  * Om du vill skriva krypteringsnycklarna till nyckelvalvet, kunna IaaS VM ansluta till slutpunkt för nyckelvalv.
  * IaaS VM måste kunna ansluta till en Azure storage-slutpunkt som är värd för databasen Azure-tillägget och ett Azure storage-konto som är värd för VHD-filer.

  > [!NOTE]
  > Om din säkerhetsprincip begränsar åtkomst från Azure virtuella datorer till Internet, kan du matcha föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutningar till IP-adresser.
  >
  >Att konfigurera och komma åt Azure Key Vault bakom en brandvägg (https://docs.microsoft.com/en-us/azure/key-vault/key-vault-access-behind-firewall)

* Använd den senaste versionen av Azure PowerShell SDK-version för att konfigurera Azure Disk Encryption. Hämta den senaste versionen av [versionen av Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Azure Disk Encryption stöds inte på [Azure PowerShell SDK version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Om du får ett fel om att använda Azure PowerShell 1.1.0, se [Azure Disk Encryption fel relaterade till Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Om du vill köra alla Azure CLI-kommandon och koppla den till din Azure-prenumeration, måste du först installera Azure CLI:
  * Om du vill installera Azure CLI och koppla den till din Azure-prenumeration, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md).
  * Om du vill använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager finns [Azure CLI-kommandona i Resource Manager-läget](../virtual-machines/azure-cli-arm-commands.md).

* När du krypterar en hanterade diskar är obligatorisk förutsättning för att ta en ögonblicksbild av den hantera disken eller en säkerhetskopia av disken utanför Azure Disk Encryption innan du aktiverar kryptering.  Utan en säkerhetskopia för kan ett oväntat fel under krypteringen kanske den disken och VM användas utan ett återställningsalternativ.  Ange AzureRmVMDiskEncryptionExtension för närvarande inte säkerhetskopiera hanterade diskar och kommer fel om användas mot en hanterad disk såvida inte parametern - skipVmBackup har angetts.  Den här parametern är inte säkert att använda såvida inte en säkerhetskopia har redan gjorts utanför Azure Disk Encryption.   När parametern - skipVmBackup anges cmdlet inte att göra en säkerhetskopia av den hantera disken före kryptering.  Därför anses en obligatorisk förutsättning för att se till att en säkerhetskopia av den hantera disken VM är på plats innan du aktiverar Azure Disk Encryption om senare behövs för återställning.  
> [!NOTE]
 > Parametern - skipVmBackup ska aldrig användas om en ögonblicksbild eller säkerhetskopiering har redan gjorts utanför Azure Disk Encryption. 

* Azure Disk Encryption lösningen använder BitLocker externa nyckelskyddet för Windows IaaS-VM. Domän push anslutna virtuella datorer, inte i alla grupprinciper som genomdriva TPM-skydd. Information om en grupprincip för ”Tillåt BitLocker utan en kompatibel TPM”, se [BitLocker gruppolicy referens](https://technet.microsoft.com/library/ee706521).
* BitLocker-principen på domänanslutna virtuella datorer med anpassade Grupprincip måste innehålla följande inställning: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption misslyckas när anpassade grupprincipinställningarna för Bitlocker är inkompatibla. På datorer som inte har rätt princip kan inställningen, tillämpa den nya principen, tvingar den nya principen för att uppdatera (gpupdate.exe/Force) och starta sedan om krävas.  
* För att skapa ett Azure AD-program, skapa nyckelvalvet, eller konfigurera en befintlig nyckelvalv och aktivera kryptering, finns det [PowerShell-skript för Azure Disk Encryption nödvändiga](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Om du vill konfigurera krav för disk-kryptering med hjälp av Azure CLI, se [Bash skriptet](https://github.com/ejarvi/ade-cli-getting-started).
* Om du vill använda Azure Backup-tjänsten för att säkerhetskopiera och återställa krypterade VMs när kryptering är aktiverat med Azure Disk Encryption, kryptera dina virtuella datorer med hjälp av Azure Disk Encryption key konfigurationen. Backup-tjänsten har stöd för virtuella datorer som krypteras med Nej KEK eller KEK konfigurationer. Se [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup kryptering](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).

* När du krypterar en Linux OS-volym, Observera att en VM-omstart krävs för närvarande i slutet av processen. Detta kan göras via portalen, powershell eller CLI.   Om du vill spåra förloppet för kryptering, regelbundet avsöka det statusmeddelande som returneras av Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  När kryptering är klar, det statusmeddelande som returneras av det här kommandot visar detta.  Till exempel ”ProgressMessage: OS-disken har krypterats, starta om den virtuella datorn” nu kan den virtuella datorn startas om och användas.  

* Azure Disk Encryption för Linux kräver datadiskar för att ha ett anslutet filsystem i Linux före kryptering

* Rekursivt monterade diskar inte stöds av Azure Disk Encryption för Linux data. Om exempelvis målsystemet har monterats en disk i foo-fältet och sedan en annan på /foo/bar/baz, kryptering av /foo/bar/baz lyckas, men misslyckas kryptering av/foo/stapel. 

* Azure Disk Encryption stöds bara på Azure stöds galleriavbildningar som uppfyller dessa krav. Kunden anpassade avbildningar stöds inte på grund av anpassade partitionsscheman och processen beteenden som kan finnas på dessa bilder. Dessutom Bildbaserad även galleriet Virtuella datorer som ursprungligen uppfyllda förutsättningar men har ändrats efter att skapa kan vara inkompatibla.  För som är därför den föreslagna proceduren för att kryptera en Linux VM att starta från en avbildning av ren galleriet, kryptera den virtuella datorn och sedan lägga till anpassade programvara eller data i den virtuella datorn efter behov.  

* Azure Disk Encryption och lokala datavolym - Bek volym för Windows och/mnt/azure_bek_disk för Linux virtuella IaaS-datorer på ett säkert sätt för krypteringsnyckeln. Inte ta bort eller redigera en innehållet i den här disken. Demontera inte disken eftersom encryption key förekomst krävs för alla kryptering på IaaS-VM. Viktigt-fil som ingår i volymen som innehåller ytterligare information.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Konfigurera Azure AD-program i Azure Active Directory
När du behöver kryptering har aktiverats på en aktiv virtuell dator i Azure, Azure Disk Encryption genererar och skriver krypteringsnycklarna till nyckelvalvet. Hantera krypteringsnycklar i nyckelvalvet kräver Azure AD-autentisering.

Skapa ett Azure AD-program för det här ändamålet. Du kan hitta detaljerade anvisningar för att registrera ett program i avsnittet ”hämta en identitet för programmet” i blogginlägget [Azure Key Vault - steg-för-steg](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Det här exemplet innehåller också ett antal användbara exempel för att installera och konfigurera nyckelvalvet. Du kan använda client secret-baserad autentisering eller klientautentisering certifikatbaserad Azure AD för autentisering.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Klienten hemlighet-baserad autentisering för Azure AD
Avsnitten som följer kan hjälpa dig att konfigurera en hemlighet-baserade klientautentisering för Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Skapa ett Azure AD-program med hjälp av Azure PowerShell
Använd följande PowerShell-cmdlet för att skapa en Azure AD-program:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId är Azure AD-ClientID och $aadClientSecret är klienthemlighet som du ska använda för att aktivera Azure Disk Encryption. Skydda hemligheten för Azure AD-klient på lämpligt sätt.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Konfigurera Azure AD-klient-ID och Hemlig från Azure-portalen
Du kan också ställa in din Azure AD-klient-ID och Hemlig med hjälp av Azure-portalen. Om du vill utföra den här uppgiften gör du följande:

1. Klicka på den **Active Directory** fliken.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Klicka på **Lägg till program**, och skriv sedan namnet på programmet.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Klicka på pilen och sedan konfigurera egenskaper för program.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Klicka på kryssmarkeringen i det nedre vänstra hörnet ska slutföras. Konfigurationssidan för programmet, och Azure AD-klient-ID visas längst ned på sidan.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Spara hemligheten som Azure AD-klienten genom att klicka på den **spara** knappen. Observera att Azure AD-klienthemlighet i textrutan nycklar. Skydda på lämpligt sätt.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


##### <a name="use-an-existing-application"></a>Använda ett befintligt program
För att köra följande kommandon, hämtar och använder den [Azure AD PowerShell-modulen](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Följande kommandon måste köras från en ny PowerShell-fönstret. Använd inte Azure PowerShell eller Azure Resource Manager-fönstret för att köra kommandona. Vi rekommenderar den här metoden eftersom dessa cmdlets finns i MSOnline modul eller Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Certifikatbaserad autentisering för Azure AD
> [!NOTE]
> Azure AD certifikatbaserad autentisering stöds för närvarande inte på Linux virtuella datorer.

I de följande avsnitten visar hur du konfigurerar en certifikatbaserad autentisering för Azure AD.

##### <a name="create-an-azure-ad-application"></a>Skapa en Azure AD-program
Kör följande PowerShell-cmdlets för att skapa en Azure AD-program:

> [!NOTE]
> Ersätt följande `yourpassword` sträng med säkra lösenord och skydda lösenordet.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

När du har slutfört det här steget ladda upp PFX-filen till nyckelvalvet och aktivera åtkomstprincipen som krävs för att distribuera certifikatet till en virtuell dator.

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

När du har slutfört det här steget ladda upp PFX-filen till nyckelvalvet och aktivera åtkomstprincipen som behövs för att distribuera certifikatet till en virtuell dator.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Ladda upp PFX-filen till nyckelvalvet
En detaljerad förklaring av den här processen finns [den officiella Azure nyckeln valvet Teamblogg](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Följande PowerShell-cmdlets är allt du behöver för uppgiften. Glöm inte att köra dem från Azure PowerShell-konsolen.

> [!NOTE]
> Ersätt följande `yourpassword` sträng med säkra lösenord och skydda lösenordet.

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
När du är klar med att ladda upp PFX distribuera ett certifikat i nyckelvalvet till en befintlig virtuell dator med följande:
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

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Ställ in nyckelvalv åtkomstprincip för Azure AD-program
Azure AD-program behöver behörighet att komma åt nycklar och hemligheter i valvet. Använd den [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) för att bevilja behörighet till programmet, med klient-ID (som skapades när programmet registrerades) som den _– ServicePrincipalName_ parametervärdet. Mer information finns i bloggposten [Azure Key Vault - steg-för-steg](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Här är ett exempel på hur du utför den här uppgiften via PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption måste du konfigurera följande principer för åtkomst till din Azure AD-klientprogram: _WrapKey_ och _ange_ behörigheter.

## <a name="terminology"></a>Terminologi
Använd tabellen nedan terminologi för att förstå några av de vanliga termer som används av den här tekniken:

| Terminologi | Definition |
| --- | --- |
| Azure AD | Azure AD [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Azure AD-kontot är en förutsättning för autentisering, lagra och hämta hemligheter från en nyckelvalvet. |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som baseras på FIPS Federal Information Processing Standards validerade maskinvarusäkerhetsmoduler, som bidrar till att skydda din kryptografiska nycklar och hemligheter känslig. Mer information finns i [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| ARM | Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en branschstandard identifieras Windows volym krypteringsteknik som används för att aktivera kryptering på Windows IaaS-VM. |
| BEK | Krypteringsnycklarna i BitLocker används för att kryptera OS startvolymen och datavolymer. BitLocker-nycklar skyddas i ett nyckelvalv som hemligheter. |
| CLI | Se [Azure-kommandoradsgränssnittet](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) är Linux-baserade och transparent diskkryptering undersystemet som används för att aktivera kryptering på Linux IaaS-VM. |
| KEK | Viktiga krypteringsnyckeln är den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller omsluter hemligheten. Du kan ange en säkerhets-och maskinvara moduler (HSM)-skyddad nyckel eller programvaruskyddad nyckel. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| PS-cmdlets | Se [Azure PowerShell-cmdlets](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Installera och konfigurera nyckelvalvet för Azure Disk Encryption
Azure Disk Encryption hjälper till att skydda disk-kryptering nycklar och hemligheter i nyckelvalvet. Slutför stegen i följande avsnitt om du vill konfigurera nyckelvalvet för Azure Disk Encryption.

#### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Om du vill skapa ett nyckelvalv med någon av följande alternativ:

* [”101-nyckel-valvet-skapa” Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Azure PowerShell nyckelvalv-cmdlets](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Så här [Secure nyckelvalvet](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Om du redan har installerat en nyckelvalv för din prenumeration, vidare till nästa avsnitt.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Ställa in en krypteringsnyckel nyckel (valfritt)
Om du vill använda en KEK för ett extra säkerhetslager för krypteringsnycklar BitLocker måste du lägga till en KEK i nyckelvalvet. Använd den [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) för att skapa en nyckel för kryptering i nyckelvalvet. Du kan också importera en KEK från din lokala nyckelhantering HSM. Mer information finns i [Key Vault dokumentationen](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Du kan lägga till KEK genom att gå till Azure Resource Manager eller genom att använda nyckelvalv-gränssnittet.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Ange behörigheter för nyckelvalvet
Azure-plattformen behöver åtkomst till krypteringsnycklarna eller hemligheter i nyckelvalvet att göra dem tillgängliga för den virtuella datorn för start och dekryptera volymerna. Om du vill tilldela behörigheter till Azure-plattformen, ange den **EnabledForDiskEncryption** egenskap i nyckeln valvet med hjälp av PowerShell-cmdleten nyckelvalv:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Du kan också ange den **EnabledForDiskEncryption** egenskapen genom att besöka den [resursutforskaren Azure](https://resources.azure.com).

Som tidigare nämnts kan du ange den **EnabledForDiskEncryption** -egenskapen i nyckelvalvet. Annars misslyckas distributionen.

Du kan konfigurera principer för ditt Azure AD-program från nyckelvalvet-gränssnitt som visas här:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

På den **avancerade åtkomstprinciper** och se till att nyckelvalvet är aktiverat för Azure Disk Encryption:

![Azure key vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Distributionsscenarier för disk-kryptering och användarupplevelser
Du kan aktivera många scenarier för disk-kryptering och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i detalj.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Aktivera kryptering på den nya virtuella IaaS-datorer som skapas från Marketplace
Du kan aktivera kryptering på nya Windows IaaS-VM från Marketplace i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Klicka på mallen Azure Snabbkurs **till Azure**, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumerationen, resursgruppen, resursgruppens plats, juridiska villkor och avtalet och klicka sedan på **skapa** att aktivera kryptering på en ny IaaS-VM.

> [!NOTE]
> Den här mallen skapar en ny krypterade Windows virtuell dator som använder Windows Server 2012 galleriet avbildningen.

Du kan aktivera kryptering på en ny IaaS RedHat Linux 7.2 virtuell dator med en 200 GB RAID-0-matris med den här [Resource Manager-mall](https://aka.ms/fde-rhel). När du distribuerar mallen kontrollerar du status för VM-kryptering med hjälp av den `Get-AzureRmVmDiskEncryptionStatus` cmdlet, enligt beskrivningen i [kryptera OS enhet på en körande Linux VM](#encrypting-os-drive-on-a-running-linux-vm). När datorn returnerar statusvärdet _VMRestartPending_, starta om den virtuella datorn.

I följande tabell visas mallparametrar Resource Manager för nya virtuella datorer från Marketplace-scenario med hjälp av Azure AD-klient-ID:

| Parameter | Beskrivning |
| --- | --- |
| adminUserName | Admin användarnamn för den virtuella datorn. |
| adminPassword | Administratörslösenord för den virtuella datorn. |
| newStorageAccountName | Namnet på lagringskontot för att lagra Operativsystemet och virtuella hårddiskar. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G serien. |
| virtualNetworkName | Namnet på den VNet som VM NIC ska tillhöra. |
| subnetName | Namn på undernät i VNet VM NIC ska tillhöra. |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i nyckelvalvet. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter i nyckelvalvet. |
| keyVaultURL | URL för nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL till den viktiga krypteringsnyckel som används för att kryptera genererade BitLocker-nyckel (valfritt). |
| keyVaultResourceGroup | Resursgruppen för nyckelvalvet. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |

> [!NOTE]
> _KeyEncryptionKeyURL_ är en valfri parameter. Du kan ge din egen KEK ytterligare säkerhetskontroll datakrypteringsnyckeln (lösenfrasen hemlig) i nyckelvalvet.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Aktivera kryptering på den nya virtuella IaaS-datorer som skapas från kund-krypterad VHD- och krypteringsnycklar
Du kan aktivera kryptering genom att använda Resource Manager-mall, PowerShell-cmdlets eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs i detalj Resource Manager-mall och CLI-kommandona.

Följ instruktionerna från någon av dessa avsnitt för att förbereda inför krypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad Azure VM.

* [Förbereda en förkrypterade Windows VHD](#preparing-a-pre-encrypted-windows-vhd)
* [Förbereda en förkrypterade Linux VHD](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Med hjälp av Resource Manager-mall
Du kan aktivera kryptering på den kryptera virtuella Hårddisken med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Klicka på mallen Azure Snabbkurs **till Azure**, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumerationen, resursgruppen, resursgruppens plats, juridiska villkor och avtalet och klicka sedan på **skapa** att aktivera kryptering på den nya IaaS-VM.

I följande tabell visas mallparametrar Resource Manager för den kryptera virtuella Hårddisken:

| Parameter | Beskrivning |
| --- | --- |
| newStorageAccountName | Namnet på lagringskontot för att lagra krypterade OS VHD: N. Det här lagringskontot bör redan har skapats i samma resursgrupp och samma plats som den virtuella datorn. |
| osVhdUri | URI för OS-VHD från lagringskontot. |
| osType | OS-produkttyp (Windows-/ Linux). |
| virtualNetworkName | Namnet på den VNet som VM NIC ska tillhöra. Namnet bör redan har skapats i samma resursgrupp och samma plats som den virtuella datorn. |
| subnetName | Namn på undernät för det virtuella nätverk som VM NIC ska tillhöra. |
| vmSize | Storleken på den virtuella datorn. För närvarande stöds endast Standard A, D och G serien. |
| keyVaultResourceID | Resurs-ID som identifierar nyckelvalv resurs i Azure Resource Manager. Du kan hämta den med hjälp av PowerShell-cmdleten `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | URL för disk-krypteringsnyckeln som har ställts in i nyckelvalvet. |
| keyVaultKekUrl | URL för nyckelkryptering-nyckel för att kryptera nyckeln genererade diskkryptering. |
| vmName | Namnet på IaaS-VM. |

#### <a name="using-powershell-cmdlets"></a>Med hjälp av PowerShell-cmdlets
Du kan aktivera kryptering på den kryptera virtuella Hårddisken med hjälp av PowerShell-cmdleten [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Med hjälp av CLI-kommandon
Gör följande om du vill aktivera kryptering för det här scenariot med hjälp av CLI-kommandon:

1. Ange åtkomstprinciper i nyckelvalvet:

   * Ange den **EnabledForDiskEncryption** flaggan:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ange behörigheter till Azure AD-program att skriva hemligheter i nyckelvalvet:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Om du vill aktivera kryptering på en befintlig eller körs VM, skriver du:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Hämta krypteringsstatus:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Om du vill möjliggöra kryptering på en ny virtuell dator från den kryptera virtuella Hårddisken måste använda följande parametrar med den `azure vm create` kommando:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Aktivera kryptering på befintlig eller körs IaaS Windows VM i Azure
Du kan aktivera kryptering genom att använda Resource Manager-mall, PowerShell-cmdlets eller CLI-kommandona i det här scenariot. I följande avsnitt beskrivs i detalj hur du aktiverar det med hjälp av Resource Manager-mall och CLI-kommandona.

#### <a name="using-the-resource-manager-template"></a>Med hjälp av Resource Manager-mall
Du kan aktivera kryptering på befintliga eller IaaS Windows virtuella datorer som körs i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Klicka på mallen Azure Snabbkurs **till Azure**, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumerationen, resursgruppen, resursgruppens plats, juridiska villkor och avtalet och klicka sedan på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas mallparametrar Resource Manager för befintlig eller virtuella datorer som använder en Azure AD-klient-ID som körs:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i nyckelvalvet. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter i nyckelvalvet. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL till den viktiga krypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| volumeType | Typ av krypteringsåtgärden utförs på volymen. Giltiga värden är _OS_, _Data_, och _alla_. |
| sequenceVersion | Sekvens version av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärden utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |

> [!NOTE]
> _KeyEncryptionKeyURL_ är en valfri parameter. Du kan ge din egen KEK ytterligare säkerhetskontroll datakrypteringsnyckeln (BitLocker-kryptering hemliga) i nyckelvalvet.

#### <a name="using-powershell-cmdlets"></a>Med hjälp av PowerShell-cmdlets
Information om hur du aktiverar kryptering med Azure Disk Encryption med PowerShell-cmdlets finns i blogginläggen [utforska Azure Disk Encryption med Azure PowerShell - del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och [utforska Azure Disk Encryption med Azure PowerShell - del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Med hjälp av CLI-kommandon
Om du vill möjliggöra kryptering på befintlig eller använder IaaS Windows VM i Azure med hjälp av CLI-kommandona måste du göra följande:

1. Att ange åtkomstprinciper i nyckelvalvet:
   * Ange den **EnabledForDiskEncryption** flaggan:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ange behörigheter till Azure AD-program att skriva hemligheter i nyckelvalvet:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Aktivera kryptering på en befintlig eller körs VM:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Hämta krypteringsstatus:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Om du vill möjliggöra kryptering på en ny virtuell dator från den kryptera virtuella Hårddisken måste använda följande parametrar med den `azure vm create` kommando:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Aktivera kryptering på en befintlig eller körs IaaS Linux-dator i Azure
Du kan aktivera kryptering på en befintlig eller körs IaaS Linux-dator i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Klicka på **till Azure** på mallen Azure Snabbkurs, ange konfigurationen för kryptering på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumerationen, resursgruppen, resursgruppens plats, juridiska villkor och avtalet och klicka sedan på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager mallparametrar för befintlig eller virtuella datorer som använder en Azure AD-klient-ID som körs:

| Parameter | Beskrivning |
| --- | --- |
| AADClientID | Klient-ID för Azure AD-program som har behörighet att skriva hemligheter i nyckelvalvet. |
| AADClientSecret | Klienthemlighet för Azure AD-program som har behörighet att skriva hemligheter i nyckelvalvet. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL till den viktiga krypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| volumeType | Typ av krypteringsåtgärden utförs på volymen. Giltiga värden som stöds är _OS_ eller _alla_ (finns stöd för Linux-distributioner och versioner för Operativsystemet och datadiskarna under prerequisiteis tidigare). |
| sequenceVersion | Sekvens version av BitLocker-åtgärden. Öka det här versionsnumret varje gång en diskkryptering åtgärden utförs på samma virtuella dator. |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på. |
| Lösenfrasen | Ange ett starkt lösenord som datakrypteringsnyckeln. |

> [!NOTE]
> _KeyEncryptionKeyURL_ är en valfri parameter. Du kan ge din egen KEK ytterligare säkerhetskontroll datakrypteringsnyckeln (lösenfrasen hemlig) i nyckelvalvet.

#### <a name="cli-commands"></a>CLI-kommandon
Du kan aktivera kryptering på den kryptera virtuella Hårddisken genom att installera och använda den [CLI kommandot](../cli-install-nodejs.md). Om du vill möjliggöra kryptering på befintlig eller IaaS Linux virtuella datorer som körs i Azure med hjälp av CLI-kommandona måste du göra följande:

1. Ange åtkomstprinciper i nyckelvalvet:

 * Ange den **EnabledForDiskEncryption** flaggan:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Ange behörigheter till Azure AD-program att skriva hemligheter i nyckelvalvet:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Aktivera kryptering på en befintlig eller körs VM:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Hämta krypteringsstatus:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Om du vill möjliggöra kryptering på en ny virtuell dator från den kryptera virtuella Hårddisken måste använda följande parametrar med den `azure vm create` kommando:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Hämta krypteringsstatus för en krypterad IaaS VM
Du kan hämta krypteringsstatus med Azure Resource Manager [PowerShell-cmdlets](/powershell/azure/overview), eller CLI-kommandona. I följande avsnitt beskrivs hur du använder Azure Portal och CLI-kommandon för att hämta krypteringsstatus.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Hämta krypteringsstatus för en krypterad Windows-dator med hjälp av Azure Resource Manager
Du kan hämta krypteringsstatus av IaaS-VM från Azure Resource Manager genom att göra följande:

1. Logga in på den [Azure Portal](https://portal.azure.com/), och klicka sedan på **virtuella datorer** i den vänstra rutan att visa en sammanfattning av de virtuella datorerna i din prenumeration. Du kan filtrera vyn virtuella datorer genom att välja prenumerationsnamn i den **prenumeration** listrutan.

2. Längst upp i den **virtuella datorer** klickar du på **kolumner**.

3. På den **Välj kolumnen** bladet väljer **diskkryptering**, och klicka sedan på **uppdatering**. Du bör se diskkryptering kolumnen visar krypteringsstatus _aktiverad_ eller _inte aktiverat_ för varje VM som visas i följande bild:

 ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Hämta krypteringsstatus för en krypterad (Windows-/ Linux) IaaS VM med hjälp av PowerShell-cmdlet diskkryptering
Du kan hämta krypteringsstatus av IaaS-VM från PowerShell-cmdlet diskkryptering `Get-AzureRmVMDiskEncryptionStatus`. För att få krypteringsinställningar för den virtuella datorn, anger du följande:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Du kan inspektera resultatet av _Get-AzureRmVMDiskEncryptionStatus_ nyckeln URL: er för kryptering.

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

Värdena OSVolumeEncrypted och DataVolumesEncrypted inställningar är inställda på _krypterad_, vilket visar att båda volymerna krypteras med Azure Disk Encryption. Information om hur du aktiverar kryptering med Azure Disk Encryption med PowerShell-cmdlets finns i blogginläggen [utforska Azure Disk Encryption med Azure PowerShell - del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) och [utforska Azure Disk Encryption med Azure PowerShell - del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> På Linux virtuella datorer, det tar tre eller fyra minuter den `Get-AzureRmVMDiskEncryptionStatus` för att rapportera krypteringsstatus.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Hämta krypteringsstatus av IaaS-VM från kommandot CLI-diskkryptering
Du kan hämta krypteringsstatus av IaaS-VM med hjälp av kommandot CLI-diskkryptering `azure vm show-disk-encryption-status`. Ange sessionen Azure CLI för att få krypteringsinställningar för den virtuella datorn:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Inaktivera kryptering på använder Windows IaaS-VM
Du kan inaktivera kryptering på en aktiv Windows eller Linux IaaS-VM via Azure Disk Encryption Resource Manager-mall eller PowerShell-cmdlets och ange konfigurationen för dekryptering.

##### <a name="windows-vm"></a>Windows VM
Inaktivera kryptering steget inaktiverar kryptering av Operativsystemet, datavolym eller båda på körs Windows IaaS-VM. Du kan inte inaktivera systemvolymen och lämna datavolym krypteras. När steget inaktivera kryptering utförs Azure klassiska distributionsmodellen uppdaterar VM tjänstmodell och Windows IaaS-VM är markerad dekrypterade. Innehållet i den virtuella datorn är inte längre krypterat i vila. Dekrypteringen tar inte bort nyckelvalvet och kryptering nyckelmaterial (krypteringsnycklar BitLocker för Windows och lösenfrasen för Linux).

##### <a name="linux-vm"></a>Linux VM
Inaktivera kryptering steget inaktiverar kryptering av datavolym på körs Linux IaaS-VM. Det här steget fungerar bara om OS-disken inte krypteras.

> [!NOTE]
> Om du inaktiverar kryptering på OS-disken tillåts inte för virtuella Linux-datorer.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Inaktivera kryptering på en befintlig eller körs IaaS-VM
Du kan inaktivera diskkryptering på Windows IaaS virtuella datorer som körs med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Klicka på mallen Azure Snabbkurs **till Azure**, ange dekryptering konfigurationen på den **parametrar** bladet och klicka sedan på **OK**.

2. Välj prenumerationen, resursgruppen, resursgruppens plats, juridiska villkor och avtalet och klicka sedan på **skapa** att aktivera kryptering på en ny IaaS-VM.

För Linux virtuella datorer kan du inaktivera kryptering med hjälp av den [inaktivera kryptering på en körs Linux VM](https://aka.ms/decrypt-linuxvm) mall.

I följande tabell visas mallparametrar för Resource Manager för inaktivering av kryptering på en körs IaaS-VM:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella datorn som krypteringsåtgärden ska utföras på.
| volumeType | Typ av volymen som en dekrypteringsåtgärd utförs på. Giltiga värden är _OS_, _Data_, och _alla_. Du kan inte inaktivera kryptering på kör Windows IaaS-VM OS/startvolymen utan inaktiverar kryptering på den _Data_ volym. Observera också att om du inaktiverar kryptering på OS-disken inte tillåts för virtuella Linux-datorer. |
| sequenceVersion | Sekvens version av BitLocker-åtgärden. Öka det här versionsnumret varje gång en disk dekrypteringsåtgärden utförs på samma virtuella dator. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Inaktivera kryptering på en befintlig eller körs IaaS-VM
Om du vill inaktivera kryptering på en befintlig eller körs IaaS-VM med hjälp av PowerShell-cmdleten, se [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Denna cmdlet har stöd för både Windows- och Linux virtuella datorer. Om du vill inaktivera kryptering, installerar ett tillägg på den virtuella datorn. Om den _namn_ parameter har angetts ett tillägg med standardnamnet _AzureDiskEncryption för virtuella Windows-datorer_ skapas.

Tillägget AzureDiskEncryptionForLinux används på Linux virtuella datorer.

> [!NOTE]
> Denna cmdlet startar om den virtuella datorn.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Aktivera kryptering på förkrypterade IaaS-VM med Azure-hanterade disken
Använda Azure hanteras Disk ARM-mall för att skapa en krypterad VM från en förkrypterade virtuell Hårddisk med hjälp av ARM-mallen finns i   
[Skapa en ny krypterade hanterade disk från en förkrypterade VHD/storage-blob] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Aktivera kryptering på en ny Linux IaaS virtuell dator med hanterad Azure-disken
Använd Azure hanteras Disk ARM-mall för att skapa en ny krypterade Linux IaaS virtuell dator med hjälp av ARM-mallen finns i   
[Distributionen av RHEL 7.2 med fullständig diskkryptering] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Aktivera kryptering på en ny Windows IaaS-VM med Azure-hanterade disken
 Använd Azure hanteras Disk ARM-mall för att skapa en ny krypterade Linux IaaS virtuell dator med hjälp av ARM-mallen finns i   
 [Skapa en ny krypterade Windows IaaS hanteras Disk virtuell dator från galleriet avbildning] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiering hanterade diskar baserat utanför och innan du aktiverar Azure Disk Encryption VM-instans.  En ögonblicksbild av den hantera disken kan hämtas från portalen eller Azure Backup kan användas.  Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen.  Cmdlet Set-AzureRmVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs.  Det här kommandot misslyckas mot hanterade diskbaserat VM tills en säkerhetskopia som har gjorts och den här parametern har angetts.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Uppdatera krypteringsinställningarna för en befintlig krypterade icke-premium virtuell dator
  Använda befintliga Azure-disken kryptering stöds gränssnitt för att köra VM [PS-cmdlets, CLI eller ARM-mallar] för att uppdatera inställningarna för kryptering som AAD klient ID/hemlig nyckel krypteringsnyckeln [KEK] BitLocker-krypteringsnyckeln för Windows-VM eller lösenfrasen för Linux Virtuella osv. Uppdatera krypteringsinställning stöds för premium- och premium-lagring virtuella datorer.

## <a name="appendix"></a>Bilaga
### <a name="connect-to-your-subscription"></a>Ansluta till din prenumeration
Innan du fortsätter kan du granska den *krav* i den här artikeln. När du har kontrollerat att alla krav är uppfyllda, kan du ansluta till din prenumeration genom att göra följande:

1. Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

    `Login-AzureRmAccount`

2. Skriv följande om du vill visa prenumerationer för ditt konto om du har flera prenumerationer och vill ange en ska användas:

    `Get-AzureRmSubscription`

3. Om du vill ange den prenumeration som du vill använda, skriver du:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Om du vill kontrollera att prenumerationen konfigurerats är korrekt, skriver du:

    `Get-AzureRmSubscription`

5. För att bekräfta Azure Disk Encryption-cmdlet: ar är installerade, skriver du:

    `Get-command *diskencryption*`

6. Följande utdata bekräftar Azure Disk Encryption PowerShell-installationen:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Förbereda en förkrypterade Windows VHD
Avsnitten som följer är nödvändiga för att förbereda en förkrypterade Windows VHD för distribution som en krypterad VHD i Azure IaaS. Använd informationen för att förbereda och starta en ny Windows VM (VHD) på Azure Site Recovery eller Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Uppdatera grupprincipen för att tillåta utan TPM för OS-skydd
Konfigurera BitLocker grupprincipinställningen **BitLocker-diskkryptering**, som du hittar **lokal datorprincip** > **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter**. Ändra inställningen till **operativsystemsenheter** > **kräver ytterligare autentisering vid start** > **Tillåt BitLocker utan en kompatibel TPM**som visas i följande bild:

![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Installera komponenter för BitLocker-funktion
För Windows Server 2012 och senare, använder du följande kommando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

För Windows Server 2008 R2, använder du följande kommando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Förbereda systemvolymen för BitLocker med hjälp av`bdehdcfg`
Om du vill komprimera OS-partitionen och förbereda datorn för BitLocker, kör du följande kommando:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Skydda systemvolymen med BitLocker
Använd den [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) kommando för att aktivera kryptering på startvolymen med hjälp av ett externt nyckelskydd. Också placera den externa nyckeln (.bek-fil) på extern enhet eller volym. Kryptering är aktiverat på systemet/startvolymen efter nästa omstart.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Förbered den virtuella datorn med en separat/Dataresurs VHD för att hämta den externa nyckeln med hjälp av BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Kryptera en OS-enheten på en Linux-VM som körs
Kryptering av en OS-enhet på en Linux-VM som körs stöds på följande distributioner:

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Krav för OS-diskkryptering

* Den virtuella datorn måste skapas från Marketplace-avbildning i Azure Resource Manager.
* Azure virtuell dator med minst 4 GB RAM-minne (rekommenderas storleken är 7 GB).
* (För RHEL och CentOS) Inaktivera SELinux. Om du vill inaktivera SELinux finns i ”4.4.2. Inaktivera SELinux ”i den [SELinux användar- och Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) på den virtuella datorn.
* Starta om den virtuella datorn på minst en gång när du inaktiverar SELinux.

##### <a name="steps"></a>Steg
1. Skapa en virtuell dator med någon av de distributioner som har angetts tidigare.

 OS-diskkryptering stöds för CentOS 7.2 via en särskild avbildning. Ange ”7.2n” som SKU: N om du vill använda den här avbildningen när du skapar den virtuella datorn:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurera den virtuella datorn efter dina behov. Om du vill att kryptera alla (OS + data) enheter, dataenheter måste vara angivet och kan monteras från /etc/fstab.

 > [!NOTE]
 > Använd UUID =... att ange dataenheter i/etc/fstab istället för att ange blockera enhetens namn (till exempel/dev/sdb1). Ordningen på enheter ändras på den virtuella datorn under krypteringen. Om den virtuella datorn är beroende av en viss ordning för blockera enheter, kommer inte att montera dem efter kryptering.

3. Logga ut från SSH-sessioner.

4. Ange om du vill kryptera OS volumeType som **alla** eller **OS** när du [Aktivera kryptering](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Alla användare kan processer som inte körs som `systemd` tjänster ska avslutas med en `SIGKILL`. Starta om den virtuella datorn. När du aktiverar OS-diskkryptering på en aktiv virtuell dator planera VM driftstopp.

5. Regelbundet övervaka förloppet för kryptering med hjälp av anvisningarna i den [nästa avsnitt](#monitoring-os-encryption-progress).

6. När Get-AzureRmVmDiskEncryptionStatus visar ”VMRestartPending”, startar du om den virtuella datorn genom att logga in till den eller genom att använda portalen, PowerShell eller CLI.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Innan du startar om rekommenderar vi att du sparar [starta diagnostik](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) av den virtuella datorn.

#### <a name="monitoring-os-encryption-progress"></a>Övervaka förloppet för OS-kryptering
Du kan övervaka förloppet för OS-kryptering på tre sätt:

* Använd den `Get-AzureRmVmDiskEncryptionStatus` cmdlet och kontrollera fältet ProgressMessage:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 När den virtuella datorn når ”OS-disken kryptering igång”, tar ungefär 40 till 50 minuter säkerhetskopieras VM på en Premium-lagring.

 Eftersom [utfärda #388](https://github.com/Azure/WALinuxAgent/issues/388) i WALinuxAgent, `OsVolumeEncrypted` och `DataVolumesEncrypted` visas som `Unknown` i vissa distributioner. Med WALinuxAgent version 2.1.5 och senare kan det här problemet löses automatiskt. Om du ser `Unknown` utdata och du kan kontrollera status för disk-kryptering med hjälp av resursutforskaren i Azure.

 Gå till [resursutforskaren Azure](https://resources.azure.com/), och expandera sedan den här hierarkin på panelen markeringen vänster:

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

 Rulla ned till krypteringsstatus för enheter i InstanceView.

 ![VM-instansvyn](./media/azure-security-disk-encryption/vm-instanceview.png)

* Titta på [starta diagnostik](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). Meddelanden från tillägget ADE prefixet `[AzureDiskEncryption]`.

* Logga in på den virtuella datorn via SSH och hämta tillägget loggen:

    /var/log/Azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Vi rekommenderar att du inte loggar in till den virtuella datorn när OS-kryptering pågår. Kopiera loggar endast när de två metoderna har misslyckats.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Förbereda en förkrypterade Linux VHD
##### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurera kryptering under installationen av distributionsplatsen genom att göra följande:

1. Välj **konfigurera krypterade volymer** när du partitionera diskarna.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Skapa en separat startenheten som inte får vara krypterade. Kryptera din rotenhet.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Ange en lösenfras. Detta är det lösenord som du överför till nyckelvalvet.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Slut partitionering.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. När du startar den virtuella datorn och ange en lösenfras, använder du det lösenord som du angav i steg 3.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Förbereda den virtuella datorn för överföring till Azure med hjälp av [instruktionerna](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Kör inte det sista steget (avetablering VM) ännu.

Konfigurera krypteringen ska fungera med Azure genom att göra följande:

1. Skapa en fil under /usr/local/sbin/azure_crypt_key.sh, med innehållet i följande skript. Vara uppmärksam på KeyFileName, eftersom den är filnamnet lösenfras som används av Azure.

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

2. Ändra crypt config i */etc/crypttab*. Det bör se ut så här:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Om du redigerar *azure_crypt_key.sh* i Windows och du kopierade den till Linux, kör `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

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
6. Kör `update-initramfs -u -k all` att uppdatera initramfs så att den `keyscript` börjar gälla.

7. Nu kan du ta bort etableringen den virtuella datorn.

 ![Ubuntu 16.04 installationen](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fortsätt till nästa steg och [överför den virtuella Hårddisken](#upload-encrypted-vhd-to-an-azure-storage-account) till Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Om du vill konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. När du partitionera diskarna väljer **kryptera volymen grupp**, och sedan ange ett lösenord. Detta är det lösenord som du överför till nyckelvalvet.

 ![Konfigurera openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Starta den virtuella datorn med hjälp av lösenordet.

 ![Konfigurera openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Förbereda den virtuella datorn för överföring till Azure genom att följa instruktionerna i [förbereda en virtuell dator SLES eller openSUSE för Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Kör inte det sista steget (avetablering VM) ännu.

Om du vill konfigurera krypteringen ska fungera med Azure, gör du följande:
1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentera bort dessa linjer i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

6. Nu kan du ta bort etableringen den virtuella datorn och [överför den virtuella Hårddisken](#upload-encrypted-vhd-to-an-azure-storage-account) till Azure.

##### <a name="centos-7"></a>CentOS 7
Om du vill konfigurera kryptering under installationen av distributionsplatsen, gör du följande:
1. Välj **kryptera data** när du partitionera diskarna.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Kontrollera att **kryptera** har valts för rot-partitionen.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Ange en lösenfras. Detta är det lösenord som du överför till nyckelvalvet.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. När du startar den virtuella datorn och ange en lösenfras, använder du det lösenord som du angav i steg 3.

 ![CentOS 7 installationen](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Förbereda den virtuella datorn för överföring till Azure med hjälp av anvisningarna i ”CentOS 7.0 +” [förbereda en CentOS-baserad virtuell dator för Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Kör inte det sista steget (avetablering VM) ännu.

6. Nu kan du ta bort etableringen den virtuella datorn och [överför den virtuella Hårddisken](#upload-encrypted-vhd-to-an-azure-storage-account) till Azure.

Om du vill konfigurera krypteringen ska fungera med Azure, gör du följande:

1. Redigera /etc/dracut.conf och Lägg till följande rad:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentera bort dessa linjer i slutet av filen /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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
4. Redigera /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh och Lägg till detta efter ”# öppna LUKS enhet”:
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

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Överföra krypterad VHD till Azure storage-konto
När BitLocker-kryptering eller DM-Crypt kryptering har aktiverats, måste lokal krypterade VHD överföras till ditt lagringskonto.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Ladda upp hemligheten som disk-kryptering för förkrypterade VM till nyckelvalvet
Den hemlighet som disk-kryptering som du hämtade måste tidigare laddas upp som en hemlighet i nyckelvalvet. Nyckelvalvet måste ha behörighet som aktiverats för din Azure AD-klient och kryptering.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Disk encryption hemlighet inte krypterats med en KEK
Om du vill konfigurera hemlighet i nyckelvalvet, använda [Set AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Om du har en Windows-dator kan filen bek kodas som en base64-sträng och sedan överförs till nyckelvalvet med hjälp av den `Set-AzureKeyVaultSecret` cmdlet. För Linux lösenfrasen kodas som en base64-sträng och sedan har överförts till nyckelvalvet. Kontrollera dessutom att följande taggar anges när du skapar hemligheten i nyckelvalvet.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Använd den `$secretUrl` i nästa steg för [kopplar OS-disk utan att använda KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disk encryption hemlighet krypteras med en KEK
Innan du laddar upp hemligheten nyckelvalvet kryptera om du vill den med hjälp av en nyckel krypteringsnyckel. Använd radbyte [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) att först kryptera hemligheten med hjälp av nyckeln krypteringsnyckeln. Utdata från åtgärden radbyte är en base64-URL: en kodad sträng som du kan sedan överföra som en hemlighet med hjälp av den [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) cmdlet.

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

Använd `$KeyEncryptionKey` och `$secretUrl` i nästa steg för [kopplar OS-disk med hjälp av KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Ange en hemlig URL när du ansluter en OS-disk
#### <a name="without-using-a-kek"></a>Utan att använda en KEK
När du bifogar OS-disk, måste du skicka `$secretUrl`. URL: en har genererats i avsnittet ”Disk encryption hemlighet inte krypterats med en KEK”.

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
När du ansluter OS-disk, skicka `$KeyEncryptionKey` och `$secretUrl`. URL: en har genererats i avsnittet ”Disk encryption hemlighet inte krypterats med en KEK”.

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

## <a name="download-this-guide"></a>Hämta denna guide
Du kan hämta den här guiden från den [TechNet-galleriet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## <a name="for-more-information"></a>Mer information
[Utforska Azure Disk Encryption med Azure PowerShell - del 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Utforska Azure Disk Encryption med Azure PowerShell - del 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
