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
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Snabbstart: skapa en virtuell dator med Windows Server med hjälp av Azure CLI i Azure-stacken

‎*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan skapa en virtuell dator i Windows Server 2016 med hjälp av Azure CLI. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln ger dig även följande steg:

* Ansluta till den virtuella datorn med en fjärransluten klient.
* Installera IIS-webbservern och visa standardstartsida.
* Rensa dina resurser.

## <a name="prerequisites"></a>Förutsättningar

* Kontrollera att Azure Stack-operatorn i **Windows Server 2016** avbildningen till stacken för Azure marketplace.

* Azure-stacken kräver en viss version av Azure CLI för att skapa och hantera resurser. Om du inte har Azure CLI har konfigurerats för Azure-stacken, följer du stegen för att [installera och konfigurera Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där du kan distribuera och hantera resurser i Azure-stacken. Kör från din Azure Stack-miljö i [az gruppen skapa](/cli/azure/group#az_group_create) kommando för att skapa en resursgrupp.

>[!NOTE]
 Värden har tilldelats för alla variabler i kodexemplen. Du kan tilldela nya värden om du vill.

I följande exempel skapas en resursgrupp med namnet myResourceGroup i den lokala platsen.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator (VM) med hjälp av den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. I följande exempel skapas en virtuell dator med namnet myVM. Det här exemplet används Demouser för ett användarnamn och Demouser@123 som användarens lösenord. Ändra dessa värden till något som är lämplig för din miljö.

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

När den virtuella datorn skapas i **PublicIPAddress** parametern i utdata innehåller den offentliga IP-adressen för den virtuella datorn. Skriv ned den här adressen eftersom du behöver komma åt den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Eftersom den här virtuella datorn ska köra IIS-webbservern, måste du öppna port 80 för Internet-trafiken.

Använd den [az vm öppna port](/cli/azure/vm#open-port) kommandot för att öppna port 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Använd nästa kommando för att skapa en fjärrskrivbordsanslutning till den virtuella datorn. Ersätt ”offentlig IP-adress” med IP-adressen för den virtuella datorn. När du uppmanas, anger du användarnamn och lösenord som du använde för den virtuella datorn.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installera IIS med PowerShell

Nu när du har loggat in till den virtuella datorn kan använda du PowerShell för att installera IIS. Starta PowerShell på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du väljer för att visa välkomstsidan för IIS. Använd den offentliga IP-adressen som beskrivs i föregående avsnitt för att besöka sidan.

![Standardwebbplatsen i IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Använd den [ta bort grupp az](/cli/azure/group#az_group_delete) kommandot för att ta bort resursgruppen, den virtuella datorn, och alla relaterade resurser.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade en grundläggande Windows Server-datorn. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
