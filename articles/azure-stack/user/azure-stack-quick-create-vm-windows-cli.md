---
title: Skapa en Windows-dator på Azure Stack med Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en Windows-VM på Azure Stack med Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: be4e16b1d20aa07e4851174e982e2f1f2a23d893
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44376994"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Snabbstart: skapa en Windows Server-dator med hjälp av Azure CLI i Azure Stack

‎*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan skapa en virtuell Windows Server 2016-dator med hjälp av Azure CLI. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln ger dig också följande steg:

* Anslut till den virtuella datorn med en fjärransluten klient.
* Installera IIS-webbservern och visa standardstartsidan.
* Rensa dina resurser.

## <a name="prerequisites"></a>Förutsättningar

* Se till att Azure Stack-operatör till den **Windows Server 2016** avbildning till Azure Stack marketplace.

* Azure Stack kräver en viss version av Azure CLI för att skapa och hantera resurserna. Om du inte har Azure CLI som konfigurerats för Azure Stack, följer du stegen för att [installera och konfigurera Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där du kan distribuera och hantera Azure Stack-resurser. Från Azure Stack-miljön, kör den [az gruppen skapa](/cli/azure/group#az-group-create) kommando för att skapa en resursgrupp.

>[!NOTE]
 Värdena tilldelas för alla variabler i kodexemplen. Du kan dock tilldela nya värden om du vill.

I följande exempel skapas en resursgrupp med namnet myResourceGroup i den lokala platsen.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator (VM) med hjälp av den [az vm skapa](/cli/azure/vm#az-vm-create) kommando. I följande exempel skapas en virtuell dator med namnet myVM. Det här exemplet används Demouser för administrativt användarnamn och Demouser@123 som användarens lösenord. Ändra dessa värden till något som är lämplig för din miljö.

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

När den virtuella datorn skapas, den **PublicIPAddress** parametern i utdata innehåller den offentliga IP-adressen för den virtuella datorn. Anteckna den här adressen eftersom du behöver komma åt den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Eftersom den här Virtuella datorn kommer att köra IIS-webbservern, måste du öppna port 80 för Internet-trafiken.

Använd den [az vm open-port](/cli/azure/vm#open-port) kommando för att öppna port 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Använd nästa kommando för att skapa en fjärrskrivbordsanslutning till den virtuella datorn. Ersätt ”offentlig IP-adress” med IP-adressen för den virtuella datorn. När du uppmanas, anger du användarnamnet och lösenordet som du använde för den virtuella datorn.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installera IIS med PowerShell

Nu när du har loggat in till den virtuella datorn, kan du använda PowerShell för att installera IIS. Starta PowerShell på den virtuella datorn och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare om du väljer för att visa välkomstsidan för IIS. Använd den offentliga IP-adressen som beskrevs i föregående avsnitt för att besöka standardsidan.

![Standardwebbplatsen i IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Använd den [az group delete](/cli/azure/group#az-group-delete) att ta bort resursgruppen, den virtuella datorn, och alla relaterade resurser.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har distribuerat du en grundläggande Windows Server-datorn. Om du vill veta mer om Azure Stack-datorer kan fortsätta att [överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md).
