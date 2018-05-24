---
title: Snabbstart – Skapa din första Azure Container Instances-behållare med PowerShell
description: I den här snabbstarten använder du Azure PowerShell för att distribuera en Windows-behållare i Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Snabbstart: Skapa din första behållare i Azure Container Instances

Azure Container Instances gör det enkelt att skapa och hantera Docker-behållare i Azure, utan att behöva etablera virtuella datorer eller gå upp till en högre tjänstnivå. I den här snabbstarten skapar du en Windows-behållare i Azure och gör den tillgänglig på Internet med ett fullständigt kvalificerat domännamn (FQDN). Den här åtgärden utförs med ett enda kommando. Inom kort kan du se programmet köras i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][qs-powershell-01]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 5.5 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Skapa en behållare

Du kan skapa en behållare genom att ange ett namn, en Docker-avbildning och en Azure-resursgrupp i cmdleten [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Du kan också göra behållaren tillgänglig på Internet med hjälp av en DNS-namnetikett.

Kör följande kommando för att starta en Nano Server-behållare som kör Internet Information Services (IIS). Värdet `-DnsNameLabel` måste vara unikt i den Azure-region där du skapar instansen, så du kan behöva ändra värdet för att det ska vara unikt.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Om några sekunder bör du få ett svar på din begäran. Först har behållaren statusen **Creating** (skapas) men den bör starta inom någon minut. Du kan kontrollera status för distributionen med hjälp av cmdleten [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Behållarens etableringsstatus, fullständiga kvalificerade domännamn (FQDN) och IP-adress visas i cmdletens utdata:

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

När behållaren **ProvisioningState** flyttas till `Succeeded` navigerar du till dess `Fqdn` i webbläsaren:

![IIS som distribuerats via Azure Container Instances visas i webbläsaren][qs-powershell-01]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren kan du ta bort den med cmdleten [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Azure-behållarinstans utifrån en avbildning som finns i det offentliga Docker Hub-registret. Om du vill skapa en behållare på egen hand och distribuera den till Azure Container Instances från ett privat Azure-behållarregister, går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
