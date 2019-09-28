---
title: Aktivera fjärr skrivbord för Linux i Azure Lab Services | Microsoft Docs
description: Lär dig hur du aktiverar fjärr skrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: c67ca111bf87c9dbfa69c93149d29dbd32767fbd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350766"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Aktivera fjärr skrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services
Den här artikeln visar hur du utför följande uppgifter:

- Aktivera fjärr skrivbord för virtuell Linux-dator
- Hur lärare kan ansluta till mallen VM via Anslutning till fjärrskrivbord (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Aktivera fjärr skrivbord för virtuell Linux-dator
Under labb skapandet kan lärare aktivera **anslutning till fjärr skrivbord** för **Linux** -avbildningar. Alternativet **aktivera anslutning till fjärrskrivbord** visas när en Linux-avbildning väljs för mallen. När det här alternativet är aktiverat kan lärare ansluta till mallen VM och student virtuella datorer via RDP (fjärr skrivbord). 

![Aktivera anslutning till fjärr skrivbord för en Linux-avbildning](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

I rutan **aktivera anslutning till fjärrskrivbord** meddelande väljer du **Fortsätt med fjärr skrivbord**. 

![Aktivera anslutning till fjärr skrivbord för en Linux-avbildning](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> När du aktiverar **anslutning till fjärr skrivbord** öppnas **RDP** -porten på Linux-datorer. Om RDP redan har installerats och kon figurer ATS på den virtuella dator avbildningen (till exempel: Ubuntu Data Science Virtual Machine-avbildning) kan du/studenter ansluta till virtuella datorer via RDP utan att följa ytterligare steg.
> 
> Om den virtuella dator avbildningen inte har RDP installerat och konfigurerad, måste du ansluta till Linux-datorn med SSH för första gången och installera RDP-och GUI-paket så att du/studenter kan ansluta till Linux-datorn med RDP senare. Mer information finns i [Installera och konfigurera fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure](../../virtual-machines/linux/use-remote-desktop.md). Sedan publicerar du avbildningen så att eleverna kan RDP i de virtuella student Linux-datorerna. 

## <a name="supported-operating-systems"></a>Operativsystem som stöds
Anslutning till fjärr skrivbord stöds för följande operativ system:

- openSUSE skottår 42,3
- CentOS-baserad 7,5
- Debian 9 "sträckning"
- Ubuntu Server 16,04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Lärare som ansluter till mallen VM med RDP
Lärare måste ansluta till mallen VM med SSH först och installera RDP-och GUI-paket på den. Sedan kan lärarna använda följande steg för att ansluta till virtuella Linux-datorer med RDP: 

Du ser alternativet **fjärr skrivbord** för att ansluta till mallen VM vid tidpunkten för att skapa labbet. 

![Anslut till Mall via RDP vid tidpunkten för skapandet](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Du ser alternativet **fjärr skrivbord** på Labbets start sida när labbet har skapats och mallen VM har startats. Starta mallen VM om den inte redan har startats. 

![Anslut till Mall via RDP när labbet har skapats](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Mer information om hur du ansluter till den virtuella datorn med SSH eller RDP finns i [Anslut med SSH eller RDP] ((#connect-using-SSH-eller-RDP). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Lärare som ansluter till en elev-VM med RDP
En lärare/lärare kan ansluta till en elev-VM genom att växla till vyn **Virtual Machines** och välja ikonen **Anslut** . Innan detta måste lärare **publicera** mal Lav bildningen med RDP-och GUI-paket installerade på den. 

![Lärare som ansluter till den virtuella student datorn](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Mer information om hur du ansluter till den virtuella datorn med SSH eller RDP finns i [Anslut med SSH eller RDP] ((#connect-using-SSH-eller-RDP). 

## <a name="connect-using-ssh-or-rdp"></a>Anslut med SSH eller RDP
Om du väljer **SSH** -alternativet visas följande dialog ruta **för att ansluta till din virtuella dator** :  

![SSH-anslutningssträng](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Välj **kopierings** knappen bredvid text rutan för att kopiera den till Urklipp. Spara SSH-anslutningssträngen. Använd den här anslutnings strängen från en SSH-Terminal (t. ex. [SparaTillFil](https://www.putty.org/)) för att ansluta till den virtuella datorn.

Om du väljer alternativet **RDP** laddas en RDP-fil ned till datorn. Spara den och öppna den för att ansluta till datorn. 

## <a name="next-steps"></a>Nästa steg
När en instruktör har aktiverat funktionen anslutning till fjärr skrivbord kan eleverna ansluta till sina virtuella datorer via RDP/SSH. Mer information finns i [använda fjärr skrivbord för virtuella Linux-datorer i ett klass rums labb](how-to-use-remote-desktop-linux-student.md). 