---
title: Fjärrskrivbord till en virtuell Linux-dator | Microsoft Docs
description: Lär dig att installera och konfigurera Fjärrskrivbord för att ansluta till en Microsoft virtuell Linux-dator för den klassiska distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 5e68774c3edb7d82fef388c593a6b96c52857be6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927748"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Använd Fjärrskrivbord för att ansluta till en virtuell Microsoft Azure Linux-dator
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Den uppdaterade Resource Manager-versionen av den här artikeln finns [här](../use-remote-desktop.md).

## <a name="overview"></a>Översikt
RDP (Remote Desktop Protocol) är ett eget protokoll som används för Windows. Hur kan vi använda RDP för att fjärransluta till en Linux VM (virtuell dator)?

Den här vägledningen ger dig svar! Hjälper dig att installera och config xrdp på din Microsoft Azure Linux-dator, vilket gör att du ansluter till den med fjärrskrivbord från en Windows-dator. Vi använder Linux VM som kör Ubuntu- eller OpenSUSE som i exemplet i den här vägledningen.

Verktyget xrdp är en RDP-servern med öppen källkod som gör det möjligt att ansluta din Linux-server med fjärrskrivbord från en Windows-dator. RDP har bättre prestanda än VNC (virtuella nätverk databehandling). VNC återges med JPEG kvalitet grafik och kan vara långsam, RDP är snabb och crystal tydlig.

> [!NOTE]
> Du måste redan ha en Microsoft Azure-dator som kör Linux. Om du vill skapa en Linux-VM finns i den [virtuell Linux-dator självstudien](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Skapa en slutpunkt för anslutning till fjärrskrivbord
Vi använder standardslutpunkten 3389 för fjärrskrivbord i det här dokumentet. Ställ in 3389 slutpunkt som `Remote Desktop` för din Linux-VM som nedan:

![image](./media/remote-desktop/endpoint-for-linux-server.png)

Om du inte vet hur du konfigurerar en slutpunkt för din virtuella dator, se [den här vägledningen](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Installera gör väldigt lätt Desktop
Ansluta till din Linux-VM via `putty`, och installera `Gnome Desktop`.

För Ubuntu, använder du:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

För OpenSUSE, använder du:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Installera xrdp
För Ubuntu, använder du:

```bash
sudo apt-get install xrdp
```

För OpenSUSE, använder du:

> [!NOTE]
> Uppdatera OpenSUSE-versionen med den version som du använder i följande kommando. I exemplet nedan används för `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Starta xrdp och ange xdrp tjänsten vid uppstart
För OpenSUSE, använder du:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

För Ubuntu, kommer xrdp igång och aktiveras vid uppstart automatiskt efter installationen.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Med hjälp av xfce om du använder en Ubuntu-version senare än Ubuntu 12.04LTS
Eftersom den aktuella versionen av xrdp inte stöder gör väldigt lätt Desktop för Ubuntu versioner senare än Ubuntu 12.04LTS, kommer vi att använda `xfce` Desktop i stället.

Installera `xfce`, Använd det här kommandot:

```bash
sudo apt-get install xubuntu-desktop
```

Aktivera sedan `xfce` med hjälp av det här kommandot:

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
Starta Fjärrskrivbord-klienten på en Windows-dator och ange Linux VM DNS-namn. Eller gå till instrumentpanelen för den virtuella datorn i Azure-portalen och klicka på `Connect` att ansluta din Linux-VM. I så fall kan se du inloggningsfönstret:

![image](./media/remote-desktop/no2.png)

Logga in med användarnamn och lösenord för din Linux-VM.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du använder xrdp [ http://www.xrdp.org/ ](http://www.xrdp.org/).
