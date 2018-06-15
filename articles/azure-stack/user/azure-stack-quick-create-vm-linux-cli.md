---
title: Skapa en virtuell Linux-dator med hjälp av Azure CLI i Azure-stacken | Microsoft Docs
description: Skapa en virtuell Linux-dator med CLI i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32156699"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Snabbstart: skapa en virtuell Linux-server-dator med hjälp av Azure CLI i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan skapa en virtuell Ubuntu Server 16.04 LTS-dator med hjälp av Azure CLI. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln kan du också stegen för att:

* Ansluta till den virtuella datorn med en fjärransluten klient.
* Installera NGINX-webbserver och visa standardstartsida.
* Rensa oanvända resurser.

## <a name="prerequisites"></a>Förutsättningar

* **En Linux-avbildning i Azure-stacken marketplace**

   Stacken för Azure marketplace innehåller inte en Linux-avbildning som standard. Hämta Azure Stack-operatorn för att ange den **Ubuntu Server 16.04 LTS** bilden som du behöver. Operatorn kan använda stegen som beskrivs i den [hämta marketplace-objekt från Azure till Azure-stacken](../azure-stack-download-azure-marketplace-item.md) artikel.

* Azure-stacken kräver en viss version av Azure CLI för att skapa och hantera resurser. Om du inte har Azure CLI som konfigurerats för Azure-stacken, logga in på den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) och följ stegen för att [ Installera och konfigurera Azure CLI](azure-stack-version-profiles-azurecli2.md).

* En offentlig SSH-nyckel med namnet-id_rsa.pub sparas i katalogen .ssh på din Windows-användarprofil. Detaljerad information om hur du skapar SSH-nycklar finns [skapa SSH-nycklar i Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där du kan distribuera och hantera resurser i Azure-stacken. Från din development kit eller Azure-stacken integrerat system som kör den [az gruppen skapa](/cli/azure/group#az_group_create) kommando för att skapa en resursgrupp.

>[!NOTE]
 Värden har tilldelats för alla variabler i kodexemplen. Du kan tilldela nya värden om du vill.

I följande exempel skapas en resursgrupp med namnet myResourceGroup i den lokala platsen.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med hjälp av den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. I följande exempel skapas en virtuell dator med namnet myVM. Det här exemplet används Demouser för ett användarnamn och Demouser@123 som användarens lösenord. Ändra dessa värden till något som är lämplig för din miljö.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Offentlig IP-adress returneras den **PublicIpAddress** parameter. Skriv ned den här adressen eftersom du behöver komma åt den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Eftersom den här virtuella datorn ska köra IIS-webbservern, måste du öppna port 80 för Internet-trafiken. Använd kommandot [az vm open-port](/cli/azure/vm#open-port) för att öppna önskad port.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Använda SSH för att ansluta till den virtuella datorn

Ansluta till den virtuella datorn från en klientdator med SSH installerad. Om du arbetar på en Windows-klient kan använda [Putty](http://www.putty.org/) att skapa anslutningen. För att ansluta till den virtuella datorn, använder du följande kommando:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Installera NGINX-webbserver

Om du vill uppdatera paketet resurser och installera det senaste NGINX-paketet, kör du följande skript:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

Med NGINX installerat, och port 80 som är öppna på den virtuella datorn, kan du komma åt webbservern med hjälp av den virtuella datorns offentliga IP-adressen. Öppna en webbläsare och gå till ```http://<public IP address>```.

![Välkomstsida för NGINX web server](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Du kan använda den [ta bort grupp az](/cli/azure/group#az_group_delete) kommando för att ta bort dessa resurser. Ta bort resursgruppen och alla dess resurser genom att köra följande kommando:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I denna Snabbstart distribuerade virtuella datorn för grundläggande Linux-server med en webbserver. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
