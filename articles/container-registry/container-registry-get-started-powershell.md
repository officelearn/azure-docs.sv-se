---
title: Snabbstart – skapa ett privat Docker-register i Azure – PowerShell
description: Lär dig snabbt att skapa ett privat Docker-containerregister i Azure med PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299751"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Snabbstart: Skapa ett privat containerregister med hjälp av Azure PowerShell

Azure Container Registry är en hanterad Docker-behållarregistertjänst för att bygga, lagra och hantera Docker-behållaravbildningar. I denna snabbstart kommer du att lära dig hur du skapar ett Azure-containerregister med hjälp av PowerShell. Använd sedan Docker-kommandon för att skicka en containeravbildning till registret, och hämta och kör slutligen avbildningen från registret.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten kräver Azure PowerShell-modul version 5.7.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att avgöra installerad version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).

Du måste också ha Docker installerat lokalt. Docker innehåller paket för [macOS][docker-mac]-, [Windows][docker-windows]- och [Linux][docker-linux]-system.

Eftersom Azure Cloud Shell inte innehåller alla nödvändiga Docker-komponenter (`dockerd`-daemon), kan du inte använda Cloud Shell för denna snabbstart.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzureRmAccount][Connect-AzureRmAccount] och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

När du är autentiserad med Azure, skapa en resursgrupp med [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. En resursgrupp är en logisk container där du kan distribuera och hantera dina Azure-resurser.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Skapa containerregister

Skapa sedan ett behållarregister i din nya resursgrupp med kommandot [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel skapas ett register med namnet ”myContainerRegistry007”. Ersätt *myContainerRegistry007* i följande kommando innan du kör det för att skapa registernyckeln:

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

I den här snabbstarten skapar du ett *Basic*-register, vilket är ett kostnadsoptimerat alternativ för utvecklare som lär sig om Azure Container Registry. Mer information om tillgängliga tjänstnivåer finns i [SKU:er för Azure Container Registry][container-registry-skus].

## <a name="log-in-to-registry"></a>Logga in till registret

Innan du skickar och hämtar containeravbildningar måste du logga in i ditt register. I produktionsscenarier bör du använda en individuell identitet eller tjänstens huvudnamn för åtkomst till containerregistret, men för att snabbstarten inte ska bli för lång aktiverar du administratörsanvändaren i registret med kommandot [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential]:

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Kör därefter [docker-inloggning][docker-login] för att logga in:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Kommandot returnerar `Login Succeeded` när det har slutförts.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När du har arbetat klart med resurserna som du skapade i den här snabbstarten använder du kommandot [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] för att ta bort resursgruppen, containerregistret och containeravbildningarna som lagras där:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure PowerShell, push-överförde en containeravbildning och hämtade och körde avbildningen från registret. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md