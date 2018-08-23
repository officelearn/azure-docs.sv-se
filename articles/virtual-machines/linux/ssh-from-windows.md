---
title: Använd SSH-nycklar med Windows för virtuella Linux-datorer | Microsoft Docs
description: Lär dig hur du skapar och använder SSH-nycklar på en Windows-dator för att ansluta till en Linux-dator på Azure.
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
ms.date: 08/20/2018
ms.author: danlep
ms.openlocfilehash: fc245d38af90e0c395389e24b14d061fcfe0c10c
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057718"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Hur du använder SSH-nycklar med Windows på Azure

Den här artikeln beskrivs hur du skapar och använder secure shell (SSH)-nycklar på en Windows-dator för att skapa och ansluta till en Linux-dator (VM) i Azure. Om du vill använda SSH-nycklar från en klient för Linux eller macOS, se den [snabb](mac-create-ssh-keys.md) eller [detaljerad](create-ssh-keys-detailed.md) vägledning.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-paket och SSH-klienter
Du ansluter till och hantera virtuella Linux-datorer i Azure med hjälp av en *SSH-klient*. Datorer som kör Linux eller macOS vanligtvis har en uppsättning SSH kommandon för att skapa och hantera en SSH-nycklar och att skapa SSH-anslutningar. 

Windows-datorer har inte alltid likvärdig SSH-kommandon som är installerad. De senaste versionerna av Windows 10 ger [OpenSSH-klientkommandon](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) att skapa och hantera SSH-nycklar och göra SSH-anslutningar från en kommandotolk. De senaste Windows 10-versioner kan även innehålla den [Windows-undersystem for Linux](https://docs.microsoft.com/windows/wsl/about) att köra och få åtkomst till verktyg, till exempel en SSH-klient internt i ett Bash-gränssnitt. 

Om du vill använda en annan SSH-verktyg för Windows ingår vanliga Windows SSH-klienter kan du installera lokalt i följande paket:

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git för Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Ett annat alternativ är att använda de SSH-verktyg som är tillgängligt i Bash i den [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Komma åt Cloud Shell i webbläsaren på [ https://shell.azure.com ](https://shell.azure.com) eller i den [Azure-portalen](https://portal.azure.com). 
* Åtkomst till Cloud Shell som en terminal inifrån Visual Studio Code genom att installera den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Det här avsnittet visas två alternativ för att skapa en SSH-nyckelpar i Windows.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Skapa SSH-nycklar med ssh-keygen

Om du kör en kommandotolk i Windows som har stöd för SSH-klientverktyg (eller du använder Azure Cloud Shell), skapa en SSH-nyckelpar med hjälp av den `ssh-keygen` kommando. Skriv följande kommando och svara på frågorna. Om en SSH-nyckelpar finns i den aktuella platsen, kommer dessa filer skrivs över. 

```bash
ssh-keygen -t rsa -b 2048
```

Mer bakgrundsinformation och information finns i den [snabb](mac-create-ssh-keys.md) eller [detaljerad](create-ssh-keys-detailed.md) steg för att skapa nycklar med `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Skapa SSH-nycklar med PuTTYgen

Om du föredrar att använda ett GUI-baserade verktyg för att skapa SSH-nycklar kan du använda PuTTYgen nyckelgenerator, ingår i den [PuTTY hämtningspaketet](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Skapa ett SSH-RSA-nyckelpar med PuTTYgen:

1. Starta PuTTYgen.

2. Klicka på **generera**. Som standard genererar ett 2048-bitars SSH-2 RSA-nyckel i PuTTYgen.

4. För muspekaren över det tomma utrymmet för att generera vissa slumpmässighet för nyckeln.

5. När den offentliga nyckeln genereras, du kan också ange och bekräfta en lösenfras. Du uppmanas att lösenfrasen när du autentiserar till den virtuella datorn med SSH-nyckel. Utan en lösenfras om någon får tag på din privata nyckel, kan de logga in på en virtuell dator eller tjänst som använder nyckeln. Vi rekommenderar att du skapar en lösenfras. Men om du glömmer bort lösenfrasen finns inget sätt att återställa den.

6. Den offentliga nyckeln visas överst i fönstret. Du kan kopiera och klistra in den offentliga nyckeln för en rad format i Azure portal eller en Azure Resource Manager-mall när du skapar en Linux VM. Du kan också klicka på **spara offentlig nyckel** att spara en kopia på datorn:

    ![Spara PuTTY offentlig nyckelfil](./media/ssh-from-windows/save-public-key.png)

7. Du kan också för att spara den privata nyckeln i PuTTy format på privat nyckel (.ppk-fil), klickar du på **Spara privat nyckel**. Du behöver den .ppk-fil av som du vill använda PuTTY för att göra en SSH-anslutning till den virtuella datorn.

    ![Spara filen med PuTTY privata nyckel](./media/ssh-from-windows/save-ppk-file.png)

    Om du vill spara den privata nyckeln i OpenSSH-format i format på privat nyckel används av många SSH-klienter, klickar du på **konverteringar** > **exportera OpenSSH-nyckel**.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ange offentlig SSH-nyckel när du distribuerar en virtuell dator

Ange din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure-portalen eller andra metoder för att skapa en Linux VM som använder SSH-nycklar för autentisering.

I följande exempel visas hur du kan kopiera och klistra in den offentliga nyckeln i Azure-portalen när du skapar en Linux-VM. Den offentliga nyckeln är vanligtvis lagras sedan i `~/.ssh/authorized_keys` på din nya virtuella datorn.

   ![Använda offentlig nyckel när du skapar en virtuell dator i Azure portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ansluta till den virtuella datorn

Ett sätt att göra en SSH-anslutning till Linux VM från Windows är att använda en SSH-klient. Det här är den bästa metoden om du har en SSH-klient installerad på din Windows-system eller om du använder SSH-verktyg i Bash i Azure Cloud Shell. Om du föredrar ett GUI-baserade verktyg kan du ansluta med PuTTY.  

### <a name="use-an-ssh-client"></a>Använda en SSH-klient
Med den offentliga nyckeln som distribuerats på den virtuella Azure-datorn och den privata nyckeln på din lokala dator, SSH till den virtuella datorn med IP-adressen eller DNS-namnet på den virtuella datorn. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörens användarnamn och fullständigt kvalificerade domännamnet (eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du har konfigurerat en lösenfras när du skapade ditt nyckelpar ska du ange lösenfrasen när frågan kommer under inloggningen.

### <a name="connect-with-putty"></a>Ansluta med PuTTY

Om du har installerat den [PuTTY hämtningspaketet](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) och tidigare genererade en PuTTY privata nyckel (.ppk-fil), kan du ansluta till Linux VM med PuTTY.

1. Starta PuTTy.

2. Fyll i värdnamnet eller IP-adressen för den virtuella datorn från Azure portal:

    ![Öppna ny PuTTY anslutning](./media/ssh-from-windows/putty-new-connection.png)

3. Innan du väljer **öppna**, klickar du på **anslutning** > **SSH** > **Auth** fliken. Bläddra till och välj din PuTTY privata nyckel (.ppk-fil):

    ![Välj din PuTTY privata nyckel för autentisering](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klicka på **öppna** att ansluta till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* Detaljerade anvisningar, alternativ och avancerade exempel på hur du arbetar med SSH-nycklar finns i [detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

* Du kan också använda PowerShell för att generera SSH-nycklar och gör SSH-anslutningar för virtuella Linux-datorer i Azure Cloud Shell. Se den [PowerShell Snabbstart](../../cloud-shell/quickstart-powershell.md#ssh).

* Om du har problem med SSH kan ansluta till din virtuella Linux-datorer finns i [Felsök SSH-anslutningar till en virtuell Linux-dator](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
