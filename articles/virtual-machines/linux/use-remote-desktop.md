---
title: "Använda fjärrskrivbord till en virtuell Linux-dator i Azure | Microsoft Docs"
description: "Lär dig hur du installerar och konfigurerar fjärrskrivbord (xrdp) för att ansluta till en Linux VM i Azure med grafiska verktyg"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: cdd8c5e932815c5741b1091a743d235de882c5b1
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Installera och konfigurera Fjärrskrivbord för att ansluta till en Linux VM i Azure
Linux virtuella datorer (VM) i Azure som oftast hanteras från kommandoraden med hjälp av en secure shell (SSH)-anslutning. När nya Linux eller för snabb felsökning scenarier kan användningen av fjärrskrivbord vara enklare. Den här artikeln beskriver hur du installerar och konfigurerar en Skrivbordsmiljö ([xfce](https://www.xfce.org)) och fjärrskrivbord ([xrdp](http://www.xrdp.org)) för dina Linux VM med hjälp av Resource Manager-distributionsmodellen.


## <a name="prerequisites"></a>Krav
Den här artikeln kräver ett befintligt Linux VM i Azure. Om du behöver skapa en virtuell dator med någon av följande metoder:

- Den [Azure CLI 2.0](quick-create-cli.md)
- Den [Azure-portalen](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Installera en Skrivbordsmiljö på Linux-VM
De flesta virtuella Linux-datorer i Azure har inte en Skrivbordsmiljö installerad som standard. Virtuella Linux-datorer hanteras ofta med hjälp av SSH-anslutningar i stället för en Skrivbordsmiljö. Det finns olika skrivbordsmiljöer i Linux som du kan välja. Beroende på ditt val av Skrivbordsmiljö kan den använda en 2 GB diskutrymme och ta 5 till 10 minuter att installera och konfigurera de nödvändiga paketen.

I följande exempel installeras förenklade [xfce4](https://www.xfce.org/) Skrivbordsmiljö på en Ubuntu VM. Kommandon för andra distributioner variera (Använd `yum` installera på Red Hat Enterprise Linux och konfigurera lämplig `selinux` regler eller Använd `zypper` att installera på SUSE, till exempel).

Första SSH till den virtuella datorn. I följande exempel ansluter till den virtuella datorn med namnet *myvm.westus.cloudapp.azure.com* med användarnamnet för *azureuser*:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du använder Windows, och du behöver mer information om hur du använder SSH, se [hur du använder SSH-nycklar med Windows](ssh-from-windows.md).

Därefter installera xfce med `apt` på följande sätt:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Installera och konfigurera en stationär dator
Nu när du har en Skrivbordsmiljö installerad kan du konfigurera Fjärrskrivbordstjänster för att lyssna efter inkommande anslutningar. [xrdp](http://xrdp.org) är en öppen källkod Remote Desktop Protocol (RDP)-server som är tillgänglig på de flesta Linux-distributioner och fungerar väl med xfce. Installera xrdp på din Ubuntu VM på följande sätt:

```bash
sudo apt-get install xrdp
```

Meddela xrdp vilka Skrivbordsmiljö ska användas när du startar sessionen. Konfigurera xrdp för att använda xfce som skrivbordsmiljön på följande sätt:

```bash
echo xfce4-session >~/.xsession
```

Starta om tjänsten xrdp för att ändringarna ska börja gälla på följande sätt:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ange ett lösenord för lokala användare
Hoppa över det här steget om du har skapat ett lösenord för användarkontot när du skapade den virtuella datorn. Om du bara använder SSH-nyckelautentisering och har inte ett lokalt kontolösenord ange, ange ett lösenord innan du använder xrdp för att logga in på den virtuella datorn. xrdp kan inte acceptera SSH-nycklar för autentisering. I följande exempel anger ett lösenord för användarkontot *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> SSHD konfigurationen för att tillåta Lösenordsinloggning om den för närvarande inte uppdateras inte att ange ett lösenord. Från ett säkerhetsperspektiv kanske du vill ansluta till den virtuella datorn med en SSH-tunnel med nyckel-baserad autentisering och ansluter sedan till xrdp. I så fall, kan du hoppa över följande steg om hur du skapar en grupp nätverkssäkerhetsregeln för att tillåta trafik för remote desktop.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Skapa en säkerhetsgrupp för nätverk-regel för Remote Desktop-trafik
För att tillåta trafik för fjärrskrivbord till din Linux VM en nätverkssäkerhet grupp regeln måste skapas som tillåter TCP på port 3389 till den virtuella datorn. Mer information om regler för nätverkssäkerhetsgrupper finns [vad är en Nätverkssäkerhetsgrupp?](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Du kan också [använda Azure portal för att skapa en grupp för nätverkssäkerhetsregeln](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I följande exempel skapas en grupp nätverkssäkerhetsregeln med [az vm öppna port](/cli/azure/vm#open-port) på port *3389*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Anslut din Linux VM med en fjärrskrivbordsklient
Öppna din lokala fjärrskrivbordsklienten och ansluta till IP-adressen eller DNS-namn för Linux-VM. Ange användarnamnet och lösenordet för användarkontot på den virtuella datorn på följande sätt:

![Ansluta till xrdp Remote Desktop-klienten](./media/use-remote-desktop/remote-desktop-client.png)

Efter autentisering, xfce Skrivbordsmiljö läsa in och ut ungefär så här:

![xfce Skrivbordsmiljö via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Felsöka
Om du inte kan ansluta till din Linux VM med hjälp av en fjärrskrivbordsklient `netstat` på ditt Linux-VM för att verifiera att den virtuella datorn lyssnar för RDP-anslutningar på följande sätt:

```bash
sudo netstat -plnt | grep rdp
```

I följande exempel visas den virtuella datorn som lyssnar på TCP-port 3389 som förväntat:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Om den *xrdp sesman* tjänsten inte lyssnar, på en Ubuntu VM startar du om tjänsten enligt följande:

```bash
sudo service xrdp restart
```

Granska loggar in */var/log* på din Ubuntu VM för uppgifter om varför tjänsten svarar inte. Du kan också övervaka syslog under en anslutning till fjärrskrivbord försöker visa eventuella fel:

```bash
tail -f /var/log/syslog
```

Andra Linux-distributioner, till exempel Red Hat Enterprise Linux och SUSE kan ha olika sätt att starta om tjänsterna och alternativa loggfilens platser att granska.

Om du inte får något svar i din fjärrskrivbordsklienten och inte ser några händelser i systemloggen, anger detta att remote desktop-trafik inte går att nå den virtuella datorn. Granska dina regler för nätverkssäkerhetsgrupper för att säkerställa att du har en regel för att tillåta TCP på port 3389. Mer information finns i [felsökning av problem med nätverksanslutningen](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar och använder SSH-nycklar med Linux virtuella datorer finns [skapa SSH-nycklar för Linux virtuella datorer i Azure](mac-create-ssh-keys.md).

Information om hur du använder SSH från Windows finns [hur du använder SSH-nycklar med Windows](ssh-from-windows.md).

