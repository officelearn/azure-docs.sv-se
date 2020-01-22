---
title: Logga in på den virtuella Windows-datorn i Azure med hjälp av Azure Active Directory (för hands version)
description: Azure AD logga in på en virtuell Azure-dator som kör Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d1fde92e9315e8df3f65b2ab91ced74b377c0a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293461"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logga in på den virtuella Windows-datorn i Azure med Azure Active Directory autentisering (för hands version)

Organisationer kan nu använda Azure Active Directory (AD)-autentisering för sina virtuella Azure-datorer (VM) som kör **Windows Server 2019 Data Center Edition** eller **Windows 10 1809** och senare. Genom att använda Azure AD för att autentisera till virtuella datorer får du ett sätt att centralt styra och tillämpa principer. Med verktyg som Azure rollbaserad Access Control (RBAC) och villkorlig åtkomst i Azure AD kan du kontrol lera vem som har åtkomst till en virtuell dator. Den här artikeln visar hur du skapar och konfigurerar en virtuell Windows Server 2019-dator för att använda Azure AD-autentisering.

|     |
| --- |
| Azure AD-inloggning för virtuella Azure Windows-datorer är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna|
|     |

Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Windows-datorer i Azure, inklusive:

- Använd samma federerade eller hanterade autentiseringsuppgifter för Azure AD som du vanligt vis använder.
- Du behöver inte längre hantera lokala administratörs konton.
- Med Azure RBAC kan du ge rätt åtkomst till virtuella datorer baserat på behov och ta bort den när den inte längre behövs.
- Innan du tillåter åtkomst till en virtuell dator kan villkorlig åtkomst för Azure AD framtvinga ytterligare krav som: 
   - Multifaktorautentisering
   - Kontroll av inloggnings risker
- Automatisera och skala Azure AD-anslutning för virtuella Azure Windows-datorer som ingår i dina VDI-distributioner.

> [!NOTE]
> När du har aktiverat den här funktionen blir dina virtuella Windows-datorer i Azure anslutna till Azure AD. Du kan inte ansluta den till en annan domän som på lokal AD eller Azure AD DS. Om du behöver göra det måste du koppla från den virtuella datorn från din Azure AD-klient genom att avinstallera tillägget.

## <a name="requirements"></a>Krav

### <a name="supported-azure-regions-and-windows-distributions"></a>Azure-regioner och Windows-distributioner som stöds

Följande Windows-distributioner stöds för närvarande i för hands versionen av den här funktionen:

- Windows Server 2019 Datacenter
- Windows 10 1809 och senare

> [!IMPORTANT]
> Fjärr anslutning till virtuella datorer som är anslutna till Azure AD tillåts endast från Windows 10-datorer som är Azure AD-anslutna eller hybrid Azure AD som är anslutna till **samma** katalog som den virtuella datorn. 

Följande Azure-regioner stöds för närvarande i för hands versionen av den här funktionen:

- Alla globala Azure-regioner

> [!IMPORTANT]
> Om du vill använda den här för hands versionen kan du bara distribuera en Windows-distribution som stöds och i en Azure-region som stöds. Funktionen stöds för närvarande inte i Azure Government eller suveräna moln.

### <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina virtuella Windows-datorer i Azure måste du se till att nätverks konfigurationen för virtuella datorer tillåter utgående åtkomst till följande slut punkter via TCP-port 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aktivera Azure AD-inloggning för virtuell Windows-dator i Azure

Om du vill använda Azure AD-inloggning för Windows VM i Azure måste du först aktivera Azure AD-inloggningsnamn för din virtuella Windows-dator och sedan måste du konfigurera RBAC-rolltjänsten för användare som har behörighet att logga in på den virtuella datorn.
Det finns flera sätt att aktivera Azure AD-inloggning för din virtuella Windows-dator:

- Använda Azure Portal upplevelsen när du skapar en virtuell Windows-dator
- Använda Azure Cloud Shell upplevelsen när du skapar en virtuell Windows-dator **eller för en befintlig virtuell Windows-dator**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Använda Azure Portal skapa VM-upplevelse för att aktivera Azure AD-inloggning

