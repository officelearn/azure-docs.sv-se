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
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 2a4eb909c39051ce9fa2efd7e7997644d9b8b1b1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Skapa en virtuell dator för Windows Azure-stacken med Azure CLI

Azure CLI används för att skapa och hantera Azure-stacken resurser från kommandoraden. Den här guiden information med Azure CLI för att skapa en virtuell Windows Server 2016-dator i Azure-stacken. När du har skapat den virtuella datorn ska du ansluta med fjärrskrivbord kan installera IIS, sedan visa standardwebbplatsen. 

## <a name="prerequisites"></a>Förutsättningar 

* Kontrollera att Azure Stack-operator har lagt till ”Windows Server 2016” bilden Stack för Azure marketplace.  

* Azure-stacken kräver en viss version av Azure CLI för att skapa och hantera resurser. Om du inte har Azure CLI har konfigurerats för Azure-stacken, följer du stegen för att [installera och konfigurera Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare i vilka Azure-stacken resurser distribueras och hanteras. Från din development kit eller Azure-stacken integrerat system som kör den [az gruppen skapa](/cli/azure/group#az_group_create) kommando för att skapa en resursgrupp. Vi har tilldelat värden för alla variabler i det här dokumentet kan du använda dem som är eller tilldela ett annat värde. I följande exempel skapas en resursgrupp med namnet myResourceGroup i den lokala platsen.

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

När den virtuella datorn skapas, anteckna den *PublicIPAddress* parameter som är utdata som du ska använda för att få åtkomst till den virtuella datorn.
 
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

Nu när du har loggat in till den virtuella Azure-datorn kan du använda en rad med PowerShell för att installera IIS och aktivera den lokala brandväggsregeln så att del tillåter webbtrafik. Öppna en PowerShell-kommandotolk och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du själv väljer för att visa standardvälkomstsidan för IIS när IIS är installerat och port 80 nu är öppen på den virtuella datorn från Internet. Se till att använda den offentliga IP-adress som du har dokumenterat ovan för att besöka standardsidan. 

![Standardwebbplatsen i IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en enkel Windows virtuell dator i den här snabbstarten. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
