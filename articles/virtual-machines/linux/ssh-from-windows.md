---
title: Använda SSH-nycklar med Windows för virtuella Linux-datorer | Microsoft Docs
description: Lär dig hur du skapar och använder SSH-nycklar på en Windows-dator för att ansluta till en virtuell Linux-dator på Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Hur du använder SSH-nycklar med Windows på Azure

Den här artikeln introducerar sätt att skapa och använda secure shell (SSH) nycklar på en Windows-dator för att skapa och ansluta till en Linux-dator (VM) i Azure. Om du vill använda SSH-nycklar från en klient för Linux eller macOS finns i [snabb](mac-create-ssh-keys.md) eller [detaljerad](create-ssh-keys-detailed.md) vägledning.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-paket och SSH-klienter
Du ansluter till och hantera virtuella Linux-datorer i Azure med hjälp av en *SSH-klienten*. Datorer som kör Linux eller macOS vanligtvis har en uppsättning SSH kommandon för att skapa och hantera SSH-nycklar och för att skapa SSH-anslutningar. 

Windows-datorer har inte alltid jämförbara SSH-kommandon som är installerad. Windows 10-versioner som innehåller den [Windows undersystem för Linux](https://docs.microsoft.com/windows/wsl/about) kan du köra och komma åt verktyg, till exempel en SSH-klient internt i ett Bash-gränssnitt. 

Om du vill använda något annat än Bash för Windows ingår vanliga Windows SSH-klienter kan du installera lokalt i följande paket:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git för Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Ett annat alternativ är att använda de SSH verktyg i Bash i den [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Åtkomst till molnet Shell i webbläsaren på [ https://shell.azure.com ](https://shell.azure.com) eller i den [Azure-portalen](https://portal.azure.com). 
* Åtkomst till molnet Shell som en terminal från i Visual Studio Code genom att installera den [Azure-konto tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Det här avsnittet visas två alternativ för att skapa en SSH-nyckel i Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Skapa SSH-nycklar med ssh-keygen

Om du kan köra en kommandotolk, till exempel Bash för Windows eller Bash (eller Bash i Azure Cloud Shell), skapa en SSH-nyckelpar med den `ssh-keygen` kommando. Skriv följande kommando och besvara frågor. Om det finns en SSH-nyckel på den aktuella platsen, skrivs filerna över. 

```bash
ssh-keygen -t rsa -b 2048
```

Mer bakgrund och information finns i [snabb](mac-create-ssh-keys.md) eller [detaljerad](create-ssh-keys-detailed.md) steg för att skapa nycklar med `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Skapa SSH-nycklar med PuTTYgen

Om du vill använda ett GUI-baserat verktyg för att skapa SSH-nycklar, kan du använda PuTTYgen nyckelgenerator, ingår i den [PuTTY-paketet](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Skapa en SSH-RSA-nyckelpar med PuTTYgen:

1. Starta PuTTYgen.

2. Klicka på **generera**. Som standard genererar PuTTYgen en 2048-bitars SSH-2 RSA-nyckel.

4. Håll muspekaren över tomt område för att generera vissa slumpmässighet för nyckeln.

5. När den offentliga nyckeln genereras, du kan också ange och bekräfta en lösenfras. Du uppmanas att lösenfrasen när du autentiserar till den virtuella datorn med SSH-nyckel. Utan en lösenfras om någon erhåller din privata nyckel, kan de logga in på en virtuell dator eller tjänst som använder nyckeln. Vi rekommenderar att du skapar en lösenfras. Men om du glömmer bort lösenfrasen finns inget sätt att återställa den.

6. Den offentliga nyckeln visas överst i fönstret. Kopiera och klistra in den här offentliga nyckeln för en rad format i Azure-portalen eller en Azure Resource Manager-mall när du skapar en Linux VM. Du kan också klicka på **spara offentlig nyckel** att spara en kopia på datorn:

    ![Spara PuTTY fil för offentlig nyckel](./media/ssh-from-windows/save-public-key.png)

7. Du kan också spara den privata nyckeln i PuTTy format för privat nyckel (.ppk-fil), klicka på **Spara privat nyckel**. Du behöver den .ppk-fil du vill använda PuTTY för att göra en SSH-anslutning för den virtuella datorn.

    ![Spara PuTTY fil för privat nyckel](./media/ssh-from-windows/save-ppk-file.png)

    Om du vill spara den privata nyckeln i formatet OpenSSH formatet privata nyckeln används av många SSH-klienter klickar du på **konverteringar** > **exportera OpenSSH-nyckel**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ange offentlig SSH-nyckel när du distribuerar en virtuell dator

Ange din offentliga SSH-nyckel när du skapar den virtuella datorn i Azure-portalen eller med andra metoder för att skapa en Linux VM som använder SSH-nycklar för autentisering.

I följande exempel visas hur du kan kopiera och klistra in den här offentliga nyckeln i Azure-portalen när du skapar en Linux VM. Den offentliga nyckeln är vanligtvis lagras sedan i `~/.ssh/authorized_keys` på den nya virtuella datorn.

   ![Använda offentliga nyckel när du skapar en virtuell dator i Azure-portalen](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ansluta till den virtuella datorn

Ett sätt att göra en SSH-anslutning för Linux-VM från Windows är att använda en SSH-klient. Detta är den bästa metoden om du har en SSH-klienten installerad på Windows-system eller du använder SSH verktyg i Bash i Azure Cloud-gränssnittet. Om du föredrar ett GUI-baserat verktyg kan du ansluta med PuTTY.  

### <a name="use-an-ssh-client"></a>Använda en SSH-klient
Med den offentliga nyckeln som distribueras på Azure VM, och den privata nyckeln i det lokala systemet SSH till den virtuella datorn med IP-adressen eller DNS-namnet på den virtuella datorn. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörsanvändarnamn och fullständigt kvalificerat domännamn (eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du har konfigurerat en lösenfras när du skapade din nyckelpar, ange lösenfrasen när du uppmanas under inloggningen.

### <a name="connect-with-putty"></a>Anslut med PuTTY

Om du har installerat den [PuTTY-paketet](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) och tidigare genererade PuTTY privata nyckel (.ppk-fil), kan du ansluta till Linux VM med PuTTY.

1. Starta PuTTy.

2. Fyll i värdnamn eller IP-adressen för den virtuella datorn från Azure portal:

    ![Öppna ny PuTTY anslutning](./media/ssh-from-windows/putty-new-connection.png)

3. Innan du väljer **öppna**, klickar du på **anslutning** > **SSH** > **Auth** fliken. Bläddra till och markera din PuTTY privata nyckel (.ppk-fil):

    ![Välj din PuTTY privata nyckel för autentisering](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klicka på **öppna** att ansluta till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* Detaljerade anvisningar, alternativ och avancerade exempel på SSH-nycklar finns [detaljerade steg för att skapa SSH-nyckeln par](create-ssh-keys-detailed.md).

* Du kan också använda PowerShell i Azure Cloud-gränssnittet för att skapa SSH-nycklar och göra SSH-anslutningar i virtuella Linux-datorer. Finns det [PowerShell quickstart](../../cloud-shell/quickstart-powershell.md#ssh).

* Om du har problem med SSH att ansluta till din virtuella Linux-datorer, se [felsökning av SSH-anslutningar till en Azure Linux VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