Du kan aktivera Azure AD-inloggning för Windows Server 2019 Data Center eller Windows 10 1809 och senare VM-avbildningar. 

Så här skapar du en Windows Server 2019 datacenter-VM i Azure med Azure AD-inloggning: 

1. Logga in på [Azure Portal](https://portal.azure.com)med ett konto som har åtkomst till att skapa virtuella datorer och välj **+ skapa en resurs**.
1. Skriv **Windows Server** i Sök i Sök fältet i Marketplace.
   1. Klicka på **Windows Server** och välj **Windows Server 2019 Data Center** i list rutan Välj en program varu plan.
   1. Klicka på **Skapa**.
1. På fliken "hantering" aktiverar du alternativet att **Logga in med AAD-autentiseringsuppgifter (för hands version)** under avsnittet Azure Active Directory från till **på**.
1. Se till att **systemtilldelad hanterad identitet** under avsnittet identitet är inställt **på on**. Den här åtgärden ska ske automatiskt när du aktiverar inloggning med autentiseringsuppgifter för Azure AD.
1. Gå igenom resten av upplevelsen med att skapa en virtuell dator. Under för hands versionen måste du skapa ett administratörs användar namn och lösen ord för den virtuella datorn.

![Logga in med Azure AD-autentiseringsuppgifter skapa en virtuell dator](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> För att kunna logga in på den virtuella datorn med din Azure AD-autentiseringsuppgift måste du först konfigurera roll tilldelningar för den virtuella datorn enligt beskrivningen i något av avsnitten nedan.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Använda Azure Cloud Shell-upplevelsen för att aktivera Azure AD-inloggning

Azure Cloud Shell är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Välj bara kopierings knappen för att kopiera koden, klistra in den i Cloud Shell och tryck på RETUR för att köra den. Det finns flera olika sätt att öppna Cloud Shell:

Välj prova i det övre högra hörnet i ett kodblock.
Öppna Cloud Shell i din webbläsare.
Välj knappen Cloud Shell på menyn i det övre högra hörnet av [Azure Portal](https://portal.azure.com).

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI-version 2.0.31 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera kan du läsa artikeln [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create). 
1. Skapa en virtuell dator med [AZ VM Create](https://docs.microsoft.com/cli/azure/vm#az-vm-create) med en distribution som stöds i en region som stöds. 
1. Installera tillägget Azure AD login VM. 

I följande exempel distribueras en virtuell dator med namnet myVM som använder Win2019Datacenter i en resurs grupp med namnet myResourceGroup i usasödracentrala-regionen. I följande exempel kan du ange dina egna resurs grupper och namn på virtuella datorer efter behov.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Det krävs att du aktiverar systemtilldelad hanterad identitet på den virtuella datorn innan du installerar tillägget Azure AD login VM.

Det tar några minuter att skapa den virtuella datorn och stödresurser.

Slutligen installerar du tillägget Azure AD login VM för att aktivera Azure AD-inloggning för Windows VM. VM-tillägg är små program som tillhandahåller konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer. Använd [AZ VM Extension](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) set för att installera AADLoginForWindows-tillägget på den virtuella datorn med namnet MyVM i myResourceGroup-resurs gruppen:

> [!NOTE]
> Du kan installera AADLoginForWindows-tillägget på en befintlig Windows Server 2019 eller Windows 10 1809 och senare VM för att aktivera det för Azure AD-autentisering. Ett exempel på AZ CLI visas nedan.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`provisioningState` `Succeeded` visas när tillägget har installerats på den virtuella datorn.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera roll tilldelningar för den virtuella datorn

Nu när du har skapat den virtuella datorn måste du konfigurera en Azure RBAC-princip för att fastställa vem som kan logga in på den virtuella datorn. Två RBAC-roller används för att auktorisera VM-inloggning:

- **Administratörs inloggning för virtuell dator**: användare med den här rollen som tilldelas kan logga in på en virtuell Azure-dator med administratörs behörighet.
- **Användar inloggning för virtuell dator**: användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med vanliga användar behörigheter.

> [!NOTE]
> Om du vill att en användare ska kunna logga in på den virtuella datorn via RDP måste du tilldela antingen rollen Administratörs inloggning för virtuell dator eller användar inloggning för virtuell dator. En Azure-användare med rollen ägare eller deltagare som har tilldelats en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via RDP. Detta är att tillhandahålla granskad åtskillnad mellan den uppsättning personer som kontrollerar virtuella datorer och den uppsättning personer som kan komma åt virtuella datorer.

Det finns flera sätt att konfigurera roll tilldelningar för virtuella datorer:

- Använda Azure AD Portal-upplevelsen
- Använda Azure Cloud Shell upplevelse

### <a name="using-azure-ad-portal-experience"></a>Använda Azure AD Portal-upplevelse

Konfigurera roll tilldelningar för dina Azure AD-aktiverade Windows Server 2019 datacenter-virtuella datorer:

1. Gå till översikts sidan för den aktuella virtuella datorn
1. Välj **åtkomst kontroll (IAM)** från meny alternativen
1. Välj **Lägg till**, **Lägg till roll tilldelning** för att öppna fönstret Lägg till roll tilldelning.
1. I list rutan **roll** väljer du en roll som **Administratörs inloggning för virtuell** dator eller **användar inloggning för virtuell dator**.
1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i fältet **Välj** . Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.
1. Välj **Spara**för att tilldela rollen.

Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

![Tilldela roller till användare som ska ha åtkomst till den virtuella datorn](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Använda Azure Cloud Shell upplevelse

I följande exempel används [AZ roll tilldelning skapa](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) för att tilldela den virtuella datorns administratörs inloggnings roll till den virtuella datorn för din aktuella Azure-användare. Användar namnet för ditt aktiva Azure-konto hämtas med [AZ-kontot show](https://docs.microsoft.com/cli/azure/account#az-account-show), och omfånget ställs in på den virtuella datorn som skapades i ett föregående steg med [AZ VM show](https://docs.microsoft.com/cli/azure/vm#az-vm-show). Omfattningen kan också tilldelas till en resurs grupp eller prenumerations nivå, och normala behörigheter för RBAC-arv gäller. Mer information finns i [rollbaserade åtkomst kontroller](../../virtual-machines/linux/login-using-aad.md).

```AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och inloggnings användar domän inte matchar, måste du ange objekt-ID: t för ditt användar konto med `--assignee-object-id`, inte bara användar namnet för `--assignee`. Du kan hämta objekt-ID: t för ditt användar konto med [AZ AD User List](https://docs.microsoft.com/cli/azure/ad/user#az-ad-user-list).

Mer information om hur du använder RBAC för att hantera åtkomst till dina Azure-prenumerations resurser finns i följande artiklar:

- [Hantera åtkomst till Azure-resurser med RBAC och Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [Hantera åtkomst till Azure-resurser med RBAC och Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).

## <a name="using-conditional-access"></a>Använda villkorlig åtkomst

Du kan tillämpa principer för villkorlig åtkomst, till exempel Multi-Factor Authentication eller användar inloggnings risker innan du auktoriserar åtkomsten till virtuella Windows-datorer i Azure som är aktiverade med Azure AD-inloggning. Om du vill tillämpa principen för villkorlig åtkomst måste du välja "Azure Windows VM-inloggning" i appen molnappar eller åtgärder tilldelning och sedan använda inloggnings risker som ett villkor och/eller kräva multifaktorautentisering som en bevilja åtkomst kontroll. 

> [!NOTE]
> Om du använder "Kräv Multi-Factor Authentication" som en beviljande åtkomst kontroll för att begära åtkomst till appen "Azure Windows VM-inloggning" måste du ange Multi-Factor Authentication-anspråk som en del av klienten som initierar RDP-sessionen till mål Windows Virtuell dator i Azure. Det enda sättet att åstadkomma detta på en Windows 10-klient är att använda Windows Hello för företag-PIN-kod eller bio metrisk autentisering med RDP-klienten. Stöd för bio metrisk autentisering har lagts till i RDP-klienten i Windows 10 version 1809. Fjärr skrivbord med Windows Hello för företag-autentisering är bara tillgängligt för distributioner som använder certifikat förtroende modell och som för närvarande inte är tillgängligt för nyckel förtroende modell.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Logga in med autentiseringsuppgifter för Azure AD till en virtuell Windows-dator

> [!IMPORTANT]
> Fjärr anslutning till virtuella datorer som är anslutna till Azure AD tillåts endast från Windows 10-datorer som är Azure AD-anslutna eller hybrid Azure AD som är anslutna till **samma** katalog som den virtuella datorn. För RDP med Azure AD-autentiseringsuppgifter måste användaren dessutom tillhöra en av de två RBAC-rollerna, en virtuell dator Administratörs inloggning eller användar inloggning för virtuella datorer. För närvarande går det inte att använda Azure-skydds för att logga in med Azure Active Directory-autentisering med AADLoginForWindows-tillägget. Endast direkt RDP stöds.

Logga in på din virtuella Windows Server 2019-dator med hjälp av Azure AD: 

1. Gå till översikts sidan för den virtuella dator som har Aktiver ATS med Azure AD-inloggning.
1. Välj **Anslut** för att öppna bladet Anslut till virtuell dator.
1. Välj **Ladda ned RDP-fil**.
1. Välj **Öppna** för att starta anslutning till fjärrskrivbord-klienten.
1. Välj **Anslut** för att starta dialog rutan Windows-inloggning.
1. Logga in med dina autentiseringsuppgifter för Azure AD.

Du är nu inloggad på den virtuella Windows Server 2019 Azure-datorn med roll behörigheter som tilldelade, till exempel VM-användare eller VM-administratör. 

> [!NOTE]
> Du kan spara. RDP-fil lokalt på datorn för att starta framtida fjärr skrivbords anslutningar till den virtuella datorn i stället för att behöva gå till översikts sidan för den virtuella datorn i Azure Portal och med alternativet Connect.

## <a name="troubleshoot"></a>Felsökning

### <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

AADLoginForWindows-tillägget måste kunna installeras för att den virtuella datorn ska kunna slutföra Azure AD Join-processen. Utför följande steg om det inte går att installera VM-tillägget korrekt.

1. RDP till den virtuella datorn med det lokala administratörs kontot och granska CommandExecution. log under  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Om tillägget startas om efter det första felet sparas loggen med distributions felet som CommandExecution_YYYYMMDDHHMMSSSSS. log. 

1. Öppna en kommando tolk på den virtuella datorn och verifiera dessa frågor mot den Instance Metadata Service-slutpunkt (IMDS) som körs på Azure-värden returnerar:

   | Kommando som ska köras | Förväntad utdata |
   | --- | --- |
   | klammer-H metadata: true "http://169.254.169.254/metadata/instance?api-version=2017-08-01 " | Korrigera information om den virtuella Azure-datorn |
   | klammer-H metadata: true "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01 " | Giltigt klient-ID som är associerat med Azure-prenumerationen |
   | klammer-H metadata: true "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01 " | Giltig åtkomsttoken utfärdat av Azure Active Directory för den hanterade identitet som har tilldelats den här virtuella datorn |

   > [!NOTE]
   > Åtkomsttoken kan avkodas med hjälp av ett verktyg som [http://calebb.net/](http://calebb.net/). Verifiera att "AppID" i åtkomsttoken matchar den hanterade identitet som tilldelats den virtuella datorn.

1. Se till att de nödvändiga slut punkterna är tillgängliga från den virtuella datorn med hjälp av kommando raden:
   
   - spiral-https:\//login.microsoftonline.com/-D –
   - spiral-https:\//login.microsoftonline.com/`<TenantID>`/-D –

   > [!NOTE]
   > Ersätt `<TenantID>` med det Azure AD-klient-ID som är associerat med Azure-prenumerationen.

   - spiral-https:\//enterpriseregistration.windows.net/-D-
   - spiral-https:\//device.login.microsoftonline.com/-D-
   - spiral-https:\//pas.windows.net/-D-

1. Enhetens tillstånd kan visas genom att köra `dsregcmd /status`. Målet är för enhets tillstånd att visas som `AzureAdJoined : YES`.

   > [!NOTE]
   > Azure AD Join-aktivitet samlas in i logg boken under användar enhetens Registration\Admin logg.

Om AADLoginForWindows-tillägget Miss lyckas med en viss felkod kan du utföra följande steg:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: AADLoginForWindows-tillägget kan inte installeras med Terminal-felkoden "1007" och avslutnings kod:-2145648574.

Den här avslutnings koden översätts till DSREG_E_MSI_TENANTID_UNAVAILABLE eftersom tillägget inte kan skicka frågor till information om Azure AD-klienten.

1. Kontrol lera att den virtuella Azure-datorn kan hämta TenantID från Instance Metadata Service.

   - RDP till den virtuella datorn som lokal administratör och kontrol lera att slut punkten returnerar giltigt klient-ID genom att köra det här kommandot från en upphöjd kommando rad på den virtuella datorn:
      
      - klammer – H metadata: true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Den virtuella dator administratören försöker installera AADLoginForWindows-tillägget, men en systemtilldelad hanterad identitet har inte aktiverat den virtuella datorn först. Gå till den virtuella datorns identitets blad. Från fliken systemtilldelad kontrollerar du att status är växlad till på.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: AADLoginForWindows-tillägget kan inte installeras med avslutnings koden:-2145648607

Den här avslutnings koden översätts till DSREG_AUTOJOIN_DISC_FAILED eftersom tillägget inte kan komma åt https://enterpriseregistration.windows.net -slutpunkten.

1. Verifiera att de nödvändiga slut punkterna är tillgängliga från den virtuella datorn med hjälp av kommando raden:

   - spiral-https:\//login.microsoftonline.com/-D –
   - spiral-https:\//login.microsoftonline.com/`<TenantID>`/-D –
   
   > [!NOTE]
   > Ersätt `<TenantID>` med det Azure AD-klient-ID som är associerat med Azure-prenumerationen. Om du behöver hitta klient-ID: t kan du hovra över ditt konto namn för att hämta katalog-ID eller välja Azure Active Directory > Egenskaper > katalog-ID i Azure Portal.

   - spiral-https:\//enterpriseregistration.windows.net/-D-
   - spiral-https:\//device.login.microsoftonline.com/-D-
   - spiral-https:\//pas.windows.net/-D-

1. Om något av kommandona Miss lyckas med "Det gick inte att matcha värd `<URL>`", kan du prova att köra det här kommandot för att avgöra vilken DNS-server som används av den virtuella datorn.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Ersätt `<URL>` med de fullständigt kvalificerade domän namnen som används av slut punkterna, till exempel "login.microsoftonline.com".

1. Sedan kan du se om du anger en offentlig DNS-server så att kommandot kan utföras:

   `nslookup <URL> 208.67.222.222`

1. Om det behövs ändrar du DNS-servern som är tilldelad den nätverks säkerhets grupp som den virtuella Azure-datorn tillhör.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: AADLoginForWindows-tillägget kan inte installeras med avslutnings koden: 51

Slutkod 51 översätts till "det här tillägget stöds inte på den virtuella datorns operativ system".

Vid en offentlig för hands version är AADLoginForWindows-tillägget endast avsett att installeras på Windows Server 2019 eller Windows 10 (version 1809 eller senare). Kontrol lera att Windows-versionen stöds. Om Windows-versionen inte stöds avinstallerar du VM-tillägget.

### <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggnings problem

Några vanliga fel när du försöker använda RDP med Azure AD-autentiseringsuppgifter inkluderar inga RBAC-roller tilldelade, otillåten klient eller 2FA inloggnings metod krävs. Använd följande information för att åtgärda problemen.

Du kan visa enhets-och SSO-status genom att köra `dsregcmd /status`. Målet är för enhets tillstånd att visas som `AzureAdJoined : YES` och `SSO State` för att Visa `AzureAdPrt : YES`.

Dessutom registreras RDP-inloggning med Azure AD-konton i logg boken under händelse loggarna för AAD\Operational.

#### <a name="rbac-role-not-assigned"></a>RBAC-rollen har inte tilldelats

Om du ser följande fel meddelande när du startar en fjärr skrivbords anslutning till den virtuella datorn: 

- Ditt konto har kon figurer ATS för att förhindra att du använder den här enheten. Kontakta system administratören om du vill ha mer information

![Ditt konto har kon figurer ATS för att förhindra att du använder den här enheten.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Kontrol lera att du har [KONFIGURERAT RBAC-principer](../../virtual-machines/linux/login-using-aad.md) för den virtuella datorn som ger användaren antingen Administratörs inloggning för den virtuella datorn eller användar inloggnings rollen för den virtuella datorn:
 
#### <a name="unauthorized-client"></a>Obehörig klient

Om du ser följande fel meddelande när du startar en fjärr skrivbords anslutning till den virtuella datorn: 

- Autentiseringsuppgifterna fungerade inte

![Autentiseringsuppgifterna fungerade inte](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Kontrol lera att den Windows 10-dator som du använder för att initiera fjärr skrivbords anslutningen är antingen en Azure AD-ansluten eller en hybrid Azure AD som är ansluten till samma Azure AD-katalog som den virtuella datorn är ansluten till. Mer information om enhets identitet finns i artikeln [Vad är en enhets identitet](https://docs.microsoft.com/azure/active-directory/devices/overview).

> [!NOTE]
> Windows 10 20H1 kommer att lägga till stöd för Azure AD-registrerade datorer för att initiera anslutning till fjärr skrivbord till din virtuella dator. Delta i Windows Insider-programmet och utforska nya funktioner i Windows 10.

Kontrol lera också att AADLoginForWindows-tillägget inte har avinstallerats efter att Azure AD Join har slutförts.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-inloggnings metod krävs

Om du ser följande fel meddelande när du startar en fjärr skrivbords anslutning till den virtuella datorn: 

- Den inloggnings metod som du försöker använda är inte tillåten. Försök med en annan inloggnings metod eller kontakta system administratören.

![Den inloggnings metod som du försöker använda är inte tillåten.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Om du har konfigurerat en princip för villkorlig åtkomst som kräver Multi-Factor Authentication (MFA) innan du kan komma åt resursen, måste du se till att Windows 10-datorn som initierar fjärr skrivbords anslutningen till den virtuella datorn loggar in med ett starkt autentiseringsmetod som Windows Hello. Om du inte använder en stark autentiseringsmetod för fjärr skrivbords anslutningen visas föregående fel.

Om du inte har distribuerat Windows Hello för företag och om det inte är ett alternativ för tillfället, kan du utesluta MFA-kravet genom att konfigurera principen för villkorlig åtkomst som utesluter "Azure Windows VM-inloggning"-appen från listan över molnappar som kräver MFA. Mer information om Windows Hello för företag finns i [Översikt över Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Windows Hello för företag PIN-autentisering med RDP har stöd av Windows 10 för flera versioner, men stöd för bio metrisk autentisering med RDP lades till i Windows 10 version 1809. Användning av Windows Hello för företag-autentisering under RDP är endast tillgängligt för distributioner som använder certifikat förtroende modell och som för närvarande inte är tillgängligt för nyckel förtroende modell.
 
## <a name="preview-feedback"></a>Förhandsgranska feedback

Dela din feedback om den här för hands versions funktionen eller rapportera problem med hjälp av den i [Azure AD feedback-forumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
