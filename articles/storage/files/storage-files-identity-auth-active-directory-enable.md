---
title: Aktivera Active Directory-autentisering via SMB för Azure-filer
description: Lär dig hur du aktiverar identitetsbaserad autentisering via SMB för Azure-filresurser via Active Directory. Dina domänanslutna virtuella datorer i Windows (VMs) kan sedan komma åt Azure-filresurser med ad-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: 44debc299054568769bfbe6cfc089cc528594274
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677079"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Aktivera lokal Active Directory Domain Services-autentisering över SMB för Azure-filresurser

[Azure Files](storage-files-introduction.md) stöder identitetsbaserad autentisering via Server Message Block (SMB) via två typer av Domäntjänster: Azure Active Directory Domain Services (Azure AD DS) och lokala ACTIVE Directory Domain Services (AD DS) (förhandsversion). Den här artikeln fokuserar på det nyligen introducerade (förhandsversionen) stöd för att utnyttja Active Directory Domain Service för autentisering till Azure-filresurser. Om du är intresserad av att aktivera Azure AD DS (GA) autentisering för Azure-filresurser, se [vår artikel i ämnet](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Azure-filresurser stöder endast autentisering mot en domäntjänst, antingen Azure Active Directory Domain Service (Azure AD DS) eller lokala Active Directory Domain Services (AD DS). 
>
> AD DS-identiteter som används för Azure-filresursautentisering måste synkroniseras med Azure AD. Synkronisering av lösenordsh hash är valfri. 
> 
> Lokal AD DS-autentisering stöder inte autentisering mot datorkonton som skapats i AD DS. 
> 
> Lokal AD DS-autentisering kan endast stödjas mot en AD-skog där lagringskontot är registrerat. Du kan bara komma åt Azure-filresurser med AD DS-autentiseringsuppgifterna från en enda skog som standard. Om du behöver komma åt din Azure-filresurs från en annan skog kontrollerar du att du har rätt skogsförtroende konfigurerat, se [vanliga frågor](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) och svar om.  
> 
> AD DS-autentisering för SMB-åtkomst och ACL-persistens stöds för Azure-filresurser som hanteras av Azure File Sync.
>
> Azure Files stöder Kerberos-autentisering med AD med RC4-HMAC-kryptering. AES Kerberos-kryptering stöds ännu inte.

När du aktiverar AD DS för Azure-filresurser över SMB kan dina AD DS-anslutna datorer montera Azure-filresurser med dina befintliga AD-autentiseringsuppgifter. Den här funktionen kan aktiveras med en AD DS-miljö som finns antingen i on-prem-datorer eller i Azure.

Identiteter som används för att komma åt Azure-filresurser måste synkroniseras med Azure AD för att framtvinga filbehörigheter på delningsnivå via [rbac-modellen (Role-based Access Control).](../../role-based-access-control/overview.md) [Windows-stil DACLs](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) på filer / kataloger som överförs från befintliga filservrar kommer att bevaras och verkställas. Den här funktionen erbjuder sömlös integrering med ditt företag AD DS-miljö. När du ersätter filservrar på prem med Azure-filresurser kan befintliga användare komma åt Azure-filresurser från sina nuvarande klienter med en enda inloggningsupplevelse, utan att de autentiseringsuppgifter som används ändras.  

> [!NOTE]
> För att hjälpa dig att konfigurera Azure Files AD-autentisering för vissa vanliga användningsfall publicerade vi [två videor](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) med steg för steg-vägledning:
> - Ersätta lokala filservrar med Azure Files (inklusive installation på privat länk för filer och AD-autentisering)
> - Använda Azure Files som profilbehållare för Windows Virtual Desktop (inklusive installation på AD-autentisering och FsLogix-konfiguration)

## <a name="prerequisites"></a>Krav 

Innan du aktiverar AD DS-autentisering för Azure-filresurser kontrollerar du att du har slutfört följande förutsättningar: 

- Välj eller skapa din AD DS-miljö och [synkronisera den med Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Du kan aktivera funktionen i en ny eller befintlig lokal AD DS-miljö. Identiteter som används för åtkomst måste synkroniseras till Azure AD. Azure AD-klienten och filresursen som du använder måste associeras med samma prenumeration. 

    Information om hur du konfigurerar en AD-domänmiljö finns i [Översikt över Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Om du inte har synkroniserat din AD till din Azure AD följer du anvisningarna i [Azure AD Connect och Azure AD Connect Health-installationsöversikten](../../active-directory/hybrid/how-to-connect-install-roadmap.md) för att konfigurera och konfigurera Azure AD Connect. 

- Domänanslutning till en lokal dator eller en Azure VM till lokala AD DS. 

    Om du vill komma åt en filresurs med AD-autentiseringsuppgifter från en dator eller virtuell dator måste enheten vara domänansluten till AD DS. Information om hur du ansluter till domänen finns [i Ansluta till en dator till en domän](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Välj eller skapa ett Azure-lagringskonto i [en region som stöds](#regional-availability). 

    Kontrollera att lagringskontot som innehåller dina filresurser inte redan är konfigurerat för Azure AD DS-autentisering. Om Azure Files Azure AD DS-autentisering är aktiverad på lagringskontot måste den inaktiveras innan den ändras för att använda lokala AD DS. Detta innebär att befintliga ACL:er som konfigurerats i Azure AD DS-miljön måste konfigureras om för korrekt behörighetsverkställighet.
    
    Information om hur du skapar en ny filresurs finns [i Skapa en filresurs i Azure Files](storage-how-to-create-file-share.md).
    
    För bästa prestanda rekommenderar vi att du distribuerar lagringskontot i samma region som den virtuella datorn som du planerar att komma åt resursen från. 

- Verifiera anslutningen genom att montera Azure-filresurser med hjälp av din lagringskontonyckel. 

    Om du vill kontrollera att enheten och filresursen är korrekt konfigurerade kan du prova att montera filresursen med hjälp av lagringskontonyckeln. Mer information finns i [Använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regional tillgänglighet

Azure Files-autentisering med AD DS (förhandsversion) är tillgänglig i [alla offentliga regioner och Azure Gov-regioner](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="workflow-overview"></a>Översikt över arbetsflöden

Innan du aktiverar AD DS-autentisering över SMB för Azure-filresurser rekommenderar vi att du läser och fyller i [det nödvändiga](#prerequisites) avsnittet. Förutsättningarna verifierar att dina AD-, Azure AD- och Azure Storage-miljöer är korrekt konfigurerade. 

Om du planerar att aktivera nätverkskonfigurationer på filresursen rekommenderar vi att du [utvärderar nätverksövervägandet](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) och slutför den relaterade konfigurationen först innan AD DS-autentisering aktiveras.

Följ sedan stegen nedan för att konfigurera Azure Files for AD Authentication: 

1. Aktivera AZURE Files AD DS-autentisering på ditt lagringskonto. 

2. Tilldela åtkomstbehörigheter för en resurs till Azure AD-identiteten (en användare, grupp eller tjänsthuvudnamn) som är synkroniserad med ad-identiteten för målet. 

3. Konfigurera ACL:er över SMB för kataloger och filer. 
 
4. Montera en Azure-filresurs till en virtuell dator som är ansluten till din AD DS. 

5. Uppdatera lösenordet för ditt lagringskontoidentitet i AD DS.

Följande diagram illustrerar arbetsflödet från slutna till slutna för att aktivera Azure AD-autentisering via SMB för Azure-filresurser. 

![Arbetsflödesdiagram för filer AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD DS-autentisering över SMB för Azure-filresurser stöds endast på datorer eller virtuella datorer som körs på os-versioner som är nyare än Windows 7 eller Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. Aktivera AD-autentisering för ditt konto 

Om du vill aktivera AD DS-autentisering via SMB för Azure-filresurser måste du först registrera ditt lagringskonto med AD DS och sedan ange de domänegenskaper som krävs på lagringskontot. När funktionen är aktiverad på lagringskontot gäller den för alla nya och befintliga filresurser i kontot. Används `join-AzStorageAccountForAuth` för att aktivera funktionen. Du hittar den detaljerade beskrivningen av arbetsflödet från slutna till slutna händer i skriptet i det här avsnittet. 

> [!IMPORTANT]
> Cmdleten `Join-AzStorageAccountForAuth` kommer att göra ändringar i din AD-miljö. Läs följande förklaring för att bättre förstå vad den gör för att säkerställa att du har rätt behörighet att köra kommandot och att de tillämpade ändringarna stämmer överens med efterlevnads- och säkerhetsprinciperna. 

Cmdleten `Join-AzStorageAccountForAuth` utför motsvarande en offlinedomänkoppling för det angivna lagringskontot. Skriptet använder cmdleten för att skapa ett konto i AD-domänen, antingen ett [datorkonto](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller ett [tjänstinloggningskonto](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Om du väljer att göra detta manuellt bör du välja det konto som passar bäst för din miljö.

AD DS-kontot som skapats av cmdleten representerar lagringskontot i AD-domänen. Om AD DS-kontot skapas under en organisationsenhet (OU) som tillämpar lösenordsförfallodatum måste du uppdatera lösenordet före den maximala lösenordsåldern. Om du inte uppdaterar lösenordet för kontot kommer autentiseringsfel att uppstå vid åtkomst till Azure-filresurser. Mer information om hur du uppdaterar lösenordet finns i [Uppdatera AD DS-kontolösenord](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Du kan använda följande skript för att utföra registreringen och aktivera funktionen eller, alternativt, du kan manuellt utföra de åtgärder som skriptet skulle. Dessa åtgärder beskrivs i avsnittet efter skriptet. Du behöver inte göra båda.

### <a name="11-script-prerequisites"></a>1.1 Skriptförutsättning
- [Ladda ner och packa upp AzFilesHybrid-modulen](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installera och kör modulen i en enhet som är domän kopplad till lokala AD DS med AD DS-autentiseringsuppgifter som har behörighet att skapa ett tjänstinloggningskonto eller ett datorkonto i mål AD.
-  Kör skriptet med en lokal AD DS-autentiseringsuppgifter som synkroniseras med din Azure AD. Den lokala AD DS-autentiseringsuppgifterna måste ha antingen lagringskontoägaren eller den deltagaren RBAC-rollbehörigheter.
- Kontrollera att ditt lagringskonto finns i en [region som stöds](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1.2 Domän ansluter till ditt lagringskonto
Kom ihåg att ersätta platshållarvärdena med dina egna i parametrarna nedan innan du kör det i PowerShell.
> [!IMPORTANT]
> Domänanslutnings-cmdlet skapar ett AD-konto som representerar lagringskontot (filresursen) i AD. Du kan välja att registrera dig som ett datorkonto eller tjänstinloggningskonto, se [Vanliga frågor och svar](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) för mer information. För datorkonton finns det en standardålder för slutdatum för lösenord som anges i AD vid 30 dagar. På samma sätt kan tjänstinloggningskontot ha en standardålder för slutdatum för lösenord inställd på AD-domänen eller organisationsenhet (OU).
> För båda kontotyperna rekommenderar vi starkt att du kontrollerar vad som är lösenordets förfalloålder som konfigurerats i AD-miljön och planerar att uppdatera lösenordet för [ditt lagringskontoidentitet i AD-kontot](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) nedan före den maximala lösenordsåldern. Om AD-kontolösenordet inte uppdateras resulterar det autentiseringsfel vid åtkomst till Azure-filresurser. Du kan överväga att [skapa en ny AD-organisationsenhet (OU) i AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) och inaktivera principen för förfallodatum för lösenord på [datorkonton](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) eller tjänstinloggningskonton i enlighet med detta. 

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
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

Följande beskrivning sammanfattar alla åtgärder `Join-AzStorageAccountForAuth` som utförs när cmdleten körs. Du kan utföra dessa steg manuellt om du föredrar att inte använda kommandot:

> [!NOTE]
> Om du redan har `Join-AzStorageAccountForAuth` kört skriptet ovan går du till nästa avsnitt "1.3 Bekräfta att funktionen är aktiverad". Du behöver inte utföra åtgärderna nedan igen.

#### <a name="a-checking-environment"></a>a. Kontrollera miljö

Först kontrollerar skriptet din miljö. Närmare bestämt kontrollerar den om [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) är installerat och om skalet körs med administratörsbehörighet. Sedan kontrollerar den om [Az.Storage 1.11.1-preview-modulen](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) är installerad och installerar den om den inte är det. Om dessa kontroller går igenom, då det kommer att kontrollera din AD DS för att se om det finns antingen ett [datorkonto](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller [tjänst inloggningskonto](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) som redan har skapats med SPN / UPN som "cifs/your-storage-account-name-here.file.core.windows.net". Om kontot inte finns skapas ett enligt beskrivningen i avsnitt b nedan.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Skapa en identitet som representerar lagringskontot i AD manuellt

Om du vill skapa det här kontot manuellt skapar `New-AzStorageAccountKey -KeyName kerb1`du en ny Kerberos-nyckel för ditt lagringskonto med . Använd sedan den Kerberos-nyckeln som lösenord för ditt konto. Den här nyckeln används endast under inspelningen och kan inte användas för någon kontroll eller dataplan åtgärder mot lagringskontot.

När du har den nyckeln skapar du antingen en tjänst eller ett datorkonto under din organisationsenhet. Använd följande specifikation: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Lösenord: Kerberos-nyckel för ditt lagringskonto.

Om din organisationsenhet tillämpar lösenordsförfallodatum måste du uppdatera lösenordet före den maximala lösenordsåldern för att förhindra autentiseringsfel vid åtkomst till Azure-filresurser. Mer information finns i Uppdatera lösenord för [ditt lagringskontoidentitet i AD DS.](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

Behåll SID för det nyskapade kontot, du behöver det för nästa steg. Identiteten som du har skapat som representerar lagringskontot behöver inte synkroniseras med Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Aktivera funktionen på ditt lagringskonto

Skriptet aktiverar sedan funktionen på ditt lagringskonto. Om du vill utföra den här konfigurationen manuellt anger du vissa konfigurationsinformation för domänegenskaperna i följande kommando och kör den sedan. Sid för lagringskontot som krävs i följande kommando är SID för den identitet som du skapade i AD DS i [föregående avsnitt](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 Bekräfta att funktionen är aktiverad

Du kan kontrollera om funktionen är aktiverad på ditt lagringskonto kan du använda följande skript:

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

Nu har du aktiverat funktionen på ditt lagringskonto. Nu när funktionen är aktiverad finns det fler steg som du måste utföra för att använda funktionen.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Du har nu aktiverat AD DS-autentisering via SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en AD DS-identitet. Om du vill ge ytterligare användare åtkomst till filresursen följer du instruktionerna i [behörigheterna Tilldela åtkomst](#2-assign-access-permissions-to-an-identity) för att använda en identitet och [konfigurera NTFS-behörigheter över SMB-avsnitt.](#3-configure-ntfs-permissions-over-smb)

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Uppdatera lösenordet för ditt lagringskontoidentitet i AD DS

Om du har registrerat AD DS-identiteten/kontot som representerar ditt lagringskonto under en organisationsenhet som tillämpar lösenordsförfallotid måste du rotera lösenordet före den maximala lösenordsåldern. Om du inte uppdaterar lösenordet för AD DS-kontot resulterar det i autentiseringsfel för att komma åt Azure-filresurser.  

Om du vill utlösa lösenordsrotation kan du köra `Update-AzStorageAccountADObjectPassword` kommandot från modulen AzFilesHybrid. Cmdlet utför åtgärder som liknar lagringskontonyckelrotation. Den får den andra Kerberos-nyckeln till lagringskontot och använder den för att uppdatera lösenordet för det registrerade kontot i AD DS. Sedan återskapas mål Kerberos-nyckeln för lagringskontot och lösenordet för det registrerade kontot i AD DS uppdateras. Du måste köra den här cmdleten i en lokal AD DS-domänansluten miljö.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-filer och hur du använder AD över SMB finns i följande resurser:

- [Översikt över Azure Files identitetsbaserad autentiseringsstöd för SMB-åtkomst](storage-files-active-directory-overview.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
