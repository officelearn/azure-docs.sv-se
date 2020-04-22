---
title: Snabbstart - Skapa en virtuell Linux-dator i Azure-portalen
description: I den här snabbstarten får du lära dig hur du använder Azure-portalen för att skapa en virtuell Linux-dator.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6bf9a89a4806db53797191336578ef9148886181
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759233"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Linux-dator i Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure-portalen är ett webbläsarbaserat användargränssnitt för att skapa Azure-resurser. Den här snabbstarten visar hur du använder Azure-portalen för att distribuera en virtuell Linux-dator (VM) som kör Ubuntu 18.04 LTS. För att se hur den virtuella datorn fungerar i praktiken använder du sedan SSH för att ansluta till den virtuella datorn och installerar NGINX-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Öppna bash-gränssnittet och använd [ssh-keygen](https://www.ssh.com/ssh/keygen/) för att skapa ett SSH-nyckelpar. Om du inte har något bash-gränssnitt på den lokala datorn kan du använda [Azure Cloud Shell](https://shell.azure.com/bash).


1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj `>_` ikonen längst upp på sidan för att öppna Cloud Shell.
1. Se till att CloudShell säger **Bash** i det övre vänstra. Om det står PowerShell använder du listrutan för att välja **Bash** och väljer **Bekräfta** om du vill byta till Bash-skalet.
1. Skriv `ssh-keygen -t rsa -b 2048` för att skapa ssh-tangenten. 
1. Du uppmanas att ange en fil där du vill spara nyckelparet. Tryck bara på **Retur** för att spara på standardplatsen, som visas inom parentes. 
1. Du kommer att bli ombedd att ange en lösenfras. Du kan skriva en lösenfras för SSH-tangenten eller trycka på **Retur** för att fortsätta utan lösenfras.
1. Kommandot `ssh-keygen` genererar offentliga och privata nycklar med `id_rsa` standardnamnet i `~/.ssh directory`. Kommandot returnerar den fullständiga sökvägen till den offentliga nyckeln. Använd sökvägen till den offentliga nyckeln `cat` för `cat ~/.ssh/id_rsa.pub`att visa innehållet med genom att skriva .
1. Kopiera utdata från det här kommandot och spara det någonstans att använda senare i den här artikeln. Det här är din offentliga nyckel och du behöver den när du konfigurerar administratörskontot för att logga in på den virtuella datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) om du inte redan har gjort det.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Skriv **virtuella datorer** i sökningen.
1. Under **Tjänster**väljer du **Virtuella datorer**.
1. På sidan **Virtuella datorer** väljer du **Lägg till**. Sidan **Skapa en virtuell dator** öppnas.
1. På fliken **Grundläggande inställningar** går du till **Projektinformation**, kontrollerar att korrekt prenumeration har valts och väljer sedan **Skapa ny** för resursgruppen. Skriv *myResourceGroup* för namnet.*. 

    ![Skapa en ny resursgrupp för din virtuella dator](./media/quick-create-portal/project-details.png)

1. Under **Instansinformation**skriver du *myVM* för **namnet virtuell dator,** väljer östra *USA* för **din region**och väljer *Ubuntu 18.04 LTS* för **din avbildning**. Låt de övriga standardvärdena vara som de är.

    ![Avsnittet Instansinformation](./media/quick-create-portal/instance-details.png)

1. Under **Administratörskonto**väljer du **SSH-offentlig nyckel,** skriver ditt användarnamn och klistrar sedan in din offentliga nyckel. Ta bort eventuella inledande eller avslutande blanksteg i din offentliga nyckel.

    ![Administratörskonto](./media/quick-create-portal/administrator-account.png)

1. Under **Inkommande portregler** > **Offentliga inkommande portar**väljer du Tillåt valda **portar** och väljer sedan **SSH (22)** och **HTTP (80)** i listrutan. 

    ![Öppna portar för RDP och HTTP](./media/quick-create-portal/inbound-port-rules.png)

1. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

1. På sidan **Skapa en virtuell dator** kan du se information om den virtuella datorn som du håller på att skapa. När du är klar väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.

    
## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Skapa en SSH-anslutning med den virtuella datorn.

1. Välj **Anslut**-knappen på översiktssidan för din virtuella dator. 

    ![Portal 9](./media/quick-create-portal/portal-quick-start-9.png)

2. På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta via IP-adress via port 22. I **Logga in med lokalt virtuellt datorkonto** visas ett anslutningskommando. Markera knappen för att kopiera kommandot. Följande exempel visar hur SSH-anslutningskommandot ser ut:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Med samma bash skal som du använde för att skapa din SSH `>_` nyckelpar `https://shell.azure.com/bash`(du kan öppna Cloud Shell genom att välja igen eller gå till), klistra in SSH-anslutning kommandot i skalet för att skapa en SSH-session.

## <a name="install-web-server"></a>Installera webbservern

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Från din SSH-session uppdaterar du dina paketkällor och installera det senaste NGINX-paketet.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen genom att skriva `exit`.


## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

Använd valfri webbläsare för att visa välkomstsidan för NGINX. Skriv den offentliga IP-adressen för den virtuella datorn som webbadress. Den offentliga IP-adressen kan hittas på översiktssidan för den virtuella datorn eller som en del av SSH-anslutningssträngen du använde tidigare.

![NGINX-standardwebbplats](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan **Ta bort** och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, skapade en nätverkssäkerhetsgrupp och en regel och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
