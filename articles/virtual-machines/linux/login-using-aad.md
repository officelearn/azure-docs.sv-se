---
title: Logga in på en Linux VM med Azure Active Directory-autentiseringsuppgifter | Microsoft Docs
description: I den här howto du lära dig hur du skapar och konfigurerar en Linux-VM för att använda Azure Active Directory-autentisering för användarinloggningar
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: 2201455477af2bd871f8e8e401536d45af4a02f7
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294666"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Logga in på en Linux-dator i Azure med Azure Active Directory-autentisering (förhandsversion)

Du kan integrera med Azure Active Directory (AD)-autentisering för att förbättra säkerheten för Linux-datorer (VM) i Azure. När du använder Azure AD-autentisering för virtuella Linux-datorer kan du centralt styra och genomdriva principer som tillåter eller nekar åtkomst till de virtuella datorerna. Den här artikeln visar hur du skapar och konfigurerar en Linux-VM för att använda Azure AD-autentisering.

> [!NOTE]
> Den här funktionen är en förhandsversion och rekommenderas inte för användning med virtuella produktionsdatorerna eller arbetsbelastningar. Använd denna funktion på en virtuell testdator som du vill ta bort när du testar.

Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Linux-datorer i Azure, inklusive:

- **Förbättrad säkerhet:**
  - Du kan använda företagets AD autentiseringsuppgifter för att logga in på virtuella Linux-datorer. Det finns inget behov att skapa lokala administratörskonton och hantera autentiseringsuppgifter livslängd.
  - Genom att minska dina lokala administratörskonton där behöver du inte bekymra dig om förlust/stöld av autentiseringsuppgifter, användare som konfigurerar svaga behörigheter osv.
  - Hjälper att säkra virtuella Linux-datorer samt lösenordens komplexitet och lösenordsprinciper livslängd som konfigurerats för Azure AD-katalogen.
  - Du kan konfigurera multifaktorautentisering för att ytterligare säker inloggning till Azure-datorer.
  - Möjligheten att logga in på virtuella Linux-datorer med Azure Active Directory fungerar även för kunder som använder [federationstjänster](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Kan samarbeta:** With Role-Based åtkomstkontroll (RBAC), kan du ange vem som kan logga in på en viss virtuell dator som en vanlig användare eller med administratörsbehörighet. När användare ansluta till eller lämnar ditt team kan uppdatera du RBAC-principen för den virtuella datorn att bevilja åtkomst efter behov. Den här upplevelsen är mycket enklare än att behöva Skrubba virtuella datorer för att ta bort onödiga offentliga SSH-nycklar. När medarbetare lämnar organisationen och användarkontot inaktiveras eller tas bort från Azure AD kan har de inte längre åtkomst till dina resurser.

### <a name="supported-azure-regions-and-linux-distributions"></a>Azure-regioner och Linux-distributioner som stöds

Följande Linux-distributioner stöds för närvarande i förhandsversionen av den här funktionen:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6,9 och CentOS 7.4 |
| Debian | Debian 9 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04, Ubuntu Server 17.10 och Ubuntu Server 18.04 |

Följande Azure-regioner stöds för närvarande i förhandsversionen av den här funktionen:

- Alla globala Azure-regioner

>[!IMPORTANT]
> Om du vill använda den här förhandsversionsfunktionen bara distribuera en Linux-distribution som stöds och i en Azure-region. Funktionen stöds inte i Azure Government eller nationella moln.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az-group-create), skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) med hjälp av en distribution som stöds och i en region som stöds. I följande exempel distribuerar en virtuell dator med namnet *myVM* som använder *Ubuntu 16.04 LTS* i en resursgrupp med namnet *myResourceGroup* i den *usasödracentrala*  region. I följande exempel kan du ange en egen resursgrupp och namn på virtuella datorer efter behov.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Installera Azure AD-kontoinloggning VM-tillägg

