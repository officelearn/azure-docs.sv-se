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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 25ffd1c5bf289fa98a4b350eb916b386eee7f05c
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Skapa en virtuell Linux-dator med Azure Portal

Det går att skapa virtuella datorer via Azure Portal. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera virtuella datorer och alla relaterade resurser. Den här snabbstarten beskriver hur man skapar en virtuell dator med Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du har ett befintligt SSH-nyckelpar kan du hoppa över det här steget. Om du använder en Windows-dator följer du instruktionerna [här](ssh-from-windows.md). 

Kör det här kommandot från ett Bash-kommandogränssnitt och följ instruktionerna på skärmen. Kommandoutdata innehåller filnamnet för den offentliga nyckeln. Innehållet i den här filen behövs när du skapar den virtuella datorn.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Välj **Beräkna** på bladet **Nytt**, välj **Ubuntu Server 16.04 LTS** på bladet **Beräkna** och klicka sedan på knappen **Skapa**.

3. Fyll i formuläret **Grundinställningar** för den virtuella datorn. Välj **SSH** för **Autentiseringstyp**. När du klistrar in den **offentliga SSH-nyckeln** ska du tänka på att ta bort eventuella inledande eller avslutande blanksteg. Skapa en ny för **Resursgrupp**. En resursgrupp är en logisk behållare där Azure-resurser skapas och hanteras gemensamt. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/quick-create-portal/create-vm-portal-basic-blade.png)  

4. Välj en storlek för den virtuella datorn och klicka på **Välj**. 

    ![Välj en storlek för den virtuella datorn på portalbladet](./media/quick-create-portal/create-vm-portal-size-blade.png)

5. På inställningsbladet väljer du **Ja** under **Use managed disks** (Använd hanterade diskar), låter standardinställningarna vara kvar för resten och klickar på **OK**.

6. På sammanfattningssidan klickar du på **Ok** för att starta distributionen av den virtuella datorn.

7. Klicka på den virtuella datorn om du vill övervaka distributionsstatus. Den virtuella datorn finns på instrumentpanelen för Azure Portal eller genom att välja **Virtuella datorer** på den vänstra menyn. När den virtuella datorn har skapats ändras statusen från till **Distribuerar** till **Körs**.


## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Som standard tillåts enbart SSH-anslutningar till virtuella Linux-datorer distribuerade i Azure. Om den här virtuella datorn kommer att vara en webbserver måste du öppna port 80 för webbtrafik. Det här steget vägleder dig genom processen med att skapa en regel för en nätverkssäkerhetsgrupp (NSG) för att tillåta inkommande anslutningar på port 80.

1. På bladet för den virtuella datorn i avsnittet **Essentials** klickar du på namnet på **Resursgruppen**.
2. Gå till bladet för resursgruppen och klicka på **Nätverkssäkerhetsgrupp** i listan över resurser. NSG-namnet bör vara namnet på den virtuella datorn med ”- nsg” tillagt i slutet.
3. Klicka på rubriken **Ingående säkerhetsregel** så öppnas listan med regler för inkommande trafik. Du bör se en regel för RDP i listan redan.
4. Klicka på **+ Lägg till** så öppnas bladet **Lägg till ingående säkerhetsregel**.
5. Under **Namn** skriver du **nginx** och ser till att **Portintervall** har angetts till 80 och **Åtgärd** till **Tillåt** och klickar sedan på **OK**.


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts kan du skapa en SSH-anslutning med den virtuella datorn.

1. Klicka på knappen **Anslut** på bladet för den virtuella datorn. Anslutningsknappen visar en SSH-anslutningssträng som kan användas för att ansluta till den virtuella datorn.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Kör följande kommando för att skapa en SSH-session. Ersätt anslutningssträngen med den som du kopierade från Azure Portal.

```bash 
ssh <replace with IP address>
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

## <a name="view-the-ngix-welcome-page"></a>Visa NGIX-välkomstsidan

Du kan använda en webbläsare som du väljer för att visa välkomstsidan till NGINX när NGINX är installerat och port 80 nu är öppen på en virtuell dator från Internet. Se till att använda den `publicIpAddress` som du har dokumenterat ovan för att besöka standardsidan. 

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen på bladet för den virtuella datorn och klickar på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

[Självstudie: Skapa virtuella datorer med hög tillgänglighet](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utforska exempel på distribution av virtuella datorer med CLI](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

