---
title: Aktivera Active Directory autentisering över SMB för Azure Files
description: Lär dig hur du aktiverar identitets baserad autentisering över SMB för Azure-filresurser via Active Directory. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196506"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser

[Azure Files](storage-files-introduction.md)   stöder identitetsbaserade autentisering över Server Message Block (SMB) via två typer av domän tjänster: Azure Active Directory Domain Services (Azure AD DS) och lokala Active Directory Domain Services (AD DS) (för hands version). Den här artikeln fokuserar på den nyligen introducerade (för hands versionen) stöd för att använda Active Directory-domän tjänst för autentisering till Azure-filresurser. Om du är intresse rad av att aktivera Azure AD DS (GA)-autentisering för Azure-filresurser, se [vår artikel på ämnes raden](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Azure-filresurser stöder bara autentisering mot en domän tjänst, antingen Azure Active Directory Domain Service (Azure AD DS) eller lokalt Active Directory Domain Services (AD DS). 
>
> AD DS-identiteter som används för autentisering med Azure-filresurs måste synkroniseras med Azure AD. Hash-synkronisering av lösen ord är valfritt. 
> 
> Lokal AD DS-autentisering stöder inte autentisering mot dator konton som skapats i AD DS. 
> 
> Lokal AD DS-autentisering kan bara stödjas mot en AD-skog där lagrings kontot är registrerat till. Du kan bara komma åt Azure-filresurser med AD DS-autentiseringsuppgifter från en enda skog som standard. Om du behöver åtkomst till Azure-filresursen från en annan skog kontrollerar du att du har rätt skogs förtroende konfigurerat, se [vanliga frågor och svar](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) för mer information.  
> 
> AD DS-autentisering för SMB-åtkomst och ACL-persistence stöds för Azure-filresurser som hanteras av Azure File Sync.
>
> Azure Files stöder Kerberos-autentisering med AD med RC4-HMAC-kryptering. AES Kerberos-kryptering stöds inte ännu.

När du aktiverar AD DS för Azure-filresurser över SMB kan AD DS-anslutna datorer montera Azure-filresurser med dina befintliga AD-autentiseringsuppgifter. Den här funktionen kan aktive ras med en AD DS-miljö som finns på lokal datorer eller som finns i Azure.

Identiteter som används för att komma åt Azure-filresurser måste synkroniseras med Azure AD för att upprätthålla fil behörigheter på resurs nivå via RBAC-modellen [(rollbaserad åtkomst kontroll)](../../role-based-access-control/overview.md) . [DACL i Windows-format](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) på filer/kataloger som överförs från befintliga fil servrar bevaras och tillämpas. Den här funktionen möjliggör sömlös integrering med din Enterprise AD DS-miljö. När du ersätter lokal-filservrar med Azure-filresurser kan befintliga användare komma åt Azure-filresurser från sina aktuella klienter med enkel inloggning, utan någon ändring av de autentiseringsuppgifter som används.  

> [!NOTE]
> För att hjälpa dig att konfigurera Azure Files AD-autentisering för några vanliga användnings fall publicerade vi [två videor](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) med stegvis vägledning:
> - Ersätta lokala fil servrar med Azure Files (inklusive installations programmet på privat länk för filer och AD-autentisering)
> - Använda Azure Files som profil behållare för Windows Virtual Desktop (inklusive installations programmet för AD-autentisering och FsLogix-konfiguration)

## <a name="prerequisites"></a>Krav 

Innan du aktiverar AD DS-autentisering för Azure-filresurser måste du kontrol lera att du har slutfört följande krav: 

- Välj eller skapa din AD DS-miljö och [synkronisera den till Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Du kan aktivera funktionen på en ny eller befintlig lokal AD DS-miljö. Identiteter som används för åtkomst måste synkroniseras med Azure AD. Azure AD-klienten och fil resursen som du använder måste vara associerad med samma prenumeration. 

    Information om hur du konfigurerar en AD-domänmiljö finns i [Active Directory Domain Services översikt](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Om du inte har synkroniserat din AD till Azure AD följer du rikt linjerna i [Azure AD Connect och Azure AD Connect Health installations översikt](../../active-directory/hybrid/how-to-connect-install-roadmap.md) för att konfigurera och konfigurera Azure AD Connect. 

- Domän – Anslut en lokal dator eller en virtuell Azure-dator till en lokal AD DS. 

    För att få åtkomst till en fil resurs med hjälp av AD-autentiseringsuppgifter från en dator eller virtuell dator måste enheten vara domänansluten till AD DS. Information om hur du ansluter till en domän finns i [ansluta en dator till en domän](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Välj eller skapa ett Azure Storage-konto.  För bästa prestanda rekommenderar vi att du distribuerar lagrings kontot i samma region som den virtuella dator som du planerar att komma åt resursen från.

    Kontrol lera att lagrings kontot som innehåller dina fil resurser inte redan har kon figurer ATS för Azure AD DS-autentisering. Om Azure Files Azure AD DS-autentisering är aktiverat på lagrings kontot måste den inaktive ras innan den kan ändras till att använda lokala AD DS. Detta innebär att befintliga ACL: er som kon figurer ATS i Azure AD DS-miljön måste konfigureras om för korrekt behörighets tillämpning.
    
    Information om hur du skapar en ny fil resurs finns [i skapa en fil resurs i Azure Files](storage-how-to-create-file-share.md).

- Kontrol lera anslutningen genom att montera Azure-filresurser med hjälp av lagrings konto nyckeln. 

    Om du vill kontrol lera att enheten och fil resursen är korrekt konfigurerade kan du försöka att [montera fil resursen](storage-how-to-use-files-windows.md) med din lagrings konto nyckel. Om du får problem med att ansluta till Azure Files kan du läsa [fel söknings verktyget som vi publicerade för Azure Files monterings fel i Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Vi ger också [vägledning](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) för att lösa scenarier när port 445 är blockerad. 

## <a name="regional-availability"></a>Regional tillgänglighet

Azure Files autentisering med AD DS (för hands version) är tillgänglig i [alla offentliga regioner och Azure gov-regioner](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Översikt

Om du planerar att aktivera alla nätverkskonfigurationer på din fil resurs, rekommenderar vi att du utvärderar [nätverks överväganden](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) och slutför den relaterade konfigurationen innan du aktiverar AD DS-autentisering.

Genom att aktivera AD DS-autentisering för dina Azure-filresurser kan du autentisera till dina Azure-filresurser med dina lokal AD DS-autentiseringsuppgifter. Dessutom kan du hantera dina behörigheter bättre genom att tillåta detaljerad åtkomst kontroll. Detta kräver att du synkroniserar identiteter från lokal AD DS till Azure AD med AD Connect. Du styr åtkomsten till delnings nivå med identiteter synkroniserade till Azure AD samtidigt som du hanterar åtkomsten för fil-/delnings nivå med lokal AD DS-autentiseringsuppgifter.

Följ sedan stegen nedan för att konfigurera Azure Files för AD DS-autentisering: 

1. [Aktivera Azure Files AD DS-autentisering på ditt lagrings konto](#1-enable-ad-ds-authentication-for-your-account)

1. [Tilldela åtkomst behörigheter för en resurs till Azure AD-identiteten (en användare, grupp eller tjänstens huvud namn) som är synkroniserad med mål-AD-identiteten](#2-assign-access-permissions-to-an-identity)

1. [Konfigurera ACL: er över SMB för kataloger och filer](#3-configure-ntfs-permissions-over-smb)
 
1. [Montera en Azure-filresurs till en virtuell dator som är ansluten till AD DS](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [Uppdatera lösen ordet för din lagrings konto identitet i AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Följande diagram illustrerar arbets flödet från slut punkt till slut punkt för att aktivera Azure AD-autentisering över SMB för Azure-filresurser. 

![AD Workflow-diagram för filer](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD DS-autentisering över SMB för Azure-filresurser stöds endast på datorer eller virtuella datorer som körs på OS-versioner som är nyare än Windows 7 eller Windows Server 2008 R2. 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1 aktivera AD DS-autentisering för ditt konto 

Om du vill aktivera AD DS-autentisering över SMB för Azure-filresurser måste du först registrera ditt lagrings konto med AD DS och sedan ange de domän egenskaper som krävs för lagrings kontot. När funktionen är aktive rad på lagrings kontot gäller den för alla nya och befintliga fil resurser i kontot. Hämta PowerShell-modulen AzFilesHybrid och Använd `join-AzStorageAccountForAuth` för att aktivera funktionen. Du hittar den detaljerade beskrivningen av arbets flödet från slut punkt till slut punkt i skriptet i det här avsnittet. 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth`Cmdleten kommer att göra ändringar i din AD-miljö. Läs följande förklaring för att bättre förstå vad det gör för att se till att du har rätt behörighet för att köra kommandot och att de tillämpade ändringarna överensstämmer med principerna för efterlevnad och säkerhet. 

`Join-AzStorageAccountForAuth`Cmdleten utför motsvarigheten till en frånkopplad domän anslutning åt det angivna lagrings kontot. Skriptet använder cmdleten för att skapa ett konto i din AD-domän, antingen ett [dator konto](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller ett [inloggnings konto för tjänsten](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Om du väljer att göra detta manuellt bör du välja det konto som passar bäst för din miljö.

AD DS-kontot som skapas av cmdleten representerar lagrings kontot i AD-domänen. Om AD DS-kontot skapas under en organisationsenhet (OU) som tillämpar lösen ordets giltighets tid måste du uppdatera lösen ordet innan du får högsta ålder för lösen ord. Om du inte uppdaterar konto lösen ordet leder det till autentiseringsfel vid åtkomst till Azure-filresurser. Information om hur du uppdaterar lösen ordet finns i [Uppdatera AD DS-kontots lösen ord](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Du kan använda följande skript för att utföra registreringen och aktivera funktionen, annars kan du manuellt utföra de åtgärder som skriptet ska utföra. Dessa åtgärder beskrivs i avsnittet följa skriptet. Du behöver inte göra något.

### <a name="11-script-prerequisites"></a>krav för 1,1-skript
- [Ladda ned och zippa upp AzFilesHybrid-modulen](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installera och kör modulen i en enhet som är domänansluten till lokala AD DS med AD DS-autentiseringsuppgifter som har behörighet att skapa ett tjänst inloggnings konto eller ett dator konto i mål AD.
-  Kör skriptet med en lokal AD DS-autentiseringsuppgift som synkroniseras med din Azure AD. Den lokala AD DS-autentiseringsuppgiften måste ha antingen lagrings kontots ägare eller rollen som deltagares RBAC-roll.
- Kontrol lera att ditt lagrings konto finns i en [region som stöds](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1,2 domän Anslut till ditt lagrings konto
Kom ihåg att ersätta plats hållarnas värden med dina egna i parametrarna nedan innan du kör det i PowerShell.
> [!IMPORTANT]
> Domän kopplings-cmdleten skapar ett AD-konto som representerar lagrings kontot (fil resursen) i AD. Du kan välja att registrera ett dator konto eller tjänst inloggnings konto, se [vanliga frågor och svar](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) för mer information. För dator konton finns det ett standard ålder för lösen ord som anges i AD med 30 dagar. På samma sätt kan tjänst inloggnings kontot ha ett standard-ålder för lösen ord som angetts i AD-domänen eller organisationsenheten (OU).
> För båda konto typerna rekommenderar vi starkt att du kontrollerar vad som är förfallo ålder för lösen ord som kon figurer ATS i din AD-miljö och planerar att [Uppdatera lösen ordet för din lagrings konto identitet i AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) för AD-kontot nedan innan det högsta tillåtna lösen ordets ålder. Du kan [skapa en ny AD-organisationsenhet (OU) i AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) och inaktivera princip för lösen ords förfallo datum på [dator konton](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) eller tjänst inloggnings konton i enlighet med detta. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

Följande beskrivning sammanfattar alla åtgärder som utförs när `Join-AzStorageAccountForAuth` cmdleten körs. Du kan utföra dessa steg manuellt, om du inte vill använda kommandot:

> [!NOTE]
> Om du redan har kört `Join-AzStorageAccountForAuth` skriptet ovan går du till nästa avsnitt "1,3 bekräfta att funktionen är aktive rad". Du behöver inte utföra åtgärderna nedan igen.

#### <a name="a-checking-environment"></a>a. Kontrollerar miljö

Först kontrollerar skriptet din miljö. Mer specifikt kontrollerar det om [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) är installerat och om gränssnittet körs med administratörs behörighet. Sedan kontrollerar den om [modulen AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) är installerad och installerar den om den inte är det. Om dessa kontroller godkänns, kontrollerar det AD DS för att se om det finns något [dator konto](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller [tjänst inloggnings konto](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) som redan har skapats med SPN/UPN som "CIFS/Your-Storage-Account-name-här. File. Core. Windows. net". Om kontot inte finns kommer det att skapas ett som beskrivs i avsnitt b nedan.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Skapa en identitet som representerar lagrings kontot i din AD manuellt

Skapa en ny Kerberos-nyckel för ditt lagrings konto med hjälp av om du vill skapa det här kontot manuellt `New-AzStorageAccountKey -KeyName kerb1` . Använd sedan Kerberos-nyckeln som lösen ord för ditt konto. Den här nyckeln används bara under konfiguration och kan inte användas för alla kontroll-eller data Plans åtgärder mot lagrings kontot.

När du har den nyckeln skapar du antingen en tjänst eller ett dator konto under din ORGANISATIONSENHET. Använd följande specifikation: SPN: "CIFS/ditt-Storage-Account-name-här. File. Core. Windows. net" Password: Kerberos Key för ditt lagrings konto.

Om din OU kräver att lösen ordet upphör att gälla måste du uppdatera lösen ordet innan du får den högsta åldern för lösen ord för att förhindra autentiseringsfel vid åtkomst till Azure-filresurser. Mer information finns i [Uppdatera lösen ordet för din lagrings konto identitet i AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) .

Behåll SID för det nyligen skapade kontot, du behöver det för nästa steg. Den identitet som du har skapat som representerar lagrings kontot behöver inte synkroniseras med Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Aktivera funktionen på ditt lagrings konto

Skriptet aktiverar sedan funktionen på ditt lagrings konto. För att utföra den här installationen manuellt, ange en del konfigurations information för domän egenskaperna i följande kommando och kör den sedan. Det lagrings konto-SID som krävs i följande kommando är SID för identiteten som du skapade i AD DS i [föregående avsnitt](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 bekräfta att funktionen är aktive rad

Du kan kontrol lera om funktionen är aktive rad på ditt lagrings konto, du kan använda följande skript:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Du har nu aktiverat funktionen på ditt lagrings konto. Nu när funktionen är aktive rad finns det flera steg som du måste utföra för att använda funktionen.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nu har du aktiverat AD DS-autentisering över SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en AD DS-identitet. Om du vill ge fler användare åtkomst till din fil resurs följer du anvisningarna i avsnittet [tilldela åtkomst behörigheter](#2-assign-access-permissions-to-an-identity) för att använda en identitet och [Konfigurera NTFS-behörigheter över SMB](#3-configure-ntfs-permissions-over-smb) -avsnitt.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5 uppdatera lösen ordet för din lagrings konto identitet i AD DS

Om du har registrerat AD DS-identiteten/-kontot som representerar ditt lagrings konto under en ORGANISATIONSENHET som tillämpar lösen ordets giltighets tid, måste du rotera lösen ordet innan du anger den högsta ålder för lösen ord. Om du inte uppdaterar lösen ordet för AD DS-kontot leder det till att autentiseringsfel får åtkomst till Azure-filresurser.  

Om du vill utlösa lösen ords rotation kan du köra `Update-AzStorageAccountADObjectPassword` kommandot från AzFilesHybrid-modulen. Cmdleten utför åtgärder som liknar lagringskontots nyckelrotation. Den hämtar den andra Kerberos-nyckeln för lagrings kontot och använder den för att uppdatera lösen ordet för det registrerade kontot i AD DS. Sedan återskapas mål-Kerberos-nyckeln för lagrings kontot och lösen ordet för det registrerade kontot uppdateras i AD DS. Du måste köra denna cmdlet i en lokal AD DS-domänansluten miljö.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files och hur du använder AD över SMB finns i följande resurser:

- [Översikt över stöd för Azure Files Identity-baserad autentisering för SMB-åtkomst](storage-files-active-directory-overview.md)
- [Vanliga frågor och svar](storage-files-faq.md)
