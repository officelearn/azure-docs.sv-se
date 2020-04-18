---
title: Använda Fjärrskrivbord till en virtuell Linux-dator i Azure
description: Lär dig hur du installerar och konfigurerar Fjärrskrivbord (xrdp) för att ansluta till en Virtuell Linux-dator i Azure med hjälp av grafiska verktyg
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 2e97442d4104f52c1a76ba8cd1d81c99508bb242
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605188"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Installera och konfigurera Fjärrskrivbord för att ansluta till en Virtuell Linux-dator i Azure
Virtuella Linux-datorer (VMs) i Azure hanteras vanligtvis från kommandoraden med hjälp av en säker skalanslutning (SSH). När du är ny på Linux, eller för snabba felsökningsscenarier, kan det vara enklare att använda fjärrskrivbord. I den här artikeln beskrivs hur du installerar och konfigurerar en skrivbordsmiljö ([xfce)](https://www.xfce.org)och fjärrskrivbord[(xrdp)](http://xrdp.org)för din Virtuella Linux-dator med hjälp av resurshanterarens distributionsmodell.


## <a name="prerequisites"></a>Krav
Den här artikeln kräver en befintlig Ubuntu 18.04 LTS VM i Azure. Om du behöver skapa en virtuell dator använder du någon av följande metoder:

- [Azure CLI](quick-create-cli.md)
- [Azure-portalen](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Installera en skrivbordsmiljö på din virtuella Linux-dator
De flesta virtuella Linux-datorer i Azure har ingen skrivbordsmiljö installerad som standard. Virtuella Linux-datorer hanteras ofta med SSH-anslutningar i stället för en skrivbordsmiljö. Det finns olika skrivbordsmiljöer i Linux som du kan välja. Beroende på ditt val av skrivbordsmiljö kan det förbruka en till 2 GB diskutrymme och ta 5 till 10 minuter att installera och konfigurera alla nödvändiga paket.

I följande exempel installeras den lätta [xfce4-skrivbordsmiljön](https://www.xfce.org/) på en Ubuntu 18.04 LTS-dator. Kommandona för andra distributioner `yum` varierar något (används för `selinux` att installera `zypper` på Red Hat Enterprise Linux och konfigurera lämpliga regler, eller använda för att installera på SUSE, till exempel).

Först SSH till din virtuella dator. Följande exempel ansluter till den virtuella datorn med *namnet myvm.westus.cloudapp.azure.com* med användarnamnet *för azureuser*. Använd dina egna värderingar:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du använder Windows och behöver mer information om hur du använder SSH läser du [Så här använder du SSH-nycklar med Windows](ssh-from-windows.md).

Installera sedan xfce `apt` med följande:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Installera och konfigurera en fjärrskrivbordsserver
Nu när du har installerat en skrivbordsmiljö konfigurerar du en fjärrskrivbordstjänst för att lyssna efter inkommande anslutningar. [xrdp](http://xrdp.org) är en RDP-server (Remote Desktop Protocol) som är tillgänglig på de flesta Linux-distributioner och fungerar bra med xfce. Installera xrdp på din Ubuntu VM enligt följande:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Tala om för xrdp vilken skrivbordsmiljö du ska använda när du startar sessionen. Konfigurera xrdp så att xfce används som skrivbordsmiljö på följande sätt:

```bash
echo xfce4-session >~/.xsession
```

Starta om xrdp-tjänsten för att ändringarna ska börja gälla på följande sätt:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Ange ett lösenord för ett lokalt användarkonto
Om du har skapat ett lösenord för ditt användarkonto när du skapade den virtuella datorn hoppar du över det här steget. Om du bara använder SSH-nyckelautentisering och inte har angett ett lokalt kontolösenord anger du ett lösenord innan du använder xrdp för att logga in på den virtuella datorn. xrdp kan inte acceptera SSH-nycklar för autentisering. I följande exempel anges ett lösenord för *användarkontot azureuser:*

```bash
sudo passwd azureuser
```

> [!NOTE]
> Att ange ett lösenord uppdaterar inte SSHD-konfigurationen så att lösenordsinloggningar tillåts om det för närvarande inte gör det. Ur ett säkerhetsperspektiv kanske du vill ansluta till din virtuella dator med en SSH-tunnel med hjälp av nyckelbaserad autentisering och sedan ansluta till xrdp. Om så är fallet, hoppa över följande steg för att skapa en nätverkssäkerhetsgrupp regel för att tillåta fjärrskrivbordstrafik.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Skapa en regel för nätverkssäkerhetsgrupp för fjärrskrivbordstrafik
För att fjärrskrivbordstrafiken ska kunna nå din virtuella Linux-dator måste en regel för nätverkssäkerhetsgrupper skapas som gör att TCP på port 3389 kan nå din virtuella dator. Mer information om regler för nätverkssäkerhetsgrupper finns i [Vad är en nätverkssäkerhetsgrupp?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Du kan också [använda Azure-portalen för att skapa en regel för nätverkssäkerhetsgrupper](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I följande exempel skapas en regel för nätverksskyddsgrupp med [az vm open-port](/cli/azure/vm#az-vm-open-port) på port *3389*. Från Azure CLI, inte SSH-sessionen till den virtuella datorn, öppnar du följande regel för nätverkssäkerhetsgrupper:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ansluta din virtuella Linux-dator med en fjärrskrivbordsklient
Öppna din lokala fjärrskrivbordsklient och anslut till IP-adressen eller DNS-namnet på din Virtuella Linux-dator. Ange användarnamn och lösenord för användarkontot på den virtuella datorn enligt följande:

![Ansluta till xrdp med klienten för fjärrskrivbord](./media/use-remote-desktop/remote-desktop-client.png)

När xfce-skrivbordsmiljön har autentiserats läses den in och ser ut ungefär som följande exempel:

![xfce skrivbordsmiljö via xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Om din lokala RDP-klient använder autentisering på nätverksnivå (NLA) kan du behöva inaktivera anslutningsinställningen. XRDP stöder för närvarande inte NLA. Du kan också titta på alternativa RDP-lösningar som stöder NLA, till exempel [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Felsöka
Om du inte kan ansluta till din Virtuella `netstat` Linux-dator med en fjärrskrivbordsklient använder du på din virtuella Linux-dator för att kontrollera att den virtuella datorn lyssnar efter RDP-anslutningar enligt följande:

```bash
sudo netstat -plnt | grep rdp
```

I följande exempel visas vm-lyssningen på TCP-port 3389 som förväntat:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Om *xrdp-sesman-tjänsten* inte lyssnar startar du om tjänsten på en Ubuntu-virtuell dator enligt följande:

```bash
sudo service xrdp restart
```

Granska loggar in */var/log* på din Ubuntu VM för indikationer på varför tjänsten kanske inte svarar. Du kan också övervaka syslogen under ett fjÃ¤rrskrivbordsanslutning som försöker visa eventuella fel:

```bash
tail -f /var/log/syslog
```

Andra Linux-distributioner som Red Hat Enterprise Linux och SUSE kan ha olika sätt att starta om tjänster och alternativa loggfilsplatser att granska.

Om du inte får något svar i fjärrskrivbordsklienten och inte ser några händelser i systemloggen, indikerar det här beteendet att fjärrskrivbordstrafik inte kan nå den virtuella datorn. Granska reglerna för nätverkssäkerhetsgruppen för att se till att du har en regel som tillåter TCP i port 3389. Mer information finns i [Felsöka problem med programanslutning](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar och använder SSH-nycklar med virtuella Linux-datorer finns [i Skapa SSH-nycklar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

Information om hur du använder SSH från Windows finns i [Så här använder du SSH-nycklar med Windows](ssh-from-windows.md).

