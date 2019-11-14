---
title: Snabb start – skapa en virtuell Linux-dator i Azure Portal
description: I den här snabb starten får du lära dig hur du använder Azure Portal för att skapa en virtuell Linux-dator.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 894fa2f3cda798a409db3ee8e9761c1702baf955
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035224"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Linux-dator i Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure Portal är ett webbläsarbaserat användar gränssnitt för att skapa Azure-resurser. Den här snabb starten visar hur du använder Azure Portal för att distribuera en virtuell Linux-dator (VM) som kör Ubuntu 18,04 LTS. För att se hur den virtuella datorn fungerar i praktiken använder du sedan SSH för att ansluta till den virtuella datorn och installerar NGINX-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Öppna bash-gränssnittet och använd [ssh-keygen](https://www.ssh.com/ssh/keygen/) för att skapa ett SSH-nyckelpar. Om du inte har något bash-gränssnitt på den lokala datorn kan du använda [Azure Cloud Shell](https://shell.azure.com/bash).


1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I menyn längst upp på sidan väljer du `>_` ikonen för att öppna Cloud Shell.
1. Se till att CloudShell säger **bash** i det övre vänstra hörnet. Om det står i PowerShell använder du List rutan för att välja **bash** och väljer **Bekräfta** för att ändra till bash-gränssnittet.
1. Skriv `ssh-keygen -t rsa -b 2048` för att skapa SSH-nyckeln. 
1. Du uppmanas att ange en fil som nyckel paret ska sparas i. Tryck på **RETUR** för att spara på standard platsen, som visas inom hak paren tes. 
1. Du uppmanas att ange en lösen fras. Du kan ange en lösen fras för SSH-nyckeln eller trycka på **RETUR** för att fortsätta utan en lösen fras.
1. Kommandot `ssh-keygen` genererar offentliga och privata nycklar med standard namnet för `id_rsa` i `~/.ssh directory`. Kommandot returnerar den fullständiga sökvägen till den offentliga nyckeln. Använd sökvägen till den offentliga nyckeln för att visa dess innehåll med `cat` genom att skriva `cat ~/.ssh/id_rsa.pub`.
1. Kopiera utdata för det här kommandot och spara det någonstans för att använda dem senare i den här artikeln. Detta är din offentliga nyckel och du behöver den när du konfigurerar ditt administratörs konto för att logga in på den virtuella datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com) om du inte redan gjort det.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Skriv **virtuella datorer** i sökningen.
1. Under **tjänster**väljer du **virtuella datorer**.
1. På sidan **virtuella datorer** väljer du **Lägg till**. Sidan **skapa en virtuell dator** öppnas.
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myResourceGroup* som namn. *. 

    ![Skapa en ny resursgrupp för din virtuella dator](./media/quick-create-portal/project-details.png)

1. Under **instans information**skriver du *myVM* för namnet på den **virtuella datorn**, väljer *östra usa* för din **region**och väljer *Ubuntu 18,04-LTS* för din **avbildning**. Låt de övriga standardvärdena vara som de är.

    ![Avsnittet Instansinformation](./media/quick-create-portal/instance-details.png)

1. Under **administratörs konto**väljer du **Offentlig SSH-nyckel**, skriver ditt användar namn och klistrar in den offentliga nyckeln. Ta bort eventuella inledande eller avslutande blanksteg i din offentliga nyckel.

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

3. Med samma bash-gränssnitt som du använde för att skapa ett SSH-nyckelpar (du kan öppna Cloud Shell igen genom att välja `>_` igen eller gå till https://shell.azure.com/bash)klistrar du in SSH-anslutningen i gränssnittet för att skapa en SSH-session.

## <a name="install-web-server"></a>Installera webbservern

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Från din SSH-session uppdaterar du dina paketkällor och installera det senaste NGINX-paketet.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen genom att skriva `exit`.


## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

Använd valfri webbläsare för att visa välkomstsidan för NGINX. Ange den offentliga IP-adressen för den virtuella datorn som webb adress. Den offentliga IP-adressen kan hittas på översiktssidan för den virtuella datorn eller som en del av SSH-anslutningssträngen du använde tidigare.

![NGINX-standardwebbplats](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, skapade en nätverkssäkerhetsgrupp och en regel och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
