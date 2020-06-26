---
title: Använd SSH-nycklar med Windows för virtuella Linux-datorer
description: Lär dig hur du skapar och använder SSH-nycklar på en Windows-dator för att ansluta till en virtuell Linux-dator på Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 81dfac2a1623253a110833a96fddd1b41bd11b26
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85390235"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Använda SSH-nycklar med Windows på Azure

Den här artikeln beskriver hur du skapar och använder SSH-nycklar ( *Secure Shell* ) på en Windows-dator för att skapa och ansluta till en virtuell Linux-dator (VM) i Azure. Information om hur du använder SSH-nycklar från en Linux-eller macOS-klient finns i [snabb](mac-create-ssh-keys.md) eller [detaljerad](create-ssh-keys-detailed.md) vägledning.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-paket och SSH-klienter
Du ansluter till och hanterar virtuella Linux-datorer i Azure med hjälp av en *SSH-klient*. Datorer som kör Linux eller macOS har vanligt vis en uppsättning SSH-kommandon för att skapa och hantera SSH-nycklar och för att göra SSH-anslutningar. 

Windows-datorer har inte alltid jämförbara SSH-kommandon installerade. De senaste versionerna av Windows 10 tillhandahåller [openssh-klient kommandon](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) för att skapa och hantera SSH-nycklar och göra SSH-anslutningar från en kommando tolk. De senaste versionerna av Windows 10 innehåller även [Windows-undersystemet för Linux](https://docs.microsoft.com/windows/wsl/about) för att köra och få till gång till verktyg som en SSH-klient internt i ett bash-gränssnitt. 

Andra vanliga Windows SSH-klienter som du kan installera lokalt ingår i följande paket:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git för Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Du kan också använda SSH-verktygen som är tillgängliga i bash i [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Åtkomst Cloud Shell i webbläsaren på [https://shell.azure.com](https://shell.azure.com) eller i [Azure Portal](https://portal.azure.com). 
* Kom åt Cloud Shell som en Terminal i Visual Studio Code genom att installera [tillägget för Azure-kontot](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
I följande avsnitt beskrivs två alternativ för att skapa ett SSH-nyckelpar i Windows. Du kan använda ett Shell-kommando ( `ssh-keygen` ) eller ett gui-verktyg (PuTTYgen). Observera också när du använder PowerShell för att skapa en nyckel, överför den offentliga nyckeln som SSH. com (SECSH)-format. När du använder CLI konverterar du nyckeln till OpenSSH-format innan du överför. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>Skapa SSH-nycklar med ssh-keygen

Om du kör ett kommando gränssnitt i Windows som stöder SSH-klientinställningar (eller använder Azure Cloud Shell) skapar du ett SSH-nyckelpar med `ssh-keygen` kommandot. Skriv följande kommando och svara på frågorna. Om det finns ett SSH-nyckelpar på den valda platsen, skrivs filerna över. 

```bash
ssh-keygen -t rsa -b 2048
```

Mer bakgrund och information finns i [snabb](mac-create-ssh-keys.md) eller [detaljerade](create-ssh-keys-detailed.md) steg för att skapa SSH-nycklar med `ssh-keygen` .

### <a name="create-ssh-keys-with-puttygen"></a>Skapa SSH-nycklar med PuTTYgen

Om du föredrar att använda ett GUI-baserat verktyg för att skapa SSH-nycklar, kan du använda PuTTYgen-nyckel generatorn som ingår i [paket hämtnings paketet SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Så här skapar du ett SSH RSA-nyckelpar med PuTTYgen:

1. Starta PuTTYgen.

2. Klicka på **Generera**. Som standard genererar PuTTYgen en 2048-bitars SSH-2 RSA-nyckel.

3. Flytta musen runt i det tomma utrymmet för att ge dig slumpmässighet för nyckeln.

4. När den offentliga nyckeln har genererats kan du välja att ange och bekräfta en lösen fras. Du kommer att uppmanas att ange lösen frasen när du autentiserar till den virtuella datorn med din privata SSH-nyckel. Om någon hämtar din privata nyckel utan lösen fras kan de logga in på en virtuell dator eller tjänst som använder den nyckeln. Vi rekommenderar att du skapar en lösen fras. Men om du glömmer bort lösenfrasen går det inte att återställa den.

5. Den offentliga nyckeln visas överst i fönstret. Du kan kopiera hela den offentliga nyckeln och klistra in den i Azure Portal eller i en Azure Resource Manager mall när du skapar en virtuell Linux-dator. Du kan också välja **Spara offentlig nyckel** för att spara en kopia på din dator. Observera att när du sparar till en fil, konverterar SparaTillFil den offentliga nyckeln till ett annat format, [RFC4716](https://tools.ietf.org/html/rfc4716). RFC4716-formatet kanske inte är kompatibelt med alla API: er. Om du vill använda i Azure Portal rekommenderar vi att du kopierar den offentliga nyckeln som visas i fönstret SparaTillFil.

    ![Spara filen med fil för fil-offentlig nyckel](./media/ssh-from-windows/save-public-key.png)

6. Om du vill spara den privata nyckeln i filen med formatet för en fil med formatet SparaTillFil (. PPK) väljer du **Spara privat nyckel**. Du kommer att behöva. PPK-filen senare för att kunna använda SparaTillFil för att skapa en SSH-anslutning till den virtuella datorn.

    ![Spara filen med den privata nyckeln för filen](./media/ssh-from-windows/save-ppk-file.png)

    Om du vill spara den privata nyckeln i openssh-formatet, det privata nyckel formatet som används av många SSH-klienter, väljer du **konverteringar**  >  **Exportera openssh nyckel**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Ange en offentlig SSH-nyckel när du distribuerar en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering, anger du din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure Portal eller andra metoder.

I följande exempel visas hur du kopierar och klistrar in den offentliga nyckeln i Azure Portal när du skapar en virtuell Linux-dator. Den offentliga nyckeln lagras vanligt vis i katalogen ~/.ssh/authorized_key på den nya virtuella datorn.

   ![Använd offentlig nyckel när du skapar en virtuell dator i Azure Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Ett sätt att göra en SSH-anslutning till din virtuella Linux-dator från Windows är att använda en SSH-klient. Detta är den bästa metoden om du har en SSH-klient installerad i Windows-systemet eller om du använder SSH-verktygen i bash i Azure Cloud Shell. Om du föredrar ett GUI-baserat verktyg kan du ansluta till SparaTillFil.  

### <a name="use-an-ssh-client"></a>Använda en SSH-klient
Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln i det lokala systemet, med hjälp av den virtuella datorns IP-adress eller DNS-namn. Ersätt *azureuser* och *myvm.westus.cloudapp.Azure.com* i följande kommando med administratörs användar namnet och det fullständigt kvalificerade domän namnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du har konfigurerat en lösen fras när du skapade nyckel paret anger du lösen frasen när du uppmanas att göra det under inloggnings processen.

Om den virtuella datorn använder just-in-Time-åtkomst-principen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Anslut med SparaTillFil

Om du har installerat det [hämtade hämtnings paketet](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) och tidigare genererade en fil med fil (. PPK) med fil (.) kan du ansluta till en virtuell Linux-dator med SparaTillFil.

1. Starta SparaTillFil.

2. Fyll i värd namnet eller IP-adressen för den virtuella datorn från Azure Portal:

    ![Öppna ny SparaTillFil-anslutning](./media/ssh-from-windows/putty-new-connection.png)

3. Välj kategorin för att **ansluta**  >  **SSH**-  >  **autentisering** . Bläddra till och välj din PPK-privata nyckel (.-fil):

    ![Välj din aktuella SparaTillFil-privata nyckel för autentisering](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klicka på **Öppna** för att ansluta till din virtuella dator.

## <a name="next-steps"></a>Nästa steg

* Detaljerade steg, alternativ och avancerade exempel på hur du arbetar med SSH-nycklar finns i [detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

* Du kan också använda PowerShell i Azure Cloud Shell för att generera SSH-nycklar och göra SSH-anslutningar till virtuella Linux-datorer. Se [snabb start för PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Om du har problem med att använda SSH för att ansluta till dina virtuella Linux-datorer kan du läsa [FELSÖK SSH-anslutningar till en virtuell Azure Linux-dator](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
