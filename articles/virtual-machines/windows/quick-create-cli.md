---
title: "Azure snabbstart – skapa virtuell Windows-dator med CLI | Microsoft Docs"
description: "Lär dig att skapa en virtuell Windows-dator med Azure CLI."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 01321cb74cce35fc01824d2c6c67211caab33258
ms.contentlocale: sv-se
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Skapa en virtuell Windows-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur man använder Azure CLI för att distribuera en virtuell dator som kör Windows Server 2016. När distributionen är klar kan vi ansluta till servern och installera IIS.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm#create). 

Följande exempel skapar en virtuell dator med namnet *myVM*. Det här exemplet använder *azureuser* som administrativt användarnamn och *myPassword12* som lösenord. Uppdatera dessa värden till något som är lämpligt för din miljö. Dessa värden krävs när du skapar en anslutning med den virtuella datorn.

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAaddress`. Den här adressen används för att få åtkomst till den virtuella datorn.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Som standard tillåts enbart RDP-anslutningar i virtuella Windows-datorer som distribueras i Azure. Om den här virtuella datorn kommer att vara en webbserver måste du öppna port 80 från Internet. Använd kommandot [az vm open-port](/cli/azure/vm#open-port) för att öppna önskad port.  
 
 ```azurecli-interactive  
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med offentliga IP-adressen för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installera IIS med PowerShell

Nu när du har loggat in till den virtuella Azure-datorn kan du använda en rad med PowerShell för att installera IIS och aktivera den lokala brandväggsregeln så att del tillåter webbtrafik. Öppna en PowerShell-kommandotolk och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du själv väljer för att visa standardvälkomstsidan för IIS när IIS är installerat och port 80 nu är öppen på den virtuella datorn från Internet. Se till att använda den offentliga IP-adress som du har dokumenterat ovan för att besöka standardsidan. 

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en virtuell dator och en regel för nätverkssäkerhetsgrupp samt installerat en webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)

