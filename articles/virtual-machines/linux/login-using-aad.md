---
title: Logga in på en virtuell Linux-dator med Azure Active Directory-autentiseringsuppgifter
description: Lär dig hur du skapar och konfigurerar en Virtuell Linux-dator för att logga in med Azure Active Directory-autentisering.
author: iainfoulds
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 2731693667d2129a72da72455c6bbdd74c277697
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366497"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Förhandsversion: Logga in på en virtuell Linux-dator i Azure med Azure Active Directory-autentisering

För att förbättra säkerheten för virtuella Linux-datorer (VMs) i Azure kan du integrera med Azure Active Directory (AD) autentisering. När du använder Azure AD-autentisering för virtuella Linux-datorer styr och tillämpar du principer centralt som tillåter eller nekar åtkomst till de virtuella datorerna. Den här artikeln visar hur du skapar och konfigurerar en Virtuell Linux-dator för att använda Azure AD-autentisering.


> [!IMPORTANT]
> Azure Active Directory-autentisering är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Använd den här funktionen på en virtuell testdator som du förväntar dig att ignorera efter testning.
>


Det finns många fördelar med att använda Azure AD-autentisering för att logga in på virtuella Linux-datorer i Azure, inklusive:

- **Förbättrad säkerhet:**
  - Du kan använda dina AD-autentiseringsuppgifter för att logga in på virtuella Azure Linux-datorer. Du behöver inte skapa lokala administratörskonton och hantera livstid för autentiseringsuppgifter.
  - Genom att minska din tillit till lokala administratörskonton behöver du inte oroa dig för förlust/stöld av autentiseringsuppgifter, användare som konfigurerar svaga autentiseringsuppgifter etc.
  - Principer för lösenordskomplexitet och lösenord som konfigurerats för din Azure AD-katalog hjälper även till att skydda virtuella Linux-datorer.
  - Om du vill ha ytterligare säker inloggning till virtuella Azure-datorer kan du konfigurera multifaktorautentisering.
  - Möjligheten att logga in på virtuella Linux-datorer med Azure Active Directory fungerar också för kunder som använder [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Sömlöst samarbete:** Med rollbaserad åtkomstkontroll (RBAC) kan du ange vem som kan logga in på en viss virtuell dator som en vanlig användare eller med administratörsbehörighet. När användare ansluter till eller lämnar teamet kan du uppdatera RBAC-principen för den virtuella datorn för att bevilja åtkomst efter behov. Den här upplevelsen är mycket enklare än att behöva skrubba virtuella datorer för att ta bort onödiga SSH-offentliga nycklar. När anställda lämnar organisationen och deras användarkonto inaktiveras eller tas bort från Azure AD har de inte längre åtkomst till dina resurser.

## <a name="supported-azure-regions-and-linux-distributions"></a>Azure-regioner och Linux-distributioner som stöds

Följande Linux-distributioner stöds för närvarande under förhandsversionen av den här funktionen:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42,3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 (PÅ ANDRA) |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 och Ubuntu Server 18.04 |


Följande Azure-regioner stöds för närvarande under förhandsversionen av den här funktionen:

- Alla globala Azure-regioner

>[!IMPORTANT]
> Om du vill använda den här förhandsversionen distribuerar du bara en Linux-distribution som stöds och i en Azure-region som stöds. Funktionen stöds inte i Azure Government eller suveräna moln.
>
> Det stöds inte att använda det här tillägget på AKS-kluster (Azure Kubernetes Service). Mer information finns i [Supportprinciper för AKS](../../aks/support-policies.md).


Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Nätverkskrav

Om du vill aktivera Azure AD-autentisering för dina virtuella Linux-datorer i Azure måste du se till att nätverkskonfigurationen för dina virtuella datorer tillåter utgående åtkomst till följande slutpunkter via TCP-port 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> För närvarande kan Azure-nätverkssäkerhetsgrupper inte konfigureras för virtuella datorer som är aktiverade med Azure AD-autentisering.

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en resursgrupp med [az-grupp skapa](/cli/azure/group#az-group-create)och skapa sedan en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) med hjälp av en distro och i en region som stöds. I följande exempel distribueras en virtuell dator med namnet *myVM* som använder *Ubuntu 16.04 LTS* i en resursgrupp med namnet *myResourceGroup* i *regionen southcentralus.* I följande exempel kan du ange dina egna resursgrupp- och VM-namn efter behov.

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

## <a name="install-the-azure-ad-login-vm-extension"></a>Installera tillägget Azure AD-inloggning vm

> [!NOTE]
> Om du distribuerar det här tillägget till en tidigare skapad virtuell dator säkerställer du att datorn har minst 1 GB minne som allokerats annars misslyckas tillägget att installera

Om du vill logga in på en Virtuell Linux-dator med Azure AD-autentiseringsuppgifter installerar du azure Active Directory-tillägget för inloggnings-VM. VM-tillägg är små program som tillhandahåller konfigurations- och automatiseringsuppgifter efter distributionen på virtuella Azure-datorer. Använd [az vm-tillägget för](/cli/azure/vm/extension#az-vm-extension-set) att installera *AADLoginForLinux-tillägget* på den virtuella datorn som heter *myVM* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

*Etableringstaten* för *lyckades* visas när tillägget har installerats på den virtuella datorn. Den virtuella datorn behöver en vm-agent som körs för att installera tillägget. Mer information finns i [VM-agentöversikt](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurera rolltilldelningar för den virtuella datorn

Azure Role-Based Access Control (RBAC) princip avgör vem som kan logga in på den virtuella datorn. Två RBAC-roller används för att auktorisera VM-inloggning:

- **Inloggning för administratör för virtuell**dator: Användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med Windows-administratörs- eller Linux-rotanvändarbehörighet.
- **Användarlogin för virtuella**datorer: Användare med den här rollen tilldelad kan logga in på en virtuell Azure-dator med vanliga användarbehörigheter.

> [!NOTE]
> Om du vill att en användare ska kunna logga in på den virtuella datorn via SSH måste du tilldela rollen *inloggning* av administratör för virtuell dator eller *användarlogin för virtuell dator.* En Azure-användare med *ägar-* eller *deltagarrollerna* som tilldelats för en virtuell dator har inte automatiskt behörighet att logga in på den virtuella datorn via SSH.

I följande exempel används [az-rolltilldelningsskapande](/cli/azure/role/assignment#az-role-assignment-create) för att tilldela rollen *inloggning till den virtuella datorns administratörslogin* till den virtuella datorn för din nuvarande Azure-användare. Användarnamnet för ditt aktiva Azure-konto hämtas med [az-kontoprogram](/cli/azure/account#az-account-show)och *scopet* är inställt på den virtuella datorn som skapats i ett tidigare steg med [az vm show](/cli/azure/vm#az-vm-show). Scopet kan också tilldelas på en resursgrupp eller prenumerationsnivå och normala RBAC-arvsbehörigheter gäller. Mer information finns i [Rollbaserade åtkomstkontroller](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Om din AAD-domän och inloggningsanvändardomän inte matchar måste du ange objekt-ID för ditt användarkonto med *--assignee-object-id*, inte bara användarnamnet för *--assignee*. Du kan hämta objekt-ID:t för ditt användarkonto med [az ad-användarlista](/cli/azure/ad/user#az-ad-user-list).

Mer information om hur du använder RBAC för att hantera åtkomst till dina Azure-prenumerationsresurser finns i [Azure CLI,](../../role-based-access-control/role-assignments-cli.md) [Azure Portal](../../role-based-access-control/role-assignments-portal.md)eller [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Du kan också konfigurera Azure AD för att kräva multifaktorautentisering för en viss användare att logga in på den virtuella Linux-datorn. Mer information finns i [Komma igång med Azure Multi-Factor Authentication i molnet](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Logga in på virtuella Linux-datorn

Visa först den offentliga IP-adressen för din virtuella dator med [az vm-show:](/cli/azure/vm#az-vm-show)

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Logga in på den virtuella Azure Linux-datorn med dina Azure AD-autentiseringsuppgifter. Med `-l` parametern kan du ange din egen Azure AD-kontoadress. Ersätt exempelkontot med ditt eget. Kontoadresser ska anges i alla gemener. Ersätt exempel-IP-adressen med den offentliga IP-adressen för den virtuella datorn från föregående kommando.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Du uppmanas att logga in på Azure AD med [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)en engångsanvändningskod på . Kopiera och klistra in engångskoden i enhetens inloggningssida.

Ange dina Inloggningsuppgifter för Azure AD på inloggningssidan när du uppmanas att göra det. 

Följande meddelande visas i webbläsaren när du har autentiserat:`You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Stäng webbläsarfönstret, gå tillbaka till SSH-prompten och tryck på **Retur.** 

Du är nu inloggad på den virtuella Azure Linux-datorn med rollbehörigheterna som tilldelade, till exempel *VM-användare* eller *VM-administratör*. Om ditt användarkonto tilldelas rollen *Inloggning för virtuell datoradministratör* kan du använda `sudo` för att köra kommandon som kräver rotbehörigheter.

## <a name="sudo-and-aad-login"></a>Sudo och AAD-inloggning

Första gången du kör sudo blir du ombedd att autentisera en andra gång. Om du inte vill autentisera igen för att köra sudo kan `/etc/sudoers.d/aad_admins` du redigera filen sudoers och ersätta den här raden:

```bash
%aad_admins ALL=(ALL) ALL
```

Med denna rad:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Felsöka inloggningsproblem

Några vanliga fel när du försöker SSH med Azure AD-autentiseringsuppgifter inkluderar inga RBAC-roller som tilldelats och upprepade uppmaningar om att logga in. Använd följande avsnitt för att korrigera dessa problem.

### <a name="access-denied-rbac-role-not-assigned"></a>Åtkomst nekad: RBAC-rollen har inte tilldelats

Om du ser följande fel i SSH-prompten kontrollerar du att du har konfigurerat RBAC-principer för den virtuella datorn som ger användaren rollen *som inloggning för virtuell datoradministratör* eller *användarlogin för virtuell dator:*

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Fortsatta SSH-inloggningsmeddelanden

Om du har slutfört autentiseringssteget i en webbläsare kan du omedelbart uppmanas att logga in igen med en ny kod. Det här felet orsakas vanligtvis av en obalans mellan inloggningsnamnet som du angav vid SSH-prompten och kontot som du loggade in på Azure AD med. Så här åtgärdar du problemet:

- Kontrollera att inloggningsnamnet som du angav vid SSH-prompten är korrekt. Ett stavfel i inloggningsnamnet kan orsaka en obalans mellan inloggningsnamnet som du angav vid SSH-prompten och kontot som du loggade in på Azure AD med. Du har till exempel skrivit *azuresuer\@contoso.onmicrosoft.com* i stället för *azureuser\@contoso.onmicrosoft.com*.
- Om du har flera användarkonton kontrollerar du att du inte tillhandahåller ett annat användarkonto i webbläsarfönstret när du loggar in på Azure AD.
- Linux är ett skiftlägeskänsligt operativsystem. Det finns enAzureuser@contoso.onmicrosoft.comskillnad mellanazureuser@contoso.onmicrosoft.com' ' och ' ', vilket kan orsaka en obalans. Se till att du anger UPN med rätt skiftlägeskänslighet vid SSH-prompten.

### <a name="other-limitations"></a>Andra begränsningar

Användare som ärver åtkomsträttigheter via kapslade grupper eller rolltilldelningar stöds för närvarande inte. Användaren eller gruppen måste tilldelas de [nödvändiga rolltilldelningarna](#configure-role-assignments-for-the-vm)direkt . Användningen av hanteringsgrupper eller kapslade grupprolltilldelningar ger till exempel inte rätt behörighet för att tillåta användaren att logga in.

## <a name="preview-feedback"></a>Feedback för förhandsversionen

Dela din feedback om den här förhandsgranskningsfunktionen eller rapportera problem med att använda den på [Azure AD-feedbackforumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory finns i [Vad är Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
