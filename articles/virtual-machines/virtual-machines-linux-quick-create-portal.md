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
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bcfd830a5e2f39f36460990cae7e84b04d9a5fbb
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Skapa en virtuell Linux-dator med Azure Portal

Det går att skapa virtuella datorer via Azure Portal. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera virtuella datorer och alla relaterade resurser. Den här snabbstarten beskriver hur man skapar en virtuell dator med Azure Portal. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du har ett befintligt SSH-nyckelpar kan du hoppa över det här steget. Om du använder en Windows-dator följer du instruktionerna [här](./virtual-machines-linux-ssh-from-windows.md). 

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

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. Välj en storlek för den virtuella datorn och klicka på **Välj**. 

    ![Välj en storlek för den virtuella datorn på portalbladet](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. På inställningsbladet väljer du **Ja** under **Use managed disks** (Använd hanterade diskar), låter standardinställningarna vara kvar för resten och klickar på **OK**.

6. På sammanfattningssidan klickar du på **Ok** för att starta distributionen av den virtuella datorn.

7. Klicka på den virtuella datorn om du vill övervaka distributionsstatus. Den virtuella datorn finns på instrumentpanelen för Azure Portal eller genom att välja **Virtuella datorer** på den vänstra menyn. När den virtuella datorn har skapats ändras statusen från till **Distribuerar** till **Körs**.

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts kan du skapa en SSH-anslutning med den virtuella datorn.

1. Klicka på knappen **Anslut** på bladet för den virtuella datorn. Anslutningsknappen visar en SSH-anslutningssträng som kan användas för att ansluta till den virtuella datorn.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Kör följande kommando för att skapa en SSH-session. Ersätt anslutningssträngen med den som du kopierade från Azure Portal.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen på bladet för den virtuella datorn och klickar på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

[Självstudie: Skapa virtuella datorer med hög tillgänglighet](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utforska exempel på distribution av virtuella datorer med CLI](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

