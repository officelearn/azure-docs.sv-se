---
title: Snabbstart - skapa ett privat Docker-register i Azure med PowerShell
description: "Snabbt lära dig skapa ett privat Docker behållare register med PowerShell."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
ms.service: container-registry
ms.devlang: na
ms.topic: quicksart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d165cc159f7da2c8e7f0be4f0fa7d353997ed5f9
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Skapa ett Azure Container registret med hjälp av PowerShell

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden information att skapa en Azure-behållare registret-instans med hjälp av PowerShell.

Denna Snabbstart kräver Azure PowerShell Modulversion 3,6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Du måste också ha Docker installeras lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

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

Skapa en ACR instans med hjälp av den [ny AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) kommando.

Namnet på registret **måste vara unika**. I följande exempel *myContainerRegistry007* används. Uppdatera den till ett unikt värde.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Använd först den [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) kommando för att hämta administratörsautentiseringsuppgifterna för ACR-instanser.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Använd sedan den [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommando för att logga in till ACR-instans.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Ett meddelande om att inloggningen lyckades returneras när inloggningen är klar.

## <a name="push-image-to-acr"></a>Push-avbildningen till ACR

Du måste ha en bild för att vidarebefordra en avbildning till en Azure-behållare registret. Om det behövs, kör du följande kommando för att hämta en avbildning som skapats i förväg från Docker-hubb.

```bash
docker pull microsoft/aci-helloworld
```

Bilden måste vara taggade med ACR server inloggningsnamnet. Kör den [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) kommando för att returnera server inloggningsnamnet för ACR-instans.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Taggen bild med hjälp av den [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) kommando. Ersätt *acrLoginServer* med inloggningen servernamnet för din ACR-instans.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Använd slutligen [docker push](https://docs.docker.com/engine/reference/commandline/push/) att skicka bilder till ACR-instans. Ersätt *acrLoginServer* med inloggningen servernamnet för din ACR-instans.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, ACR-instans och alla bilder i behållaren.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapat du ett Azure Container registret med Azure CLI. Om du vill använda Azure Container registret med Azure Container instanser fortsätta att Azure Behållarinstanser kursen.

> [!div class="nextstepaction"]
> [Azure Behållarinstanser självstudiekursen](../container-instances/container-instances-tutorial-prepare-app.md)