---
title: Snabbstart – Skapa en Virtuell Windows-dator med Azure CLI
description: I den här snabbstarten får du lära dig hur du använder Azure CLI för att skapa en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 37f86f480e0d2a46bb54ad9b041fb1892a9ebbc4
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458172"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Snabbstart: Skapa en virtuell Windows-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI för att distribuera en virtuell dator (VM) i Azure som kör Windows Server 2016. För att se hur den virtuella datorn fungerar i praktiken ansluter du till den med RDP och installerar IIS-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/bash](https://shell.azure.com/bash)en separat webbläsarflik genom att gå till . Välj **Kopiera** om du vill kopiera kodblocken, klistra in den i Molnskalet och tryck på **Retur** för att köra den.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM*. I det här exemplet används *azureuser* för ett administrativt användarnamn. 

Du måste ange ett lösenord som uppfyller [lösenordskraven för virtuella Azure-datorer](/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm
). Med hjälp av exemplet nedan uppmanas du att ange ett lösenord på kommandoraden. Du kan också `--admin-password` lägga till parametern med ett värde för ditt lösenord. Användarnamnet och lösenordet används senare när du ansluter till den virtuella datorn.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Skriv ned din egen `publicIpAddress` i utdataresultatet från din virtuella dator. Den här adressen används för att ansluta till den virtuella datorn i nästa steg.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Som standard öppnas endast RDP-anslutningar när du skapar en virtuell Windows-dator i Azure. Använd [az vm open-port](/cli/azure/vm) för att öppna TCP port 80 för användning med IIS-webbservern:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Använd följande kommando för att skapa en fjärrskrivbordssession från den lokala datorn. Ersätt IP-adressen med den virtuella datorns offentliga IP-adress. När du uppmanas att göra det anger du autentiseringsuppgifterna som användes när den virtuella datorn skapades:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Installera webbservern

Installera IIS-webbservern för att se hur den virtuella datorn fungerar i praktiken. Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När kommandot har körts stänger du RDP-anslutningen till den virtuella datorn.

## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

När IIS har installerats och port 80 är öppen på den virtuella datorn från Internet använder du valfri webbläsare för att visa standardvälkomstsidan för IIS. Använd den virtuella datorns offentliga IP-adress, som du hämtade i ett tidigare steg. I följande exempel visas standardwebbplatsen för IIS:

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
