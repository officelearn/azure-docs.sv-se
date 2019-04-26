---
title: Använda Fjärrskrivbord för att en virtuell Linux-dator i Azure | Microsoft Docs
description: Lär dig att installera och konfigurera fjärrskrivbord (xrdp) för att ansluta till en Linux-VM i Azure med grafiska verktyg
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: dec0b8bcd5a0e0d20ead5b149405e3716589ba53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478249"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Installera och konfigurera Fjärrskrivbord för att ansluta till en Linux-VM i Azure
Linux-datorer (VM) i Azure hanteras vanligtvis från kommandoraden med hjälp av en secure shell (SSH)-anslutning. När nya till Linux, eller för snabb felsökning scenarier kan det vara enklare användning av fjärrskrivbord. Den här artikeln beskriver hur du installerar och konfigurerar en Skrivbordsmiljö ([xfce](https://www.xfce.org)) och fjärrskrivbord ([xrdp](http://www.xrdp.org)) för din Linux-VM med hjälp av Resource Manager-distributionsmodellen.


## <a name="prerequisites"></a>Nödvändiga komponenter
Den här artikeln kräver en befintlig virtuell Ubuntu 16.04 LTS-dator i Azure. Om du vill skapa en virtuell dator kan använda en av följande metoder:

- Den [Azure CLI](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Installera en Skrivbordsmiljö på din Linux-VM
De flesta virtuella Linux-datorer i Azure har inte en Skrivbordsmiljö som installeras som standard. Virtuella Linux-datorer hanteras ofta med hjälp av SSH-anslutningar i stället för en Skrivbordsmiljö. Det finns olika skrivbordsmiljöerna i Linux som du själv väljer. Beroende på ditt val av Skrivbordsmiljö, kan den använda en till 2 GB diskutrymme och tar 5 till 10 minuter att installera och konfigurera de nödvändiga paketen.

I följande exempel installeras lightweight [xfce4](https://www.xfce.org/) Skrivbordsmiljö på en dator med Ubuntu 16.04 LTS. Kommandon för andra distributioner kan skilja sig något (Använd `yum` installera på Red Hat Enterprise Linux och konfigurera lämplig `selinux` regler eller Använd `zypper` att installera på SUSE, till exempel).

Först SSH till den virtuella datorn. I följande exempel ansluter till den virtuella datorn med namnet *myvm.westus.cloudapp.azure.com* med användarnamnet för *azureuser*. Använd dina egna värden:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du använder Windows och behöver mer information om hur du använder SSH, se [hur du använder SSH-nycklar med Windows](ssh-from-windows.md).

Installera med hjälp av xfce `apt` på följande sätt:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Installera och konfigurera en stationär fjärrserver
Nu när du har en Skrivbordsmiljö installerad kan du konfigurera Fjärrskrivbordstjänster för att lyssna efter inkommande anslutningar. [xrdp](http://xrdp.org) är en öppen källkod Remote Desktop Protocol (RDP)-server som är tillgängliga i de flesta Linux-distributioner och fungerar bra med xfce. Installera xrdp på Ubuntu-VM på följande sätt:

```bash
sudo apt-get install xrdp
sudo systemctl enable xrdp
```

Berätta xrdp vilka Skrivbordsmiljö ska användas när du startar din session. Konfigurera xrdp för att använda xfce som skrivbordsmiljön på följande sätt:

```bash
echo xfce4-session >~/.xsession
```

Starta om tjänsten xrdp för att ändringarna ska börja gälla enligt följande:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ange lösenordet för ett lokalt användarkonto
Hoppa över det här steget om du har skapat ett lösenord för ditt konto när du skapade den virtuella datorn. Om du bara använder SSH-nyckelautentisering och inte har ett lokalt kontolösenord som angetts, ange ett lösenord innan du använder xrdp för att logga in på den virtuella datorn. xrdp kan inte acceptera SSH-nycklar för autentisering. I följande exempel anger ett lösenord för användarkontot *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> SSHD-konfigurationen för att tillåta inloggningar med lösenord om det för närvarande inte uppdateras inte att ange ett lösenord. Från ett säkerhetsperspektiv kanske du vill ansluta till den virtuella datorn med en SSH-tunnel med nyckel-baserad autentisering och sedan ansluta till xrdp. I så, fall hoppar du över följande steg om hur du skapar en nätverkssäkerhetsgruppregel för att tillåta trafik för fjärrskrivbordet.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Skapa en regel för Nätverkssäkerhetsgrupp för Remote Desktop-trafik
Om du vill tillåta Remote Desktop att nå din Linux-VM, en nätverkssäkerhetsgrupp kan gruppen regel måste skapas som TCP på port 3389 nå din virtuella dator. Mer information om regler för nätverkssäkerhetsgrupper finns i [vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Du kan också [använda Azure-portalen för att skapa en nätverkssäkerhetsgruppregel](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I följande exempel skapas en nätverkssäkerhetsgruppregel med [az vm open-port](/cli/azure/vm#az-vm-open-port) på port *3389*. Inte SSH-sessionen till den virtuella datorn, öppna följande nätverkssäkerhetsgruppregel från Azure-CLI:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ansluta din Linux-VM med en fjärrskrivbordsklienten
Öppna din lokala fjärrskrivbordsklienten och ansluta till IP-adressen eller DNS-namnet på din Linux-VM. Ange användarnamnet och lösenordet för användarkontot på den virtuella datorn på följande sätt:

![Ansluta till xrdp med hjälp av fjärrskrivbord-klienten](./media/use-remote-desktop/remote-desktop-client.png)

När de har autentiserat, xfce Skrivbordsmiljö läsa in och se ut ungefär så här:

![xfce Skrivbordsmiljö via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Om din lokala RDP-klient använder autentisering på nätverksnivå (NLA), kan du behöva inaktivera anslutningsinställningen. XRDP stöder för närvarande inte NLA. Du kan också titta på alternativa RDP-lösningar som har stöd för NLA, till exempel [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Felsöka
Om du inte kan ansluta till din Linux-VM med hjälp av fjärrskrivbord-klienten kan använda `netstat` på din Linux-VM för att verifiera att den virtuella datorn lyssnar för RDP-anslutningar på följande sätt:

```bash
sudo netstat -plnt | grep rdp
```

I följande exempel visas den virtuella datorn lyssnar på TCP-port 3389 som förväntat:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Om den *xrdp sesman* tjänsten inte lyssnar, på en Ubuntu-VM startar du om tjänsten på följande sätt:

```bash
sudo service xrdp restart
```

Granska loggarna i */var/log* på din Ubuntu VM bryts till varför tjänsten svarar inte. Du kan också övervaka syslog under en anslutning till fjärrskrivbord försöker visa eventuella fel:

```bash
tail -f /var/log/syslog
```

Andra Linux-distributioner som Red Hat Enterprise Linux och SUSE kan ha olika sätt att starta om tjänster och alternativa loggfilens platser att granska.

Om du inte får några svar i din fjärrskrivbordsklienten och inte ser alla händelser i systemloggen, anger detta att trafik för fjärrskrivbordet inte kan nå den virtuella datorn. Granska dina regler för nätverkssäkerhetsgrupper för att säkerställa att du har en regel för att tillåta TCP på port 3389. Mer information finns i [felsöka problem med programanslutningar](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar och använder SSH-nycklar med Linux-datorer finns i [skapa SSH-nycklar för Linux-datorer i Azure](mac-create-ssh-keys.md).

Information om hur du använder SSH från Windows finns i [hur du använder SSH-nycklar med Windows](ssh-from-windows.md).