Om du vill logga in på en Linux VM med Azure AD-autentiseringsuppgifter, installera Azure Active Directory-loggen i VM-tillägget. VM-tillägg är litet program som ger efter distributionen konfiguration och automatisering av uppgifter i Azure virtual machines. Använd [az vm-tilläggsuppsättningen](/cli/azure/vm/extension#az-vm-extension-set) att installera den *AADLoginForLinux* tillägget på den virtuella datorn med namnet *myVM* i den *myResourceGroup* resurs grupp:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Den *provisioningState* av *lyckades* visas när tillägget har installerats på den virtuella datorn.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera rolltilldelningar för den virtuella datorn

Azure rollbaserad åtkomstkontroll (RBAC)-princip bestämmer vem som kan logga in på den virtuella datorn. Två RBAC-roller används för att auktorisera VM inloggningen:

- **Administratörsinloggning för virtuell dator**: användare med den här rollen tilldelad kan logga in till en Azure-dator med Windows-administratör eller Linux-rotanvändare.
- **Användarinloggning för virtuell dator**: användare med den här rollen tilldelad kan logga in till en Azure-dator med vanliga användarbehörigheter.

> [!NOTE]
> Om du vill tillåta en användare att logga in på den virtuella datorn via SSH måste du tilldela antingen den *administratörsinloggning för virtuell dator* eller *användarinloggning för virtuell dator* roll. En Azure-användare med den *ägare* eller *deltagare* roller som är tilldelade för en virtuell dator inte automatiskt har behörighet att logga in på den virtuella datorn via SSH.

I följande exempel används [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) att tilldela den *administratörsinloggning för virtuell dator* rollen till den virtuella datorn för din aktuella Azure-användare. Användarnamnet för din aktiva Azure-konto hämtas med [az konto show](/cli/azure/account#az-account-show), och *omfång* är inställd på den virtuella datorn som skapades i föregående steg med [az vm show](/cli/azure/vm#az-vm-show). Omfånget kan också tilldelas en resurs gruppen eller på prenumerationsnivån och normal RBAC arv av behörigheter gäller. Mer information finns i [rollbaserade åtkomstkontroller](../../azure-resource-manager/resource-group-overview.md#access-control)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och användarnamn inloggningsdomän inte matchar, måste du ange objekt-ID för ditt konto med den *--tilldelad person-objekt-id*, inte bara användarnamnet för *--tilldelad person*. Du kan hämta objekt-ID för ditt konto med [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Mer information om hur du använder RBAC för att hantera åtkomst till din Azure-prenumerationsresurser finns i använda den [Azure CLI 2.0](../../role-based-access-control/role-assignments-cli.md), [Azure-portalen](../../role-based-access-control/role-assignments-portal.md), eller [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Du kan också konfigurera Azure AD för att kräva multifaktorautentisering för en viss användare att logga in på Linux-datorn. Mer information finns i [Kom igång med Azure Multi-Factor Authentication i molnet](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Logga in på Linux-dator

Först visa offentliga IP-adressen för den virtuella datorn med [az vm show](/cli/azure/vm#az-vm-show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Logga in på den virtuella Linux-datorer med hjälp av Azure AD-autentiseringsuppgifter. Den `-l` parametern kan du ange din egen adress för Azure AD-konto. Ange den offentliga IP-adressen för den virtuella datorn som utdata i föregående kommando:

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

Du uppmanas att logga in på Azure AD med en gång använda kod i [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin). Kopiera och klistra in koden enstaka användning i inloggningssidan enheten som visas i följande exempel:

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

När du uppmanas, anger du dina autentiseringsuppgifter för Azure AD-inloggningen på inloggningssidan. Följande meddelande visas i webbläsaren när du har autentiserats:

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

Stäng webbläsarfönstret, återgå till en SSH-Kommandotolken och tryck på den **RETUR** nyckel. Du är nu inloggad på virtuella Linux-datorer med behörigheterna som tilldelats som *VM User* eller *VM-administratör*. Om ditt konto tilldelas den *administratörsinloggning för virtuell dator* roll, som du kan använda den `sudo` att köra kommandon som kräver rotprivilegier.

## <a name="troubleshoot-sign-in-issues"></a>Felsöka problem med användarinloggning

Några vanliga fel när du försöker SSH med autentiseringsuppgifter för Azure AD innehåller inga RBAC-roller som har tilldelats och upprepade uppmaningar att logga in. Använd följande avsnitt för att åtgärda dessa problem.

### <a name="access-denied-rbac-role-not-assigned"></a>Åtkomst nekad: RBAC-roll som inte har tilldelats

Om du ser följande fel i din SSH-Kommandotolken, kontrollera att du har [konfigurerat RBAC-principer](#configure-rbac-policy-for-the-virtual-machine) för den virtuella datorn som ger användaren antingen den *administratörsinloggning för virtuell dator* eller *virtuella Datorn användarinloggning* roll:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Fortsatt SSH-inloggning anvisningarna

Om du har slutfört autentiseringen steg i en webbläsare kan du omedelbart ombedd att logga in igen med en ny kod. Det här felet beror vanligtvis på ett matchningsfel mellan inloggning namn som du angav i SSH-Kommandotolken och det konto som du har loggat in på Azure AD med. Du åtgärdar problemet:

- Kontrollera att du har angett prompten SSH-inloggning namn är korrekt. Ett skrivfel i inloggningsnamnet kan orsaka ett matchningsfel mellan inloggning namn som du angav i SSH-Kommandotolken och det konto som du har loggat in på Azure AD med. Exempel: du har angett *azuresuer@contoso.onmicrosoft.com* i stället för *azureuser@contoso.onmicrosoft.com*.
- Om du har flera användarkonton kan du kontrollera att du inte anger ett annat användarkonto i webbläsarfönstret när du loggar in till Azure AD.
- Linux är en skiftlägeskänslig operativsystem. Det är skillnad mellan ”Azureuser@contoso.onmicrosoft.com” och ”azureuser@contoso.onmicrosoft.com”, vilket kan orsaka ett matchningsfel. Se till att du anger UPN-namnet med rätt skiftlägeskänslighet i SSH-Kommandotolken.

## <a name="preview-feedback"></a>Feedback från förhandsversionen

Lämna feedback om den här förhandsversionen funktion eller rapporten problem som används i den [Azure AD-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [vad är Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
