---
title: Logga in på Windows virtuell dator i Azure med Azure Active Directory (förhandsversion)
description: Azure AD loggar in på en Virtuell Azure-dator som kör Windows
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
ms.openlocfilehash: 88ae3c45126403161e35ec46e5ccc2666c3edb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050064"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logga in på Windows virtuell dator i Azure med Azure Active Directory-autentisering (förhandsversion)

Organisationer kan nu använda Azure Active Directory (AD) autentisering för sina Virtuella Azure-datorer (VMs) som kör **Windows Server 2019 Datacenter edition** eller Windows **10 1809** och senare. Genom att använda Azure AD för att autentisera till virtuella datorer kan du styra och tillämpa principer centralt. Med verktyg som RBAC (Azure Role-Based Access Control) och Azure AD Conditional Access kan du styra vem som kan komma åt en virtuell dator. Den här artikeln visar hur du skapar och konfigurerar en virtuell windows server 2019-dator för att använda Azure AD-autentisering.

|     |
| --- |
| Azure AD-inloggning för virtuella Azure Windows-datorer är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Windows-datorer i Azure, inklusive:

- Använd samma federerade eller hanterade Azure AD-autentiseringsuppgifter som du normalt använder.
- Du behöver inte längre hantera lokala administratörskonton.
- Azure RBAC kan du bevilja lämplig åtkomst till virtuella datorer baserat på behov och ta bort den när det inte längre behövs.
- Innan du tillåter åtkomst till en virtuell dator kan Azure AD Villkorlig åtkomst framtvinga ytterligare krav, till exempel: 
   - Multi-Factor Authentication
   - Riskkontroll för inloggning
- Automatisera och skala Azure AD-koppling till virtuella Azure Windows-datorer som ingår i dina VDI-distributioner.

> [!NOTE]
> När du har aktiverat den här funktionen kommer dina virtuella Windows-datorer i Azure att azure AD-anslutna. Du kan inte ansluta den till en annan domän som lokala AD eller Azure AD DS. Om du behöver göra det måste du koppla från den virtuella datorn från din Azure AD-klient genom att avinstallera tillägget.

## <a name="requirements"></a>Krav

### <a name="supported-azure-regions-and-windows-distributions"></a>Azure-regioner och Windows-distributioner som stöds

Följande Windows-distributioner stöds för närvarande under förhandsversionen av den här funktionen:

- Windows Server 2019 Datacenter
- Windows 10 1809 och senare

> [!IMPORTANT]
> Fjärranslutning till virtuella datorer som är anslutna till Azure AD tillåts endast från Windows 10-datorer som är Azure AD-anslutna eller hybrid Azure AD anslutna till **samma** katalog som den virtuella datorn. 

Följande Azure-regioner stöds för närvarande under förhandsversionen av den här funktionen:

- Alla globala Azure-regioner

> [!IMPORTANT]
> Om du vill använda den här förhandsversionen distribuerar du bara en Windows-distribution som stöds och i en Azure-region som stöds. Funktionen stöds för närvarande inte i Azure Government eller suveräna moln.

### <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina Virtuella Windows-datorer i Azure måste du se till att nätverkskonfigurationen för dina virtuella datorer tillåter utgående åtkomst till följande slutpunkter via TCP-port 443:

- https:\//enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https:\//device.login.microsoftonline.com
- https:\//pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aktivera Azure AD-inloggning för Windows VM i Azure

Om du vill använda Azure AD-inloggning för Windows VM i Azure måste du först aktivera Azure AD-inloggningsalternativet för din Windows VM och sedan måste du konfigurera RBAC-rolltilldelningar för användare som har behörighet att logga in på den virtuella datorn.
Det finns flera sätt att aktivera Azure AD-inloggning för din Virtuella Windows-dator:

