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
ms.date: 03/20/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 0f9ff487e289eadb857508134b7e08b00360fdd3
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Skapa en virtuell Windows-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur man använder Azure CLI för att distribuera en virtuell dator som kör Windows Server 2016.

Kontrollera att Azure CLI har installerats innan du börjar. Mer information finns i [installationsguiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm#create). 

Följande exempel skapar en virtuell dator med namnet `myVM`. Det här exemplet använder `azureuser` som administrativt användarnamn och ` myPassword12` som lösenord. Uppdatera dessa värden till något som är lämpligt för din miljö. Dessa värden krävs när du skapar en anslutning med den virtuella datorn.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna den offentliga IP-adressen. Den här adressen används för att få åtkomst till den virtuella datorn.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med offentliga IP-adressen för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

När den inte längre behövs kan följande kommando användas för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudier om att installera en roll och konfigurera brandvägg](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Utforska exempel på distribution av virtuella datorer med CLI](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
