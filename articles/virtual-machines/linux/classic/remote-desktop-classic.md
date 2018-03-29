---
title: Fjärrskrivbord till en virtuell Linux-dator | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar Fjärrskrivbord för att ansluta till en Microsoft Azure Linux-VM för den klassiska distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 0e1bfe468e1572ca98be956d39d82df562dce0e6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Använd Fjärrskrivbord för att ansluta till en virtuell Microsoft Azure Linux-dator
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Den uppdaterade Resource Manager-versionen av den här artikeln finns [här](../use-remote-desktop.md).

## <a name="overview"></a>Översikt
RDP (Remote Desktop Protocol) är ett protokoll för Windows. Hur kan vi använda RDP för att fjärransluta till en Linux VM (virtuell dator)

Den här vägledningen ger svaret! Det hjälper dig att installera och config xrdp på din Microsoft Azure Linux VM, som gör att du kan ansluta till den med fjärrskrivbord från en Windows-dator. Vi använder Linux VM körs Ubuntu eller OpenSUSE som exemplet i den här vägledningen.

Verktyget xrdp är en öppen källkod RDP-server som kan du ansluta din Linux-server med fjärrskrivbord från en Windows-dator. RDP har bättre prestanda än VNC (virtuella nätverk datorer). VNC återger använder JPEG-kvalitet grafik och kan vara långsam, RDP är snabb och crystal Rensa.

> [!NOTE]
> Du måste redan ha en Microsoft Azure-dator som kör Linux. Om du vill skapa och konfigurera en Linux-VM finns i [virtuella Azure Linux-datorn kursen](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Skapa en slutpunkt för fjärrskrivbord
Vi använder standardslutpunkten 3389 för fjärrskrivbord i det här dokumentet. Ställ in 3389 slutpunkt som `Remote Desktop` till din Linux VM som nedan:

![Bild](./media/remote-desktop/endpoint-for-linux-server.png)

Om du inte vet hur du konfigurerar en slutpunkt för den virtuella datorn, se [vägledningen](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Installera gör väldigt lätt Desktop
Ansluta till din Linux VM via `putty`, och installera `Gnome Desktop`.

Ubuntu, Använd:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

För OpenSUSE, använder du:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Installera xrdp
Ubuntu, Använd:

```bash
sudo apt-get install xrdp
```

För OpenSUSE, använder du:

> [!NOTE]
> Uppdatera OpenSUSE versionen med den version som du använder i kommandot. I exemplet nedan används för `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Starta xrdp och Ställ in xdrp tjänsten vid uppstart
För OpenSUSE, använder du:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

För Ubuntu, kommer xrdp igång och aktiveras vid uppstart automatiskt efter installationen.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Med hjälp av xfce om du använder en Ubuntu version senare än Ubuntu 12.04LTS
Eftersom den aktuella versionen av xrdp inte stöder gör väldigt lätt skrivbordet för Ubuntu versioner senare än Ubuntu 12.04LTS, kommer vi att använda `xfce` skrivbordet i stället.

Så här installerar du `xfce`, använder du följande kommando:

```bash
sudo apt-get install xubuntu-desktop
```

Aktivera sedan `xfce` med det här kommandot:

```bash
echo xfce4-session >~/.xsession
```

Redigera konfigurationsfilen `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Lägg till rad `xfce4-session` före raden `/etc/X11/Xsession`.

Använd detta för att starta om tjänsten xrdp:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Ansluta din Linux-VM från en Windows-dator
Starta fjärrskrivbordsklienten på en Windows-dator och ange Linux VM DNS-namn. Eller gå till instrumentpanelen på den virtuella datorn i Azure-portalen och klicka på `Connect` att ansluta din Linux VM. I så fall visas inloggningsfönstret:

![Bild](./media/remote-desktop/no2.png)

Logga in med användarnamn och lösenord för Linux-VM.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du använder xrdp [ http://www.xrdp.org/ ](http://www.xrdp.org/).
