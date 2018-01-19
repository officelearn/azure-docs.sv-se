---
title: "Snabbstart – Skapa din första Azure Container Instances-behållare med PowerShell"
description: "Kom igång med Azure Container Instances genom att skapa en Windows-behållarinstans med PowerShell."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 18a342fed7e99e82082764d6f5a3429a3ce794b7
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Skapa din första behållare i Azure Container Instances

Azure Container Instances gör det enkelt att skapa och hantera Docker-behållare i Azure, utan att behöva etablera virtuella datorer eller gå upp till en högre tjänstnivå.

I den här snabbstarten skapar du en Windows-behållare i Azure och gör den tillgänglig på Internet med en offentlig IP-adress. Den här åtgärden utförs med ett enda kommando. Inom kort kan du se programmet köras i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][qs-powershell-01]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Skapa en behållare

Du kan skapa en behållare genom att ange ett namn, en Docker-avbildning och en Azure-resursgrupp i cmdleten [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Du kan också göra behållaren tillgänglig på Internet med en offentlig IP-adress. I detta fall använder vi en Nano Server-behållare som kör Internet Information Services (IIS).

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Om några sekunder bör du få ett svar på din begäran. Först har behållaren statusen **Creating** (skapas) men den bör starta inom någon minut. Du kan kontrollera status med hjälp av cmdleten [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Behållarens etableringsstatus och IP-adress visas i cmdletens utdata:

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

När behållarens status **ProvisioningState** övergår till `Succeeded` kan du nå den via webbläsaren med den angivna IP-adressen.

![IIS som distribuerats via Azure Container Instances visas i webbläsaren][qs-powershell-01]

## <a name="delete-the-container"></a>Ta bort behållaren

När du är klar med behållaren kan du ta bort den med cmdleten [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup]:

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du startat en färdig Windows-behållare i Azure Container Instances. Om du vill försöka skapa en behållare på egen hand och distribuera den till Azure Container Instances via Azure Container Registry, går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
