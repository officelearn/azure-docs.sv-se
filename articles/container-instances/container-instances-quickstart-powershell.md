---
title: "Snabbstart - skapa din första Azure Behållarinstanser behållare med PowerShell"
description: "Kom igång med Azure Container instanser genom att skapa en instans för Windows-behållaren med PowerShell."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fbd1bee04c5180beda23c04607b313eec9edcab4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Skapa din första behållare i Azure Container Instances

Azure Behållarinstanser som gör det enkelt att skapa och hantera Docker-behållare i Azure, utan att behöva etablera virtuella datorer eller anta en högre nivå tjänst.

I Snabbstart, skapa en Windows-behållare i Azure och exponera till internet med en offentlig IP-adress. Den här åtgärden utförs med ett enda kommando. Inom en liten stund visas detta i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][qs-powershell-01]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Denna Snabbstart kräver Azure PowerShell Modulversion 4.4 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Skapa en behållare

Du kan skapa en behållare genom att ange ett namn, en Docker-avbildning och en Azure-resursgrupp till den [ny AzureRmContainerGroup] [ New-AzureRmContainerGroup] cmdlet. Du kan också göra behållaren tillgänglig på Internet med en offentlig IP-adress. I detta fall använder vi en behållare för Windows Nano Server som kör Internet Information Services (IIS).

```powershell
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Du får ett svar på din begäran inom några sekunder. Först att behållaren i en **skapa** tillstånd, men det bör starta inom en minut eller två. Du kan kontrollera status med hjälp av den [Get-AzureRmContainerGroup] [ Get-AzureRmContainerGroup] cmdlet:

```powershell
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Behållarens etableringsstatusen och IP-adress visas i den cmdlet-utdatan:

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

När behållaren **ProvisioningState** flyttas till `Succeeded`, kan du nå den i din webbläsare med den angivna IP-adressen.

![IIS som distribueras med Azure Behållarinstanser som visas i webbläsare][qs-powershell-01]

## <a name="delete-the-container"></a>Ta bort behållaren

När du är klar med behållaren du kan ta bort den med hjälp av den [ta bort AzureRmContainerGroup] [ Remove-AzureRmContainerGroup] cmdlet:

```powershell
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten startas en förskapad Windows-behållare i Azure Container instanser. Om du vill försöka skapa en behållare själv och distribuera den till Azure Behållarinstanser använda registret för Azure-behållare fortsätter att Azure Behållarinstanser kursen.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudier](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png