---
title: Snabbstart – skapa ett privat Docker-register i Azure med PowerShell
description: Lär dig snabbt att skapa ett privat Docker-behållarregister med PowerShell.
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9603ac779c7dbc640a7c24856c32f04edbac849d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>Snabbstart: Skapa ett Azure Container Registry med PowerShell

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. I den här guiden får du information om hur du kan skapa en Azure Container Registry-instans med hjälp av PowerShell, överföra en behållaravbildning till registret och distribuera behållaren från ditt register till Azure Container Instances (ACI).

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

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Kommandot returnerar `Login Succeeded` när det har slutförts. Det kan även hända att du ser en säkerhetsvarning som rekommenderar att parametern `--password-stdin` används. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Se kommandoreferensen [docker login][docker-login] om du vill ha mer information.

## <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Kör följande kommando vid behov för att hämta en befintlig avbildning från Docker Hub.

```powershell
docker pull microsoft/aci-helloworld
```

Avbildningen måste vara taggad med namnet på ACR-inloggningsservern. Använd kommandot [docker tag][docker-tag] för att göra det.

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Distribuera avbildningen till ACI
Om du vill distribuera avbildningen som en behållarinstans i Azure Container Instances (ACI) konverterar du först autentiseringsuppgifterna för registret till en PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Kör följande kommando för att distribuera behållaravbildningen från behållarregistret med 1 processorkärna och 1 GB minne:

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Du bör få ett inledande svar från Azure Resource Manager med information om din behållare. Om du vill övervaka statusen för behållaren och kontrollera när den körs upprepar du kommandot [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. Det ska ta mindre än en minut.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Exempel på utdata: `Succeeded`

## <a name="view-the-application"></a>Visa programmet
När distribueringen till ACI har genomförts hämtar du behållarens offentliga IP-adress med kommandot [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Exempel på utdata: `"13.72.74.222"`

Gå till den offentliga IP-adressen som visas i webbläsaren om du vill se programmet som körs. Det bör se ut ungefär så här:

![Hello World-app i webbläsaren][qs-portal-15]

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] till att ta bort resursgruppen, Azure Container Registry och alla Azure-behållaravbildningar.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I snabbstarten skapade du ett Azure Container Registry med Azure CLI och startade en instans av den via Azure Container Instances. Fortsätt till självstudien om Azure Container Instances om du vill titta närmare på ACI.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
