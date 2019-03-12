---
title: Skapa en Linux-dator med hjälp av Azure CLI i Azure Stack | Microsoft Docs
description: Skapa en Linux-dator med CLI i Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b3fbd91ddf06d4515ee55d24ba9330d0af6dc124
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540333"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Snabbstart: skapa en Linux-server-dator med hjälp av Azure CLI i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan skapa en Ubuntu Server 16.04 LTS-dator med hjälp av Azure CLI. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln ger dig också hur du:

* Anslut till den virtuella datorn med en fjärransluten klient.
* Installera NGINX-webbservern och visa standardstartsidan.
* Rensa oanvända resurser.

## <a name="prerequisites"></a>Förutsättningar

* **En Linux-avbildning i Azure Stack marketplace**

   I Azure Stack marketplace innehåller inte en Linux-avbildning som standard. Hämta Azure Stack-operatör att tillhandahålla den **Ubuntu Server 16.04 LTS** avbildning som du behöver. Operatören kan använda stegen som beskrivs i den [hämta marketplace-objekt från Azure till Azure Stack](../azure-stack-download-azure-marketplace-item.md) artikeln.

* Azure Stack kräver en viss version av Azure CLI för att skapa och hantera resurserna. Om du inte har konfigurerats för Azure Stack Azure CLI kan logga in på den [Utvecklingskit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller en Windows-baserad externa klient om du är [är anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) och följ stegen för att [ Installera och konfigurera Azure CLI](azure-stack-version-profiles-azurecli2.md).

* En offentlig SSH-nyckel med namnet-id_rsa.pub sparas i .ssh-katalogen för din Windows-användarprofil. Detaljerad information om hur du skapar SSH-nycklar finns i [skapar SSH-nycklar på Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare där du kan distribuera och hantera Azure Stack-resurser. Från din development kit eller Azure Stack-integrerat system som kör den [az gruppen skapa](/cli/azure/group#az-group-create) kommando för att skapa en resursgrupp.

>[!NOTE]
 Värdena tilldelas för alla variabler i kodexemplen. Du kan dock tilldela nya värden om du vill.

I följande exempel skapas en resursgrupp med namnet myResourceGroup i den lokala platsen.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med hjälp av den [az vm skapa](/cli/azure/vm#az-vm-create) kommando. I följande exempel skapas en virtuell dator med namnet myVM. Det här exemplet används Demouser för administrativt användarnamn och Demouser@123 som användarens lösenord. Ändra dessa värden till något som är lämplig för din miljö.

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

Den offentliga IP-adressen returneras i de **PublicIpAddress** parametern. Anteckna den här adressen eftersom du behöver komma åt den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Eftersom den här virtuella datorn kommer att köra IIS-webbservern, måste du öppna port 80 för Internet-trafiken. Använd kommandot [az vm open-port](/cli/azure/vm) för att öppna önskad port.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Anslut till den virtuella datorn via SSH

Anslut till den virtuella datorn från en klientdator med SSH installerat. Om du arbetar på en Windows-klient kan använda [Putty](https://www.putty.org/) att skapa anslutningen. För att ansluta till den virtuella datorn, använder du följande kommando:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Installera NGINX-webbservern

Kör följande skript för att uppdatera paketresurser och installera det senaste NGINX-paketet:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

När NGINX är installerat och port 80 är öppen på den virtuella datorn kan du komma åt webbservern med hjälp av den virtuella datorns offentliga IP-adressen. Öppna en webbläsare och gå till ```http://<public IP address>```.

![Välkomstsidan för NGINX web server](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Du kan använda den [az group delete](/cli/azure/group#az-group-delete) till att ta bort de här resurserna. Om du vill ta bort resursgruppen och alla dess resurser, kör du följande kommando:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har distribuerat du en grundläggande server för Linux VM med en webbserver. Om du vill veta mer om Azure Stack-datorer kan fortsätta att [överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md).
