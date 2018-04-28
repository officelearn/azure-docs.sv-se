---
title: Skapa en virtuell dator för Windows Azure-stacken med Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en virtuell Windows-dator på Azure-stacken använder Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 5665af14b9b0d0705b68c8a27c593b19c31b053e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-stack-using-azure-cli"></a>Snabbstart: skapa en virtuell Windows-dator i Azure-stacken använder Azure CLI

Azure CLI används för att skapa och hantera Azure-stacken resurser från kommandoraden. Den här artikeln visar hur du använder Azure CLI för att skapa och få åtkomst till en virtuell dator i Windows Server 2016 i Azure-stacken.

## <a name="prerequisites"></a>Förutsättningar

* Kontrollera att Azure Stack-operator har lagt till ”Windows Server 2016” bilden Stack för Azure marketplace.

* Azure-stacken kräver en viss version av Azure CLI för att skapa och hantera resurser. Om du inte har Azure CLI har konfigurerats för Azure-stacken, följer du stegen för att [installera och konfigurera Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare i vilka Azure-stacken resurser distribueras och hanteras. Från din development kit eller Azure-stacken integrerat system som kör den [az gruppen skapa](/cli/azure/group#az_group_create) kommando för att skapa en resursgrupp. Tilldelar värden för alla variabler i det här dokumentet kan du använda dessa värden eller tilldela nya värden. I följande exempel skapas en resursgrupp med namnet myResourceGroup i den lokala platsen.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med hjälp av den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. I följande exempel skapas en virtuell dator med namnet myVM. Det här exemplet används Demouser för ett användarnamn och Demouser@123 som lösenord. Uppdatera dessa värden till något som är lämpligt för din miljö. Dessa värden krävs när du ansluter till den virtuella datorn.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

När den virtuella datorn skapas i *PublicIPAddress* parameter visas. Skriv ned den här adressen eftersom du behöver komma åt den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Som standard tillåts bara en RDP-anslutningar till en virtuell dator för Windows som distribuerats i Azure-stacken. Om den här virtuella datorn kommer att vara en webbserver måste du öppna port 80 från Internet. Använd kommandot [az vm open-port](/cli/azure/vm#open-port) för att öppna önskad port.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med offentliga IP-adressen för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installera IIS med PowerShell

Du kan använda en rad med PowerShell när du loggar in på Azure-dator för att installera IIS och aktivera regeln för lokala brandväggen att tillåta webbtrafik. Öppna en PowerShell-kommandotolk och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du väljer för att visa välkomstsidan för IIS. Använd den offentliga IP-adressen som beskrivs i föregående avsnitt för att besöka sidan.

![Standardwebbplatsen i IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#az_group_delete) kommandot för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en enkel Windows virtuell dator i den här snabbstarten. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
