---
title: Använd SSH-nycklar med Windows för virtuella Linux-datorer
description: Lär dig hur du skapar och använder SSH-nycklar på en Windows-dator för att ansluta till en virtuell Linux-dator på Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e01fb23bbf1720f7d8df9c269373c1b8dc3ec75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034813"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Så här använder du SSH-nycklar med Windows på Azure

This article describes ways to generate and use *secure shell* (SSH) keys on a Windows computer to create and connect to a Linux virtual machine (VM) in Azure. Om du vill använda SSH-nycklar från en Linux- eller macOS-klient läser du [den snabba](mac-create-ssh-keys.md) eller [detaljerade](create-ssh-keys-detailed.md) vägledningen.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-paket och SSH-klienter
Du ansluter till och hanterar virtuella Linux-datorer i Azure med en *SSH-klient*. Datorer som kör Linux eller macOS har vanligtvis en uppsättning SSH-kommandon för att generera och hantera SSH-nycklar och göra SSH-anslutningar. 

Windows-datorer har inte alltid jämförbara SSH-kommandon installerade. De senaste versionerna av Windows 10 ger [OpenSSH-klientkommandon](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) för att skapa och hantera SSH-nycklar och göra SSH-anslutningar från en kommandotolk. De senaste Windows 10-versionerna innehåller även [Windows-undersystemet för Linux för](https://docs.microsoft.com/windows/wsl/about) att köra och komma åt verktyg som en SSH-klient internt i ett Bash-skal. 

Andra vanliga Windows SSH-klienter som du kan installera lokalt ingår i följande paket:

* [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git för Windows](https://git-for-windows.github.io/)
* [MobaXterm (på andra)](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Du kan också använda SSH-verktygen som är tillgängliga i Bash i [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Få åtkomst till Cloud [https://shell.azure.com](https://shell.azure.com) Shell i din webbläsare på eller i [Azure-portalen](https://portal.azure.com). 
* Få åtkomst till Cloud Shell som en terminal inifrån Visual Studio-kod genom att installera [Azure Account-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
I följande avsnitt beskrivs två alternativ för att skapa ett SSH-nyckelpar i Windows. Du kan använda ett`ssh-keygen`skalkommando ( ) eller ett GUI-verktyg (PuTTYgen). Observera också, när du använder Powershell för att skapa en nyckel, ladda upp den offentliga nyckeln som ssh.com (SECSH) format. När du använder CLI konverterar du nyckeln till OpenSSH-format innan du laddar upp. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Skapa SSH-nycklar med ssh-keygen

Om du kör ett kommandoskal i Windows som stöder SSH-klientverktyg (eller om `ssh-keygen` du använder Azure Cloud Shell) skapar du ett SSH-nyckelpar med kommandot. Skriv följande kommando och svara på uppmaningarna. Om det finns ett SSH-nyckelpar på den valda platsen skrivs filerna över. 

```bash
ssh-keygen -t rsa -b 2048
```

Mer bakgrund och information finns i de [snabba](mac-create-ssh-keys.md) eller `ssh-keygen` [detaljerade](create-ssh-keys-detailed.md) stegen för att skapa SSH-nycklar med .

### <a name="create-ssh-keys-with-puttygen"></a>Skapa SSH-nycklar med PuTTYgen

Om du föredrar att använda ett GUI-baserat verktyg för att skapa SSH-nycklar kan du använda PuTTYgen-nyckelgeneratorn, som ingår i [PuTTY-nedladdningspaketet](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Så här skapar du ett SSH RSA-nyckelpar med PuTTYgen:

1. Starta PuTTYgen.

2. Klicka på **Generera**. Som standard genererar PuTTYgen en 2048-bitars SSH-2 RSA-nyckel.

4. Flytta musen runt i det tomma området för att ge slumpmässighet för nyckeln.

5. När den offentliga nyckeln har genererats anger du och bekräftar en lösenfras. Du kommer att uppmanas att göra lösenfrasen när du autentiserar till den virtuella datorn med din privata SSH-nyckel. Utan en lösenfras, om någon får din privata nyckel, kan de logga in på en virtuell dator eller tjänst som använder nyckeln. Vi rekommenderar att du skapar en lösenfras. Men om du glömmer bort lösenfrasen går det inte att återställa den.

6. Den offentliga nyckeln visas högst upp i fönstret. Du kan kopiera hela den här offentliga nyckeln och sedan klistra in den i Azure-portalen eller en Azure Resource Manager-mall när du skapar en Virtuell Linux-dator. Du kan också välja **Spara offentlig nyckel** för att spara en kopia på datorn:

    ![Spara den offentliga nyckelfilen för PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Om du vill spara den privata nyckeln i PuTTy-formatet för privata nyckel (.ppk-fil) väljer du **Spara privat nyckel**. Du behöver PPK-filen senare för att kunna använda PuTTY för att göra en SSH-anslutning till den virtuella datorn.

    ![Spara den privata nyckelfilen för PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Om du vill spara den privata nyckeln i OpenSSH-format väljer du **Konverteringar** > **Exportera OpenSSH-tangenten**i formatet OpenSSH.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Ange en allmän SSH-nyckel när du distribuerar en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering anger du den offentliga SSH-nyckeln när du skapar den virtuella datorn med Hjälp av Azure-portalen eller andra metoder.

Följande exempel visar hur du kopierar och klistrar in den här offentliga nyckeln i Azure-portalen när du skapar en Virtuell Linux-dator. Den offentliga nyckeln lagras vanligtvis i katalogen ~/.ssh/authorized_key på den nya virtuella datorn.

   ![Använda offentlig nyckel när du skapar en virtuell dator i Azure-portalen](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Ett sätt att göra en SSH-anslutning till din Virtuella Linux-dator från Windows är att använda en SSH-klient. Det här är den metod som föredras om du har en SSH-klient installerad på ditt Windows-system eller om du använder SSH-verktygen i Bash i Azure Cloud Shell. Om du föredrar ett GUI-baserat verktyg kan du ansluta till PuTTY.  

### <a name="use-an-ssh-client"></a>Använda en SSH-klient
Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln på ditt lokala system, SSH till din virtuella dator med IP-adressen eller DNS-namnet på din virtuella dator. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörens användarnamn och det fullständigt kvalificerade domännamnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du har konfigurerat en lösenfras när du skapade nyckelparet anger du lösenfrasen när du uppmanas att göra det under inloggningsprocessen.

Om den virtuella datorn använder just-in-time-åtkomstprincipen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med hjälp av just-in-time-principen](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Anslut med PuTTY

Om du har installerat [hämtningspaketet för PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) och tidigare genererat en PuTTY-fil (.ppk) kan du ansluta till en Virtuell Linux-dator med PuTTY.

1. Starta PuTTy.

2. Fyll i värdnamnet eller IP-adressen för din virtuella dator från Azure-portalen:

    ![Öppna ny PuTTY-anslutning](./media/ssh-from-windows/putty-new-connection.png)

3. Välj kategorin **Anslutning** > **SSH** > **Auth.** Bläddra till och välj din privata PuTTY-nyckel (.ppk-fil):

    ![Välj din privata PuTTY-nyckel för autentisering](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klicka på **Öppna** för att ansluta till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* Detaljerade steg, alternativ och avancerade exempel på hur du arbetar med SSH-nycklar finns i [Detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

* Du kan också använda PowerShell i Azure Cloud Shell för att generera SSH-nycklar och göra SSH-anslutningar till virtuella Linux-datorer. Se [snabbstarten för PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Om du har problem med att använda SSH för att ansluta till dina virtuella Linux-datorer läser [du Felsöka SSH-anslutningar till en Virtuell Azure Linux.](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
