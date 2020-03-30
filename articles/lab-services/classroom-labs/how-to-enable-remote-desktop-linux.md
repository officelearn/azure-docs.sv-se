---
title: Aktivera fjärrskrivbord för Linux i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du aktiverar fjärrskrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270867"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Aktivera fjärrskrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services
I den här artikeln visas hur du utför följande uppgifter:

- Aktivera fjärrskrivbord för Virtuell Linux
- Hur lärare kan ansluta till mallen VM via Fjärrskrivbordsanslutning (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Aktivera fjärrskrivbord för Virtuell Linux
När labbet skapas kan lärare aktivera **fjärrskrivbordsanslutning** för **Linux-avbildningar.** Alternativet **Aktivera anslutning till fjärrskrivbord** visas när en Linux-avbildning är markerad för mallen. När det här alternativet är aktiverat kan lärare ansluta till virtuella datorer och virtuella datorer för elever via RDP (Remote Desktop). 

![Aktivera fjärrskrivbordsanslutning för en Linux-avbildning](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

I meddelanderutan Aktivera anslutning **till fjärrskrivbord** väljer du **Fortsätt med fjärrskrivbord**. 

![Aktivera fjärrskrivbordsanslutning för en Linux-avbildning](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Om du aktiverar **fjärrskrivbordsanslutning** öppnas endast **RDP-porten** på Linux-datorer. Om RDP redan är installerat och konfigurerat på avbildningen för den virtuella datorn (till exempel Ubuntu Data Science Virtual Machine-avbildningen) kan du/eleverna ansluta till virtuella datorer via RDP utan att följa några ytterligare steg.
> 
> Om vm-avbildningen inte har RDP installerat och konfigurerat måste du ansluta till Linux-datorn med SSH för första gången och installera RDP- och GUI-paket så att du/eleverna kan ansluta till Linux-datorn med RDP senare. Mer information finns i [Installera och konfigurera Fjärrskrivbord för att ansluta till en Virtuell Linux-dator i Azure](../../virtual-machines/linux/use-remote-desktop.md). Sedan publicerar du avbildningen så att eleverna kan RDP i till studenten Linux virtuella datorer. 

## <a name="supported-operating-systems"></a>Operativsystem som stöds
För närvarande stöds fjärrskrivbordsanslutningen för följande operativsystem:

- openSUSE Leap 42,3
- CentOS-baserade 7,5
- Debian 9 "Stretch"
- Ubuntu-server 16,04 LTS

## <a name="connect-to-the-template-vm"></a>Ansluta till den virtuella malldatorn 
Lärare måste ansluta till mallen VM med SSH först och installera RDP- och GUI-paket på den. Sedan kan lärarna använda RDP för att ansluta till mallen VM: 

1. Om du ser **Anpassa mall** i verktygsfältet markerar du den. Välj sedan **Fortsätt** i dialogrutan **Anpassa mall.** Den här åtgärden startar mallen VM.  

    ![Anpassa mall](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. När mallen VM har startats kan du välja **Anslut mall** och sedan **ansluta via SSH** i verktygsfältet. 

    ![Anslut till mall via RDP när labbet har skapats](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Du ser följande dialogrutan **Anslut till den virtuella datorn.** Markera knappen **Kopiera** bredvid textrutan om du vill kopiera den till Urklipp. Spara SSH-anslutningssträngen. Använd den här anslutningssträngen från en SSH-terminal (som [Putty)](https://www.putty.org/)för att ansluta till den virtuella datorn.
 
    ![SSH-anslutningssträng](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installera RDP- och GUI-paket så att du/eleverna kan ansluta till Linux-datorn med RDP senare. Mer information finns i [Installera och konfigurera Fjärrskrivbord för att ansluta till en Virtuell Linux-dator i Azure](../../virtual-machines/linux/use-remote-desktop.md). Sedan publicerar du avbildningen så att eleverna kan RDP i till studenten Linux virtuella datorer.
5. När dessa paket har installerats kan du använda **mallen Anslut till** i verktygsfältet och sedan välja **Anslut via RDP** för att ansluta till mallen VM via RDP. Spara RDP-filen och använd den för att ansluta till mallen VM via RDP. 

## <a name="next-steps"></a>Nästa steg
När en lärare har aktiverat anslutningsfunktionen för fjärrskrivbord kan deltagarna ansluta till sina virtuella datorer via RDP/SSH. Mer information finns i [Använda fjärrskrivbord för virtuella Linux-datorer i ett klassrumslabb](how-to-use-remote-desktop-linux-student.md). 