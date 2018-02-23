---
title: "Snabbstart – skapa ett privat Docker-register i Azure med PowerShell"
description: "Lär dig snabbt att skapa ett privat Docker-behållarregister med PowerShell."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 80b5055dee35cd6efe62ee949c05aef386a3ba14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Skapa ett Azure Container Registry med PowerShell

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden beskriver hur du skapar en Azure Container Registry-instans med hjälp av PowerShell.

Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

Skapa en ACR-instans med hjälp av kommandot [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel används *myContainerRegistry007*. Uppdatera det här till ett unikt värde.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Använd först kommandot [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) för att hämta autentiseringsuppgifter som administratör för ACR-instansen.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Använd sedan kommandot [docker login][docker-login] och logga in på ACR-instansen.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Kommandot returnerar `Login Succeeded` när det har slutförts. Det kan även hända att du ser en säkerhetsvarning som rekommenderar att parametern `--password-stdin` används. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Se kommandoreferensen [docker login][docker-login] om du vill ha mer information.

## <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Kör följande kommando vid behov för att hämta en befintlig avbildning från Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Avbildningen måste vara taggad med namnet på ACR-inloggningsservern. Kör kommandot [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) namnet på inloggningsservern för ACR-instansen.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt *acrLoginServer* med namnet på inloggningsservern för ACR-instansen.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Använd slutligen [docker push][docker-push] till att push-överföra avbildningen till ACR-instansen. Ersätt *acrLoginServer* med namnet på inloggningsservern för ACR-instansen.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) till att ta bort resursgruppen, ACR-instansen och alla behållaravbildningar.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure CLI. Om du vill använda Azure Container Registry med Azure Container Instances fortsätter du till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/