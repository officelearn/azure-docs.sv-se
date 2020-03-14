---
title: Aktivera Active Directory autentisering över SMB för Azure Files
description: Lär dig hur du aktiverar identitets baserad autentisering över SMB för Azure-filresurser via Active Directory. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: rogarana
ms.openlocfilehash: d9d2e06cc3beae8a7bb8ea1b4eee15fb1641ddd4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255228"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Aktivera Active Directory autentisering över SMB för Azure-filresurser

[Azure Files](storage-files-introduction.md) stöder identitetsbaserade autentisering över Server Message Block (SMB) via två typer av domän tjänster: Azure Active Directory Domain Services (Azure AD DS) (ga) och Active Directory (AD) (för hands version). Den här artikeln fokuserar på den nyligen introducerade (för hands versionen) stöd för att använda Active Directory-domän tjänst för autentisering till Azure-filresurser. Om du är intresse rad av att aktivera Azure AD DS (GA)-autentisering för Azure-filresurser, se [vår artikel på ämnes raden](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Azure-filresurser stöder bara autentisering mot en domän tjänst, antingen Azure Active Directory Domain Service (Azure AD DS) eller Active Directory (AD). 
>
> AD-identiteter som används för autentisering med Azure-filresurs måste synkroniseras med Azure AD. Hash-synkronisering av lösen ord är valfritt. 
> 
> AD-autentisering stöder inte autentisering mot dator konton som skapats i AD. 
> 
> AD-autentisering kan bara stödjas mot en AD-skog där lagrings kontot är registrerat till. Du kan bara komma åt Azure-filresurser med AD-autentiseringsuppgifterna från en enda AD-skog som standard. Om du behöver åtkomst till Azure-filresursen från en annan skog kontrollerar du att du har rätt skogs förtroende konfigurerat, se [vanliga frågor och svar](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) för mer information.  
> 
> AD-autentisering för SMB-åtkomst och ACL-persistence stöds för Azure-filresurser som hanteras av Azure File Sync.
>
> Azure Files stöder Kerberos-autentisering med AD med RC4-HMAC-kryptering. AES Kerberos-kryptering stöds inte ännu.

När du aktiverar AD för Azure-filresurser över SMB kan dina AD-domänanslutna datorer montera Azure-filresurser med dina befintliga AD-autentiseringsuppgifter. Den här funktionen kan aktive ras med en AD-miljö som finns antingen på lokal datorer eller som finns i Azure.

AD-identiteter som används för att få åtkomst till Azure-filresurser måste synkroniseras med Azure AD för att upprätthålla fil behörigheter på resurs nivå via RBAC-modellen [(rollbaserad åtkomst kontroll)](../../role-based-access-control/overview.md) . [DACL i Windows-format](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) på filer/kataloger som överförs från befintliga fil servrar bevaras och tillämpas. Den här funktionen möjliggör sömlös integrering med din företags AD-domän infrastruktur. När du ersätter lokal-filservrar med Azure-filresurser kan befintliga användare komma åt Azure-filresurser från sina aktuella klienter med enkel inloggning, utan någon ändring av de autentiseringsuppgifter som används.  
 
## <a name="prerequisites"></a>Förutsättningar 

Innan du aktiverar AD-autentisering för Azure-filresurser måste du kontrol lera att du har slutfört följande krav: 

- Välj eller skapa din AD-miljö och synkronisera den till Azure AD. 

    Du kan aktivera funktionen i en ny eller befintlig AD-miljö. Identiteter som används för åtkomst måste synkroniseras med Azure AD. Azure AD-klienten och fil resursen som du använder måste vara associerad med samma prenumeration. 

    Information om hur du installerar en AD-domänmiljö finns i [Active Directory Domain Services översikt](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Om du inte har synkroniserat din AD till Azure AD, följer du rikt linjerna i [Vad är hybrid identitet med Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) för att fastställa önskad autentiseringsmetod och Azure AD Connect installations alternativ. 

- Domän – Anslut en lokal dator eller en virtuell Azure-dator till AD (kallas även AD DS). 

    För att få åtkomst till en fil resurs med hjälp av AD-autentiseringsuppgifter från en dator eller virtuell dator måste enheten vara domänansluten till AD. Information om hur du ansluter till AD finns i [ansluta en dator till en domän](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Välj eller skapa ett Azure Storage-konto i [en region som stöds](#regional-availability). 

    Kontrol lera att lagrings kontot som innehåller dina fil resurser inte redan har kon figurer ATS för Azure AD DS-autentisering. Om Azure Files Azure AD DS-autentisering är aktiverat på lagrings kontot måste den inaktive ras innan du kan ändra den till att använda AD. Detta innebär att befintliga ACL: er som kon figurer ATS i Azure AD DS-miljön måste konfigureras om för korrekt behörighets tillämpning.
    
    Information om hur du skapar en ny fil resurs finns [i skapa en fil resurs i Azure Files](storage-how-to-create-file-share.md).
    
    För bästa prestanda rekommenderar vi att du distribuerar lagrings kontot i samma region som den virtuella dator som du planerar att komma åt resursen från. 

- Kontrol lera anslutningen genom att montera Azure-filresurser med hjälp av lagrings konto nyckeln. 

    Om du vill kontrol lera att enheten och fil resursen är korrekt konfigurerade kan du försöka att montera fil resursen med din lagrings konto nyckel. Mer information finns i [använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regional tillgänglighet

Azure Files AD-autentisering (för hands version) är tillgänglig i [de flesta offentliga regioner](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Files AD-autentisering är inte tillgänglig i:
- USA, västra
- USA, västra 2
- USA, östra
- USA, östra 2
- Europa, västra
- Europa, norra

## <a name="workflow-overview"></a>Översikt över arbets flöde

Innan du aktiverar AD-autentisering över SMB för Azure-filresurser rekommenderar vi att du går igenom [förutsättningarna](#prerequisites) och ser till att du har slutfört alla steg. Kraven verifierar att dina AD-, Azure AD-och Azure Storage-miljöer är korrekt konfigurerade. 

Ge sedan åtkomst till Azure Files resurser med AD-autentiseringsuppgifter: 

- Aktivera Azure Files AD-autentisering på ditt lagrings konto.  

- Tilldela åtkomst behörigheter för en resurs till Azure AD-identiteten (en användare, grupp eller tjänstens huvud namn) som är synkroniserad med mål-AD-identiteten. 

- Konfigurera ACL: er över SMB för kataloger och filer. 

- Montera en Azure-filresurs från en AD-domänansluten virtuell dator. 

Följande diagram illustrerar arbets flödet från slut punkt till slut punkt för att aktivera Azure AD-autentisering över SMB för Azure-filresurser. 

![AD Workflow-diagram för filer](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> AD-autentisering över SMB för Azure-filresurser stöds bara på datorer eller virtuella datorer som körs på OS-versioner som är nyare än Windows 7 eller Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Aktivera AD-autentisering för ditt konto 

Om du vill aktivera AD-autentisering över SMB för Azure-filresurser måste du först registrera ditt lagrings konto med AD och sedan ange de nödvändiga domän egenskaperna för lagrings kontot. När funktionen är aktive rad på lagrings kontot gäller den för alla nya och befintliga fil resurser i kontot. Använd `join-AzStorageAccountForAuth` för att aktivera funktionen. Du hittar den detaljerade beskrivningen av arbets flödet från slut punkt till slut punkt i avsnittet nedan. 

> [!IMPORTANT]
> `join-AzStorageAccountForAuth`-cmdleten kommer att göra ändringar i din AD-miljö. Läs följande förklaring för att bättre förstå vad det gör för att se till att du har rätt behörighet för att köra kommandot och att de tillämpade ändringarna överensstämmer med principerna för efterlevnad och säkerhet. 

`join-AzStorageAccountForAuth`-cmdleten kommer att utföra motsvarigheten till en offline-domän anslutning för det angivna lagrings kontot. Ett konto skapas i AD-domänen, antingen ett [dator konto](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) eller ett [inloggnings konto för tjänsten](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Det skapade AD-kontot representerar lagrings kontot i AD-domänen. Om AD-kontot har skapats under en AD-organisationsenhet (OU) som tillämpar lösen ordets giltighets tid måste du uppdatera lösen ordet innan du får högsta ålder för lösen ord. Om du inte uppdaterar lösen ordet för AD-kontot leder det till autentiseringsfel vid åtkomst till Azure-filresurser. Information om hur du uppdaterar lösen ordet finns i [Uppdatera AD-kontots lösen ord](#update-ad-account-password).

Du kan använda följande skript för att utföra registreringen och aktivera funktionen, annars kan du manuellt utföra de åtgärder som skriptet ska utföra. Dessa åtgärder beskrivs i avsnittet följa skriptet. Du behöver inte göra något.

### <a name="1-check-prerequisites"></a>1. kontrol lera krav
- [Ladda ned och zippa upp AzFilesHybrid-modulen](https://github.com/Azure-Samples/azure-files-samples/releases)
- Installera och kör modulen i en enhet som är domänansluten till AD med AD-autentiseringsuppgifter som har behörighet att skapa ett tjänst inloggnings konto eller ett dator konto i mål AD.
-  Kör skriptet med en AD-autentiseringsuppgift som synkroniseras med din Azure AD. AD-autentiseringsuppgiften måste ha antingen lagrings kontots ägare eller rollen som deltagares RBAC-roll.
- Kontrol lera att ditt lagrings konto finns i en [region som stöds](#regional-availability).

### <a name="2-execute-ad-enablement-script"></a>2. kör AD-aktiverings skript
Kom ihåg att ersätta plats hållarnas värden med dina egna i parametrarna nedan innan du kör det i PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
#You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationalUnitName "<ou-name-here>"
```

Följande beskrivning sammanfattar alla åtgärder som utförs när `join-AzStorageAccountForAuth` cmdleten körs. Du kan utföra dessa steg manuellt, om du inte vill använda kommandot:

> [!NOTE]
> Om du redan har kört Join-AzStorageAccountForAuth-skriptet ovan går du till nästa avsnitt "3. Bekräfta att funktionen är aktive rad. Du behöver inte utföra åtgärderna nedan igen.

#### <a name="a-checking-environment"></a>a. Kontrollerar miljö

Först kontrollerar den din miljö. Det är särskilt det som kontrollerar om [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) är installerat och om gränssnittet körs med administratörs behörighet. Sedan kontrollerar den om [modulen AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) är installerad och installerar den om den inte är det. Om dessa kontroller godkänns, kontrollerar det din annons för att se om det finns något [dator konto](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller [tjänst inloggnings konto](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) som redan har skapats med SPN/UPN som "CIFS/Your-Storage-Account-name-här. File. Core. Windows. net". Om kontot inte finns kommer det att skapas ett som beskrivs i avsnitt b nedan.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Skapa en identitet som representerar lagrings kontot i din AD manuellt

Skapa en ny Kerberos-nyckel för ditt lagrings konto med hjälp av `New-AzStorageAccountKey -KeyName kerb1`för att skapa det här kontot manuellt. Använd sedan Kerberos-nyckeln som lösen ord för ditt konto. Den här nyckeln används bara under konfiguration och kan inte användas för alla kontroll-eller data Plans åtgärder mot lagrings kontot.

När du har den nyckeln skapar du antingen en tjänst eller ett dator konto under din ORGANISATIONSENHET. Använd följande specifikation: SPN: "CIFS/ditt-Storage-Account-name-här. File. Core. Windows. net" Password: Kerberos Key för ditt lagrings konto.

Om din OU kräver att lösen ordet upphör att gälla måste du uppdatera lösen ordet innan du får den högsta åldern för lösen ord för att förhindra autentiseringsfel vid åtkomst till Azure-filresurser. Mer information finns i [Uppdatera AD-kontots lösen ord](#update-ad-account-password) .

Behåll SID för det nyligen skapade kontot, du behöver det för nästa steg. AD-identiteten som du precis har skapat som representerar lagrings kontot behöver inte synkroniseras med Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Aktivera funktionen på ditt lagrings konto

Skriptet aktiverar sedan funktionen på ditt lagrings konto. För att utföra den här installationen manuellt, ange en del konfigurations information för domän egenskaperna i följande kommando och kör den sedan. Det lagrings konto-SID som krävs i följande kommando är SID för identiteten som du skapade i AD (avsnitt b ovan).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServicesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. kontrol lera att funktionen är aktive rad

Du kan kontrol lera om funktionen är aktive rad på ditt lagrings konto, du kan använda följande skript:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Du har nu aktiverat funktionen på ditt lagrings konto. Även om funktionen är aktive rad måste du fortfarande utföra ytterligare åtgärder för att kunna använda funktionen på rätt sätt.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Nu har du aktiverat AD-autentisering över SMB och tilldelat en anpassad roll som ger åtkomst till en Azure-filresurs med en AD-identitet. Om du vill ge fler användare åtkomst till din fil resurs följer du anvisningarna i avsnittet [tilldela åtkomst behörigheter](#assign-access-permissions-to-an-identity) för att använda en identitet och [Konfigurera NTFS-behörigheter över SMB](#configure-ntfs-permissions-over-smb) -avsnitt.

## <a name="update-ad-account-password"></a>Uppdatera lösen ord för AD-konto

Om du har registrerat AD-identiteten/kontot som representerar ditt lagrings konto under en ORGANISATIONSENHET som tillämpar lösen ordets giltighets tid måste du rotera lösen ordet innan du anger den högsta ålder för lösen ord. Om du inte uppdaterar lösen ordet för AD-kontot leder det till att autentiseringsfel får åtkomst till Azure-filresurserna.  

Om du vill utlösa lösen ords rotation kan du köra kommandot `Update-AzStorageAccountADObjectPassword` från AzFilesHybrid-modulen. Cmdleten utför åtgärder som liknar lagrings kontots nyckel rotation. Den hämtar den andra Kerberos-nyckeln för lagrings kontot och använder den för att uppdatera lösen ordet för det registrerade kontot i AD. Sedan återskapas mål-Kerberos-nyckeln för lagrings kontot och lösen ordet för det registrerade kontot uppdateras i AD. Du måste köra denna cmdlet i en AD-domänansluten miljö.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Files och hur du använder AD över SMB finns i följande resurser:

- [Översikt över stöd för Azure Files Identity-baserad autentisering för SMB-åtkomst](storage-files-active-directory-overview.md)
- [VANLIGA FRÅGOR OCH SVAR](storage-files-faq.md)
