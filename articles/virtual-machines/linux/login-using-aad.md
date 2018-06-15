---
title: Logga in på en Linux VM med Azure Active Directory-autentiseringsuppgifter | Microsoft Docs
description: I den här ta du lära dig hur du skapar och konfigurerar en Linux VM för att använda Azure Active Directory-autentisering för användarinloggningar
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/16/2018
ms.author: iainfou
ms.openlocfilehash: 96cc7aeb5fd1c64dc3793a801a4a5b759e7558b9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652880"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logga in på en virtuell Linux-dator i Azure med Azure Active Directory-autentisering (förhandsgranskning)

Du kan integrera med Azure Active Directory (AD)-autentisering för att förbättra säkerheten för Linux virtuella datorer (VM) i Azure. När du använder Azure AD-autentisering för virtuella Linux-datorer kan du centralt styra och genomdriva principer som tillåter eller nekar åtkomst till de virtuella datorerna. Den här artikeln visar hur du skapar och konfigurerar en Linux VM för att använda Azure AD-autentisering.

> [!NOTE]
> Den här funktionen är i förhandsvisning och rekommenderas inte för användning med virtuella maskiner eller arbetsbelastningar. Använd denna funktion på virtuella testdatorer som du förväntar dig att ta bort efter testning.

Det finns många fördelar med att använda Azure AD authentication för att logga in på Linux virtuella datorer i Azure, inklusive:

- **Förbättrad säkerhet:**
  - Du kan använda AD företagets inloggningsuppgifter för att logga in på virtuella Azure Linux-datorer. Det finns inget behov av att skapa lokala administratörskonton och hantera autentiseringsuppgifter livslängd.
  - Genom att minska din beroendet av andra lokala administratörskonton, behöver du inte bekymra dig om förlust/stöld av autentiseringsuppgifter, användare konfigurera svaga referenser osv.
  - Att säkra virtuella Linux-datorer samt lösenordens komplexitet och lösenordsprinciper livslängd som konfigurerats för Azure AD-katalogen.
  - Du kan konfigurera multifaktorautentisering för ytterligare säker inloggning till Azure virtuella datorer.
  - Möjligheten att logga in på virtuella Linux-datorer med Azure Active Directory fungerar även för kunder som använder [federationstjänster](../../active-directory/connect/active-directory-aadconnectfed-whatis.md).

- **Sömlös samarbete:** With Role-Based åtkomstkontroll (RBAC), kan du ange vem som kan logga in på en viss virtuell dator som en vanlig användare eller med administratörsbehörighet. När användare ansluta till eller lämnar din grupp, kan du uppdatera RBAC-principen för den virtuella datorn att bevilja åtkomst efter behov. Det här upplevelsen är mycket enklare än att Skrubba virtuella datorer för att ta bort onödiga offentliga SSH-nycklar. När medarbetare lämnar organisationen och deras användarkonto inaktiveras eller tas bort från Azure AD kan har de inte längre åtkomst till resurser.

### <a name="supported-azure-regions-and-linux-distributions"></a>Azure-regioner och Linux-distributioner som stöds

Följande Linux-distributioner stöds för närvarande under förhandsgranskning av den här funktionen:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6,9 och CentOS 7.4 |
| RedHat Enterprise Linux | RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 och Ubuntu Server 17.10 |

Följande Azure-regioner stöds för närvarande under förhandsgranskning av den här funktionen:

- Alla globala Azure-regioner

>[!IMPORTANT]
> Om du vill använda den här förhandsgranskningsfunktionen bara distribuera en stöds Linux distro och i en Azure-regionen stöds. Funktionen stöds inte i Azure Government eller suveräna moln.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az-group-create), skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) med hjälp av en stöds distro och i en region som stöds. I följande exempel distribuerar en virtuell dator med namnet *myVM* som använder *Ubuntu 16.04 LTS* i en resursgrupp med namnet *myResourceGroup* i den *southcentralus*  region. I följande exempel kan du ange egna resursgruppen och VM-namn efter behov.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser.

## <a name="install-the-azure-ad-login-vm-extension"></a>Installera Azure AD-inloggningen VM-tillägget

