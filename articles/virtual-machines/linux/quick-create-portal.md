---
title: Snabbstart – Skapa en virtuell Linux-dator i Azure Portal | Microsoft Docs
description: I den här snabbstarten lär du dig hur du använder Azure Portal för att skapa en virtuell Linux-dator
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 8/16/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 189d6d0030264590986d6fe2af47d35705cfb08b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575799"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Linux-dator i Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure-portalen är ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. Den här snabbstarten visar hur du använder Azure-portalen för att distribuera en virtuell Linux-dator (VM) som kör Ubuntu 16.04 LTS. För att se hur den virtuella datorn fungerar i praktiken använder du sedan SSH för att ansluta till den virtuella datorn och installerar NGINX-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Öppna bash-gränssnittet och använd [ssh-keygen](https://www.ssh.com/ssh/keygen/) för att skapa ett SSH-nyckelpar. Ett exempel kommando för att göra detta visas nedan. Om du inte har något bash-gränssnitt på den lokala datorn kan du använda [Azure Cloud Shell](https://shell.azure.com/bash).

```bash
ssh-keygen -t rsa -b 2048
```

Du uppmanas att ange en fil som nyckel paret ska sparas i. Du kan antingen ange en viss filplats eller bara trycka på RETUR för att spara på standard platsen som anges inom hakparenteser. Då uppmanas du att ange en lösen fras. Du kan ange en lösen fras för SSH-nyckeln eller så kan du trycka på RETUR för att fortsätta utan en lösen fras.

```bash
[root@linuxvm ~]$ ssh-keygen -t rsa -b 2048
Enter the file in which to save the key (home/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your public key has been saved in /home/root/.ssh/id_rsa.pub.
The key fingerprint is: SHA256:kkQS13gbaxevy4ULH0mW6wLIkcFm0twx/rIlSo1fIqU
The key's randomart image is:
+---[RSA 2018]----+
|   +oo=+         |
|  . B=o.+ .      |
|   + o+. + +     |
|    o* o+ = .    |
|   .EoB.S+ =     |
|   .o+.O. * .    |
|    . o. = =     |
|        . *      |
|         .       |
+----[SHA256]-----+
```
Kommandot genererar offentliga och privata nycklar med standard `id_rsa` namnet i `~/.ssh directory`. `ssh-keygen` Kommandot returnerar den fullständiga sökvägen till den offentliga nyckeln. Använd sökvägen till den offentliga nyckeln för att visa innehållet med `cat`.

```bash
cat ~/.ssh/id_rsa.pub
```

>[!NOTE]
> Om du väljer att spara SSH-nyckeln på en annan plats än standardvärdet måste du använda den platsen när du kör`cat`

Spara kommandots utdata. Detta är din offentliga nyckel och du behöver den när du konfigurerar ditt administratörs konto för att logga in på den virtuella datorn.

Mer information om ssh-keygen-kommandot finns på [sidan man](https://linux.die.net/man/1/ssh-keygen).

Om du använder en Windows-dator och vill veta mer om hur du skapar SSH-nyckelpar, inklusive användningen av SparaTillFil, kan du läsa [hur du använder SSH-nycklar med Windows](ssh-from-windows.md).

Om du skapar SSH-nyckelpar med Cloud Shell kommer det att lagras i en Azure-filresurs som [monteras automatiskt av Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Ta inte bort den här filresursen eller lagringskontot förrän du har hämtat dina nycklar eller om du förlorat åtkomst till den virtuella datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.

1. I sökrutan ovanför listan över Azure Marketplace-resurser söker du efter **Ubuntu Server 18,04** och väljer Ubuntu 18,04 LTS-erbjudandet och väljer sedan **skapa**.

1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** under **Resursgrupp**. I popup-fönstret skriver du *myResourceGroup* för namnet på resursgruppen och väljer sedan **OK**.

    ![Skapa en ny resursgrupp för din virtuella dator](./media/quick-create-portal/project-details.png)

1. Under **Instansinformation** skriver du *myVM* för **Namn på virtuell dator** och väljer *USA, östra* som **Region**. Låt de övriga standardvärdena vara som de är.

    ![Avsnittet Instansinformation](./media/quick-create-portal/instance-details.png)

1. Under **administratörs konto**väljer du **Offentlig SSH-nyckel**, skriver ditt användar namn och klistrar sedan in den offentliga nyckeln som du sparade tidigare i text rutan. Ta bort eventuella inledande eller avslutande blanksteg i din offentliga nyckel.

    ![Administratörskonto](./media/quick-create-portal/administrator-account.png)

1. Under **Regler för inkommande portar** > **Offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **SSH (22)** och **HTTP (80)** från listrutan.

    ![Öppna portar för RDP och HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

1. På sidan **Skapa en virtuell dator** kan du se information om den virtuella datorn som du håller på att skapa. När du är klar väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en SSH-anslutning med den virtuella datorn.

1. Välj **Anslut**-knappen på översiktssidan för din virtuella dator.

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta via IP-adress via port 22. I **Logga in med lokalt virtuellt datorkonto** visas ett anslutningskommando. Klicka på knappen för att kopiera kommandot. Följande exempel visar hur SSH-anslutningskommandot ser ut:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Med samma bash-gränssnitt som du använde för att skapa ett SSH-nyckelpar (t. ex. [Azure Cloud Shell](https://shell.azure.com/bash) eller ditt lokala bash-gränssnitt) klistrar du in ssh-anslutningen i gränssnittet för att skapa en SSH-session.

## <a name="install-web-server"></a>Installera webbservern

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Från din SSH-session uppdaterar du dina paketkällor och installera det senaste NGINX-paketet.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen genom att skriva `exit`.


## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

Använd valfri webbläsare för att visa välkomstsidan för NGINX. Ange den virtuella datorns offentliga IP-adress som webbadress. Den offentliga IP-adressen kan hittas på översiktssidan för den virtuella datorn eller som en del av SSH-anslutningssträngen du använde tidigare.

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, skapade en nätverkssäkerhetsgrupp och en regel och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
