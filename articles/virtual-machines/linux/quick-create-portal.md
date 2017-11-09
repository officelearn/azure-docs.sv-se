---
title: "Azure snabbstart – skapa virtuell dator med Portal | Microsoft Docs"
description: "Azure snabbstart – skapa virtuell dator med Portal"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6ac6ed21f3cf363137381b82835a11d0920aee3b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Skapa en virtuell Linux-dator med Azure Portal

Det går att skapa virtuella datorer via Azure Portal. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera virtuella datorer och alla relaterade resurser. Den här snabbstartsguide genom att skapa en virtuell dator och installera en webbserver på den virtuella datorn.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du har ett befintligt SSH-nyckelpar kan du hoppa över det här steget.

Kör det här kommandot från ett Bash-kommandogränssnitt och följ instruktionerna på skärmen. Kommandoutdata innehåller filnamnet för den offentliga nyckeln. Kopiera innehållet i en fil för offentlig nyckel till Urklipp.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**. 

3. Ange informationen för den virtuella datorn. Välj **Offentlig SSH-nyckel** som **Autentiseringstyp**. När du klistrar in den offentliga SSH-nyckeln ska du tänka på att ta bort eventuella inledande eller avslutande blanksteg. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 

    ![Skärmbild som visar storlekar på virtuella datorer](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. Under **Inställningar** behåller du standardvärdena och klickar på **OK**.

6. På sammanfattningssidan klickar du på **Ok** för att starta distributionen av den virtuella datorn.

7. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt.


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en SSH-anslutning med den virtuella datorn.

1. Klicka på knappen **Anslut** på den virtuella datorns egenskaper. Anslutningsknappen visar en SSH-anslutningssträng som kan användas för att ansluta till den virtuella datorn.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Kör följande kommando för att skapa en SSH-session. Ersätt anslutningssträngen med den som du kopierade från Azure Portal.

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>Installera NGINX

Använd följande bash-skript för att uppdatera paketkällor och installera det senaste NGINX-paketet. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

När det är klart avslutar du SSH-sessionen och återgår till VM-egenskaperna i Azure Portal.


## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

En nätverkssäkerhetsgrupp (NSG) säkrar ingående och utgående trafik. När en VM skapas från Azure Portal skapas en regel för inkommande trafik på port 22 för SSH-anslutningar. Eftersom denna VM är värd för en webbserver måste du skapa en NSG-regel för port 80.

1. Klicka på namnet på den virtuella datorns **resursgrupp**.
2. Välj **nätverkssäkerhetsgruppen**. Du kan identifiera NSG med kolumnen **Typ**. 
3. På menyn till vänster under inställningarna klickar du på **Ingående säkerhetsregel**.
4. Klicka på **Lägg till**.
5. Skriv **http** i fältet **Namn**. Kontrollera att 80 har angetts för **Portintervall** och att **Tillåt** har angetts för **Åtgärd**. 
6. Klicka på **OK**.


## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

När NGINX är installerat och port 80 är öppen för din VM kan webbservern nu nås från internet. Öppna en webbläsare och ange den virtuella datorns offentliga IP-adress. Den offentliga IP-adressen finns på VM-egenskaper i Azure-portalen.

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det, välja en resursgrupp för den virtuella datorn och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en virtuell dator och en regel för nätverkssäkerhetsgrupp samt installerat en webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
