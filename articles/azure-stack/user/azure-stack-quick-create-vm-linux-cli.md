---
title: "Skapa en virtuell Linux-dator med hjälp av Azure CLI i Azure-stacken | Microsoft Docs"
description: Skapa en virtuell Linux-dator med CLI i Azure-stacken.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 160893f5b2e9c78bd62355782c279fb08f7f6b48
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Skapa en virtuell Linux-dator med hjälp av Azure CLI i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Azure CLI används för att skapa och hantera Azure-stacken resurser från kommandoraden. Den här snabbstartsguide information med Azure CLI för att skapa en virtuell Linux-dator i Azure-stacken.  När den virtuella datorn skapas en webbserver är installerad och port 80 är öppna för att möjliggöra Internet-trafik.

## <a name="prerequisites"></a>Förutsättningar 

* Kontrollera att Azure Stack-operator har lagt till ”Ubuntu Server 16.04 LTS” bilden Stack för Azure marketplace. 

* Azure-stacken kräver en viss version av Azure CLI för att skapa och hantera resurser. Om du inte har Azure CLI har konfigurerats för Azure-stacken, logga in på den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) och följ stegen för att [installera och konfigurera Azure CLI](azure-stack-connect-cli.md).

* En offentlig SSH-nyckel med namnet id_rsa.pub ska skapas i katalogen .ssh på din Windows-användarprofil. Detaljerad information om hur du skapar SSH-nycklar finns [skapa SSH-nycklar i Windows](../../virtual-machines/linux/ssh-from-windows.md). 

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
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

När du är klar, kommer kommandot utdata parametrar för den virtuella datorn.  Anteckna den *PublicIPAddress*eftersom du kan använda detta för att ansluta och hantera den virtuella datorn.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Som standard tillåts enbart SSH-anslutningar till virtuella Linux-datorer distribuerade i Azure. Om den här virtuella datorn kommer att vara en webbserver måste du öppna port 80 från Internet. Använd kommandot [az vm open-port](/cli/azure/vm#open-port) för att öppna önskad port.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Från en dator med SSH installerat använder du följande kommando för att ansluta till den virtuella datorn. Om du arbetar med Windows kan du skapa anslutningen med hjälp av [Putty](http://www.putty.org/). Se till att ersätta med korrekt offentlig IP-adressen för den virtuella datorn. I vårt exempel har IP-adressen 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installera NGINX

Använd följande bash-skript för att uppdatera paketkällor och installera det senaste NGINX-paketet. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

Du kan använda en webbläsare som du väljer för att visa välkomstsidan till NGINX när NGINX är installerat och port 80 nu är öppen på en virtuell dator från Internet. Se till att använda den *publicIpAddress* som du har dokumenterat ovan för att besöka standardsidan. 

![NGINX-standardwebbplats](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en enkel virtuell Linux-dator i den här snabbstarten. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).