Installera Azure Active Directory-loggen i VM-tillägget för att logga in till en Linux-VM med autentiseringsuppgifter för Azure AD. VM-tillägg är små program som innehåller efter distributionen konfiguration och automatisering av uppgifter på virtuella Azure-datorer. Använd [az vm-tillägget set](/cli/azure/vm/extension#az-vm-extension-set) att installera den *AADLoginForLinux* tillägget på den virtuella datorn med namnet *myVM* i den *myResourceGroup* resurs grupp:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Den *provisioningState* av *lyckades* visas när tillägget har installerats på den virtuella datorn.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera rolltilldelningar för den virtuella datorn

Azure rollbaserad åtkomstkontroll (RBAC)-princip bestämmer vem som kan logga in på den virtuella datorn. Två RBAC-roller används för att auktorisera VM inloggning:

- **Administratören för virtuella datorer inloggningen**: användare som har tilldelats den här rollen kan logga in till en virtuell Azure-dator med Windows-administratör eller Linux rot användarbehörigheter.
- **Virtual Machine användarinloggning**: användare som har tilldelats den här rollen kan logga in till en virtuell Azure-dator med vanliga användarbehörigheter.

> [!NOTE]
> Om du vill tillåta användare att logga in på den virtuella datorn via SSH, måste du tilldela antingen den *virtuella administratörsinloggning* eller *virtuella användarinloggning* roll. En Azure användare med den *ägare* eller *deltagare* roller för en virtuell dator inte har behörighet att logga in på den virtuella datorn via SSH automatiskt.

I följande exempel används [az rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) att tilldela den *virtuella administratörsinloggning* roll till den virtuella datorn för din aktuella Azure-användare. Användarnamnet för din aktiva Azure-konto har erhållits med [az konto visa](/cli/azure/account#az-account-show), och *omfång* är inställd på den virtuella datorn skapas i föregående steg med [az vm visa](/cli/azure/vm#az-vm-show). Omfånget kan också tilldelas på en resurs eller en prenumeration nivå och gäller normal RBAC ärvda behörigheter. Mer information finns i [rollbaserad åtkomstkontroll](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och användarnamn för inloggningsdomänen inte matchar, måste du ange objekt-ID för ditt konto med den *--id för tilldelad objektet*, inte bara användarnamn för *--tilldelades*. Du kan hämta objekt-ID för ditt konto med [az ad användarlistan](/cli/azure/ad/user#az-ad-user-list).

Se mer information om hur RBAC kan användas för att hantera åtkomst till resurserna i Azure-prenumeration med hjälp av den [Azure CLI 2.0](../../role-based-access-control/role-assignments-cli.md), [Azure-portalen](../../role-based-access-control/role-assignments-portal.md), eller [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Du kan också konfigurera Azure AD för att kräva multifaktorautentisering för en viss användare att logga in på den virtuella Linux-datorn. Mer information finns i [Kom igång med Azure Multi-Factor Authentication i molnet](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Logga in på Linux-dator

Visa först offentliga IP-adressen för den virtuella datorn med [az vm visa](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Logga in på Azure Linux virtuella datorer som använder Azure AD-autentiseringsuppgifterna. Den `-l` parametern kan du ange egna Azure AD-adressen för kontot. Ange den offentliga IP-adressen på den virtuella datorn som visas i föregående kommando:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Du uppmanas att logga in på Azure AD med en enstaka Använd kod i [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Kopiera och klistra in koden enstaka Använd inloggningssidan enheten som visas i följande exempel:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

När du uppmanas ange dina inloggningsuppgifter för Azure AD på inloggningssidan. Följande meddelande visas i webbläsaren när du har autentiserats:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Stäng webbläsarfönstret återgå till SSH kommandotolk och tryck på den **RETUR** nyckel. Du är nu inloggad på den virtuella datorn i Azure Linux med rollbehörigheterna som tilldelats som *VM användaren* eller *VM administratören*. Om ditt konto har tilldelats den *virtuella administratörsinloggning* roll, som du kan använda den `sudo` att köra kommandon som kräver rotprivilegier.

## <a name="troubleshoot-sign-in-issues"></a>Felsökning av problem med inloggning

Några vanliga fel när du försöker SSH med autentiseringsuppgifter för Azure AD innehåller inga RBAC-roller som är tilldelad, och upprepas anvisningarna för att logga in. Använd följande avsnitt för att lösa dessa problem.

### <a name="access-denied-rbac-role-not-assigned"></a>Åtkomst nekas: RBAC-roll som inte tilldelats

Om du ser följande fel på SSH-Kommandotolken, kontrollerar du att [konfigurerat RBAC principer](#configure-rbac-policy-for-the-virtual-machine) för den virtuella datorn som ger användaren antingen den *virtuella administratörsinloggning* eller *virtuella Datorn användarinloggning* roll:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Fortsatt SSH-inloggning uppmanas

Om du har slutfört steg autentisering i en webbläsare, kan du omedelbart ombedd att logga in igen med en ny kod. Det här felet beror vanligtvis på ett matchningsfel mellan inloggning namn som du angav i SSH-Kommandotolken och det konto som du har loggat in till Azure AD med. Åtgärda problemet:

- Kontrollera att du har angett i Kommandotolken SSH-inloggning namn är korrekt. Ett skrivfel i namnet inloggning kan orsaka ett matchningsfel mellan inloggning namn som du angav i SSH-Kommandotolken och det konto som du har loggat in till Azure AD med. Till exempel du har angett *azuresuer@contoso.onmicrosoft.com* i stället för *azureuser@contoso.onmicrosoft.com*.
- Om du har flera användarkonton kan du kontrollera att du inte anger ett annat användarkonto i webbläsarfönstret när du loggar in till Azure AD.
- Linux är en skiftlägeskänslig operativsystem. Det är skillnad mellan 'Azureuser@contoso.onmicrosoft.com'och'azureuser@contoso.onmicrosoft.com', vilket kan orsaka ett matchningsfel. Se till att du anger UPN med rätt skiftlägeskänslighet SSH i Kommandotolken.

## <a name="preview-feedback"></a>Förhandsgranska feedback

Dela din feedback om den här preview funktion eller rapporten problem som används i den [Feedbackforum för Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns [vad är Azure Active Directory](../../active-directory/active-directory-whatis.md) och [hur du kommer igång med Azure Active Directory](../../active-directory/get-started-azure-ad.md)