- Använda Azure-portalen när du skapar en Windows VM
- Använda Azure Cloud Shell-upplevelsen när du skapar en Virtuell **Windows-dator eller för en befintlig Windows-virtuell dator**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Använda Azure portal skapa VM-upplevelse för att aktivera Azure AD-inloggning

Du kan aktivera Azure AD-inloggning för Windows Server 2019 Datacenter- eller Windows 10 1809- och senare VM-avbildningar. 

Så här skapar du en virtuell virtuell Windows Server 2019 Datacenter i Azure med Azure AD-inloggning: 

1. Logga in på [Azure-portalen](https://portal.azure.com), med ett konto som har åtkomst till att skapa virtuella datorer, och välj **+ Skapa en resurs**.
1. Skriv **Windows Server** i Sök i sökfältet på Marketplace.
   1. Klicka på **Windows Server** och välj Windows **Server 2019 Datacenter** från Välj en listruta för programvaruabonnemang.
   1. Klicka på **Skapa**.
1. På fliken "Hantering" aktiverar du alternativet att **logga in med AAD-autentiseringsuppgifter (förhandsversion)** under avsnittet Azure Active Directory från Av till **På**.
1. Kontrollera att **systemtilldelade hanterade identitet** under avsnittet Identitet är inställt **på På**. Den här åtgärden ska ske automatiskt när du aktiverar inloggning med Azure AD-autentiseringsuppgifter.
1. Gå igenom resten av upplevelsen av att skapa en virtuell dator. Under den här förhandsversionen måste du skapa ett användarnamn och lösenord för administratören för den virtuella datorn.

![Logga in med Azure AD-autentiseringsuppgifter skapar en virtuell dator](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> För att logga in på den virtuella datorn med din Azure AD-autentiseringsuppgifter måste du först konfigurera rolltilldelningar för den virtuella datorn enligt beskrivningen i ett av avsnitten nedan.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Använda Azure Cloud Shell-upplevelsen för att aktivera Azure AD-inloggning

Azure Cloud Shell är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Välj knappen Kopiera för att kopiera koden, klistra in den i Cloud Shell och tryck på RETUR för att köra den. Det finns flera olika sätt att öppna Cloud Shell:

Välj Prova i det övre högra hörnet av ett kodblock.
Öppna Cloud Shell i din webbläsare.
Välj knappen Cloud Shell på menyn längst upp till höger i [Azure Portal](https://portal.azure.com).

Om du väljer att installera och använda CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0.31 eller senare. Kör az --version för att se versionen. Om du behöver installera eller uppgradera läser du artikeln [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). 
1. Skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) med hjälp av en distribution som stöds i en region som stöds. 
1. Installera tillägget Azure AD-inloggning vm. 

I följande exempel distribueras en virtuell dator med namnet myVM som använder Win2019Datacenter, till en resursgrupp med namnet myResourceGroup i regionen southcentralus. I följande exempel kan du ange dina egna resursgrupp- och VM-namn efter behov.

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
> Det krävs att du aktiverar systemtilldelade hanterade identitet på din virtuella dator innan du installerar Azure AD-tillägget för inloggning virtuell dator.

Det tar några minuter att skapa den virtuella datorn och stödresurser.

Installera slutligen azure AD-tillägget för att aktivera Azure AD-inloggning för Windows VM. VM-tillägg är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distributionen på virtuella Azure-datorer. Använd [az vm-tillägget](/cli/azure/vm/extension#az-vm-extension-set) för att installera tillägget AADLoginForWindows på den virtuella datorn med namnet myVM i resursgruppen myResourceGroup:

> [!NOTE]
> Du kan installera AADLoginForWindows-tillägget på ett befintligt Windows Server 2019 eller Windows 10 1809 och senare VM för att aktivera det för Azure AD-autentisering. Ett exempel på AZ CLI visas nedan.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Av `provisioningState` `Succeeded` visas när tillägget är installerat på den virtuella datorn.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera rolltilldelningar för den virtuella datorn

Nu när du har skapat den virtuella datorn måste du konfigurera Azure RBAC-principen för att avgöra vem som kan logga in på den virtuella datorn. Två RBAC-roller används för att auktorisera VM-inloggning:

- **Inloggning för administratör för virtuell**dator: Användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med administratörsbehörighet.
- **Användarlogin för virtuella**datorer: Användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med vanliga användarbehörigheter.

> [!NOTE]
> Om du vill att en användare ska kunna logga in på den virtuella datorn via RDP måste du tilldela rollen inloggning av administratör för virtuell dator eller användarlogin för virtuell dator. En Azure-användare med ägar- eller deltagarrollerna som tilldelats för en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via RDP. Detta för att ge en granskad separation mellan den uppsättning personer som styr virtuella datorer jämfört med den uppsättning personer som kan komma åt virtuella datorer.

Det finns flera sätt att konfigurera rolltilldelningar för virtuell dator:

- Använda Azure AD Portal-upplevelsen
- Använda Azure Cloud Shell-upplevelsen

### <a name="using-azure-ad-portal-experience"></a>Använda Azure AD Portal-upplevelse

Så här konfigurerar du rolltilldelningar för dina Virtuella Azure AD-aktiverade virtuella datorer med Windows Server 2019 Datacenter:

1. Navigera till den specifika översiktssidan för virtuella datorer
1. Välj **Åtkomstkontroll (IAM)** på menyalternativen
1. Välj **Lägg till**, Lägg till **rolltilldelning** för att öppna fönstret Lägg till rolltilldelning.
1. I listrutan **Roll** väljer du en roll som **administratörsinloggning för virtuell dator** eller **användarinloggning för virtuell dator**.
1. Välj en användare, grupp, tjänsthuvudnamn eller hanterad identitet i fältet **Välj.** Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.
1. Välj **Spara**om du vill tilldela rollen.

Efter en stund tilldelas säkerhetsobjektet rollen i det valda scopet.

![Tilldela roller till användare som kommer åt den virtuella datorn](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Använda Azure Cloud Shell-upplevelsen

I följande exempel används [az-rolltilldelningsskapande](/cli/azure/role/assignment#az-role-assignment-create) för att tilldela rollen inloggning till den virtuella datorns administratörslogin till den virtuella datorn för din nuvarande Azure-användare. Användarnamnet för ditt aktiva Azure-konto hämtas med [az-kontoprogram](/cli/azure/account#az-account-show)och scopet är inställt på den virtuella datorn som skapats i ett tidigare steg med [az vm show](/cli/azure/vm#az-vm-show). Scopet kan också tilldelas på en resursgrupp eller prenumerationsnivå och normala RBAC-arvsbehörigheter gäller. Mer information finns i [Rollbaserade åtkomstkontroller](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och inloggningsanvändardomän inte matchar måste du ange `--assignee-object-id`objekt-ID för `--assignee`ditt användarkonto med inte bara användarnamnet för . Du kan hämta objekt-ID:t för ditt användarkonto med [az ad-användarlista](/cli/azure/ad/user#az-ad-user-list).

Mer information om hur du använder RBAC för att hantera åtkomsten till dina Azure-prenumerationsresurser finns i följande artiklar:

- [Hantera åtkomst till Azure-resurser med RBAC och Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](/azure/role-based-access-control/role-assignments-portal)
- [Hantera åtkomst till Azure-resurser med RBAC och Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="using-conditional-access"></a>Använda villkorlig åtkomst

Du kan tillämpa principer för villkorlig åtkomst, till exempel multifaktorautentisering eller användaranmälningsriskkontroll innan du godkänner åtkomst till virtuella Windows-datorer i Azure som är aktiverade med Azure AD-inloggning. Om du vill tillämpa principen för villkorlig åtkomst måste du välja "Azure Windows VM Sign-In"-app från tilldelningsalternativet för molnappar eller åtgärder och sedan använda inloggningsrisk som ett villkor och/eller kräva multifaktorautentisering som en bidragsåtkomstkontroll. 

> [!NOTE]
> Om du använder "Kräv multifaktorautentisering" som en bidragsåtkomstkontroll för att begära åtkomst till appen "Azure Windows VM Sign-In", måste du ange multifaktorautentiseringsanspråk som en del av klienten som initierar RDP-sessionen till målet Windows VIRTUELLD dator i Azure. Det enda sättet att uppnå detta på en Windows 10-klient är att använda Windows Hello för företag PIN-kod eller biometrisk authenication med RDP-klienten. Stöd för biometrisk autentisering har lagts till i RDP-klienten i Windows 10 version 1809. Fjärrskrivbord med Windows Hello för företag-autentisering är endast tillgängligt för distributioner som använder cert-förtroendemodell och som för närvarande inte är tillgängligt för nyckelförtroendemodellen.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Logga in med Azure AD-autentiseringsuppgifter på en Virtuell Windows-dator

> [!IMPORTANT]
> Fjärranslutning till virtuella datorer som är anslutna till Azure AD tillåts endast från Windows 10-datorer som är Azure AD-anslutna eller hybrid Azure AD anslutna till **samma** katalog som den virtuella datorn. Dessutom, till RDP med Hjälp av Azure AD-autentiseringsuppgifter, måste användaren tillhöra en av de två RBAC-rollerna, Virtual Machine Administrator Login eller Virtual Machine User Login. För närvarande kan Inte Azure Bastion användas för att logga in med Azure Active Directory-autentisering med tillägget AADLoginForWindows. Endast direkt RDP stöds.

Så här loggar du in på den virtuella windows server 2019-datorn med Azure AD: 

1. Navigera till översiktssidan för den virtuella datorn som har aktiverats med Azure AD-inloggning.
1. Välj **Anslut** för att öppna bladet Anslut till virtuell dator.
1. Välj **Ladda ned RDP-fil**.
1. Välj **Öppna** om du vill starta klienten för anslutning till fjärrskrivbord.
1. Välj **Anslut** om du vill starta inloggningsdialogrutan för Windows.
1. Logga in med dina Azure AD-autentiseringsuppgifter.

Du är nu inloggad på den virtuella Datorn i Windows Server 2019 med rollbehörigheterna som tilldelade, till exempel VM-användare eller VM-administratör. 

> [!NOTE]
> Du kan spara . RDP-filen lokalt på datorn för att starta framtida fjärrskrivbordsanslutningar till din virtuella dator i stället för att behöva navigera till översiktssidan för virtuella datorer i Azure-portalen och använda anslutningsalternativet.

## <a name="troubleshoot"></a>Felsöka

### <a name="troubleshoot-deployment-issues"></a>Felsöka distributionsproblem

Tillägget AADLoginForWindows måste installeras för att den virtuella datorn ska kunna slutföra Azure AD-anslutningsprocessen. Utför följande steg om vm-tillägget inte kan installeras korrekt.

1. RDP till den virtuella datorn med det lokala administratörskontot och undersöka CommandExecuti'n.log under  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Om tillägget startas om efter det första felet sparas loggen med distributionsfelet som CommandExecution_YYYYMMDDHHMMSSSSS.log. "
1. Öppna en kommandotolk på den virtuella datorn och verifiera dessa frågor mot instansmetadatatjänstens slutpunkt (IMDS) som körs på Azure-värdreturen:

   | Kommando för att köra | Förväntad utdata |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Korrekt information om den virtuella Azure-datorn |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Giltigt klient-ID som är associerat med Azure-prenumerationen |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Giltig åtkomsttoken utfärdad av Azure Active Directory för den hanterade identitet som har tilldelats den här virtuella datorn |

   > [!NOTE]
   > Åtkomsttoken kan avkodas med [http://calebb.net/](http://calebb.net/)ett verktyg som . Verifiera "appid" i åtkomsttoken matchar den hanterade identiteten som tilldelats den virtuella datorn.

1. Kontrollera att de slutpunkter som krävs är tillgängliga från den virtuella datorn med hjälp av kommandoraden:
   
   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\//login.microsoftonline.com/`<TenantID>`/ -D –

   > [!NOTE]
   > Ersätt `<TenantID>` med Azure AD-klient-ID som är associerat med Azure-prenumerationen.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Enhetstillståndet kan visas `dsregcmd /status`genom att köra . Målet är att Enhetstillstånd `AzureAdJoined : YES`ska visas som .

   > [!NOTE]
   > Azure AD-anslutningsaktivitet fångas in i Loggboken under loggen User Device Registration\Admin.

Om tillägget AADLoginForWindows misslyckas med viss felkod kan du utföra följande steg:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: AADLoginForWindows-tillägget kan inte installeras med terminalfelkoden "1007" och Exit-kod: -2145648574.

Den här avslutningskoden översätts till DSREG_E_MSI_TENANTID_UNAVAILABLE eftersom tillägget inte kan fråga Azure AD-klientinformationen.

1. Verifiera att Azure VM kan hämta Klient-ID från instansmetadatatjänsten.

   - RDP till den virtuella datorn som lokal administratör och verifiera slutpunkten returnerar giltigt klient-ID genom att köra det här kommandot från en upphöjd kommandorad på den virtuella datorn:
      
      - curl -H Metadata:truehttp://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. Vm-administratören försöker installera tillägget AADLoginForWindows, men en systemtilldelad hanterad identitet har inte aktiverat den virtuella datorn först. Navigera till den virtuella datorns identitetsblad. Kontrollera status på fliken System tilldelad.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: AADLoginForWindows-tillägget installeras inte med Exit-kod: -2145648607

Den här avslutningskoden översätts till DSREG_AUTOJOIN_DISC_FAILED eftersom tillägget `https://enterpriseregistration.windows.net` inte kan nå slutpunkten.

1. Kontrollera att de slutpunkter som krävs är tillgängliga från den virtuella datorn med hjälp av kommandoraden:

   - curl https:\//login.microsoftonline.com/ -D –
   - curl https:\//login.microsoftonline.com/`<TenantID>`/ -D –
   
   > [!NOTE]
   > Ersätt `<TenantID>` med Azure AD-klient-ID som är associerat med Azure-prenumerationen. Om du behöver hitta klient-ID:t kan du hovra över ditt kontonamn för att hämta katalogen/klient-ID:t eller välja Azure Active Directory > Properties > Directory ID i Azure-portalen.

   - curl https:\//enterpriseregistration.windows.net/ -D -
   - curl https:\//device.login.microsoftonline.com/ -D -
   - curl https:\//pas.windows.net/ -D -

1. Om något av kommandona misslyckas med `<URL>`"Det gick inte att lösa värden" kan du prova att köra det här kommandot för att fastställa den DNS-server som används av den virtuella datorn.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Ersätt `<URL>` med de fullständigt kvalificerade domännamn som används av slutpunkterna, till exempel "login.microsoftonline.com".

1. Se sedan om du kan lyckas med kommandot om du anger en offentlig DNS-server:

   `nslookup <URL> 208.67.222.222`

1. Om det behövs ändrar du DNS-servern som har tilldelats den nätverkssäkerhetsgrupp som den virtuella Azure-datorn tillhör.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: AADLoginForWindows-tillägget installeras inte med Exit-kod: 51

Avsluta kod 51 översätts till "Det här tillägget stöds inte på den virtuella datorns operativsystem".

Vid offentlig förhandsversion är AADLoginForWindows-tillägget endast avsett att installeras på Windows Server 2019 eller Windows 10 (Build 1809 eller senare). Kontrollera att windowsversionen stöds. Om versionen av Windows inte stöds avinstallerar du vm-tillägget.

### <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggningsproblem

Några vanliga fel när du försöker RDP med Azure AD-autentiseringsuppgifter inkluderar inga RBAC-roller som tilldelats, obehörig klient eller 2FA-inloggningsmetod som krävs. Använd följande information för att korrigera dessa problem.

Enheten och SSO-tillståndet kan visas `dsregcmd /status`genom att köra . Målet är att Enhetstillstånd `AzureAdJoined : YES` `SSO State` ska `AzureAdPrt : YES`visas som och visa .

Rdp-inloggning med Azure AD-konton fångas också in i Loggboken under händelseloggarna AAD\Operational.

#### <a name="rbac-role-not-assigned"></a>RBAC-roll har inte tilldelats

Om följande felmeddelande visas när du startar en fjärrskrivbordsanslutning till den virtuella datorn: 

- Ditt konto är konfigurerat för att hindra dig från att använda den här enheten. Kontakta systemadministratören om du vill ha mer information

![Ditt konto är konfigurerat för att hindra dig från att använda den här enheten.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Kontrollera att du har [konfigurerat RBAC-principer](../../virtual-machines/linux/login-using-aad.md) för den virtuella datorn som ger användaren rollen administratörsinloggning för virtuell dator eller användarlogin för virtuell dator:
 
#### <a name="unauthorized-client"></a>Obehörig klient

Om följande felmeddelande visas när du startar en fjärrskrivbordsanslutning till den virtuella datorn: 

- Dina autentiseringsuppgifter fungerade inte

![Dina autentiseringsuppgifter fungerade inte](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Kontrollera att den Windows 10-dator som du använder för att initiera fjärrskrivbordsanslutningen är en som antingen är Azure AD-ansluten eller hybrid Azure AD ansluten till samma Azure AD-katalog där den virtuella datorn är ansluten till. Mer information om enhetsidentitet finns i artikeln [Vad är en enhetsidentitet](/azure/active-directory/devices/overview).

> [!NOTE]
> Windows 10 20H1, lägger till stöd för Azure AD-registrerad dator för att initiera fjärrskrivbordsanslutning till din virtuella dator. Gå med i Windows Insider Program för att prova det här och utforska nya funktioner i Windows 10.

Kontrollera också att tillägget AADLoginForWindows inte har avinstallerats när Azure AD-anslutning har slutförts.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-inloggningsmetod krävs

Om följande felmeddelande visas när du startar en fjärrskrivbordsanslutning till den virtuella datorn: 

- Inloggningsmetoden som du försöker använda är inte tillåten. Prova med en annan inloggningsmetod eller kontakta systemadministratören.

![Inloggningsmetoden som du försöker använda är inte tillåten.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Om du har konfigurerat en princip för villkorlig åtkomst som kräver MFA (Multifaktor authentication) innan du kan komma åt resursen måste du se till att Windows 10-datorn som initierar fjärrskrivbordsanslutningen till den virtuella datorn loggar in med en stark autentiseringsmetod som Windows Hello. Om du inte använder en stark autentiseringsmetod för fjärrskrivbordsanslutningen visas föregående fel.

Om du inte har distribuerat Windows Hello för företag och om det inte är ett alternativ för tillfället kan du utesluta MFA-krav genom att konfigurera principen för villkorlig åtkomst som utesluter appen "Azure Windows VM-inloggning" från listan över molnappar som kräver MFA. Mer information om Windows Hello för företag finns i [Översikt över Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Pin-autentisering med Windows Hello för företag med RDP har stötts av Windows 10 för flera versioner, men stöd för biometrisk autentisering med RDP lades till i Windows 10 version 1809. Att använda Windows Hello för företag-autentisering under RDP är endast tillgängligt för distributioner som använder cert-förtroendemodell och som för närvarande inte är tillgängligt för nyckelförtroendemodellen.
 
## <a name="preview-feedback"></a>Feedback för förhandsversionen

Dela din feedback om den här förhandsgranskningsfunktionen eller rapportera problem med att använda den på [Azure AD-feedbackforumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
