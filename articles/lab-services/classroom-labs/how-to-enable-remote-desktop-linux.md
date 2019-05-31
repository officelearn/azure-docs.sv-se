---
title: Aktivera Fjärrskrivbord för Linux i Azure Lab Services | Microsoft Docs
description: Lär dig mer om att aktivera Fjärrskrivbord för Linux-datorer i ett labb i Azure Lab Services.
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
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 389d467bd9672743d4a086e8a1c505fb0366dba7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237127"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Aktivera och använda Fjärrskrivbord för Linux-datorer i ett labb i Azure Lab Services
Den här artikeln visar hur du gör följande:

- Aktivera Fjärrskrivbord för Linux VM
- Hur lärare kan ansluta till mallen för virtuell dator via Remote Desktop Connection (RDP).
- Hur studenter ansluter till den virtuella datorn via RDP för studenten

## <a name="enable-remote-desktop-for-linux-vm"></a>Aktivera Fjärrskrivbord för Linux VM
När du skapar lab lärare kan aktivera **fjärrskrivbordsanslutning** för **Linux** bilder. Den **aktivera anslutning till fjärrskrivbord** alternativ visas när en Linux-avbildning har valts för mallen. När det här alternativet är aktiverat, kan lärare ansluta till mall för virtuell dator och student virtuella datorer via RDP (Remote Desktop). 

![Aktivera anslutning till fjärrskrivbord för en Linux-avbildning](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

På den **aktiverar anslutning till fjärrskrivbord** meddelanderutan väljer **Fortsätt med fjärrskrivbord**. 

![Aktivera anslutning till fjärrskrivbord för en Linux-avbildning](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Aktivera **fjärrskrivbordsanslutning** öppnas bara den **RDP** porten på Linux-datorer. Du som en lärare, ansluta till Linux-dator med hjälp av SSH för första gången och installera RDP och GUI-paket så att du kan ansluta till Linux-datorn med RDP senare. Sedan kan du **publicera** avbildningen så att studenter kan RDP i att student virtuella Linux-datorer. 

## <a name="supported-operating-systems"></a>Operativsystem som stöds
För närvarande stöds fjärrskrivbordsanslutningen för följande operativsystem:

- openSUSE Leap 42.3
- CentOS-baserade 7.5
- Debian 9 ”Stretch”
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Lärare som ansluter till VM-mallen med RDP
Lärare måste ansluta till VM-mallen med hjälp av SSH först och installera RDP och GUI-paket på den. Lärare kan sedan använda följande steg för att ansluta till den virtuella Linux-datorer med RDP: 

Du ser den **fjärrskrivbord** alternativet för att ansluta till VM-mallen vid tidpunkten för skapat labbet. 

![Ansluta till mall via RDP när den skapas](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

Du ser den **fjärrskrivbord** alternativet på den testmiljön startsida när du har skapat labbet och VM-mallen har startats. Starta VM-mallen om den inte redan startats. 

![Ansluta till mall via RDP när labbet har skapats](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Mer information om hur du ansluter till den virtuella datorn med SSH eller RDP finns i [Anslut med SSH eller RDP]((#connect-using-ssh-or-rdp). 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Lärare som ansluter till en student virtuell dator med RDP
En lärare kan ansluta till en student VM genom att växla till den **virtuella datorer** visa och välja den **ansluta** ikon. Åtgärden lärare måste **publicera** mall-bild med RDP och GUI-paket som är installerade på den. 

![Lärare som ansluter till student VM](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Mer information om hur du ansluter till den virtuella datorn med SSH eller RDP finns i [Anslut med SSH eller RDP]((#connect-using-ssh-or-rdp). 

## <a name="students-connecting-to-the-student-vm"></a>Studenter som ansluter till student VM
Student kan RDP i sina virtuella Linux-datorer efter labbägare (lärare) **publicerar** mallen virtuell dator med RDP och GUI-paket som har installerats på datorn. Här är stegen: 

1. När en student loggar in på portalen Labs direkt (`https://labs.azure.com`) eller genom att använda en registreringslänk (`https://labs.azure.com/register/<registrationCode>`), en panel för varje labb elevens har åtkomst till visas. 
2. På panelen, väljer **starta** om den virtuella datorn stoppas. 
3. Välj **Anslut**. Du ser två alternativ för att ansluta till den virtuella datorn: **SSH** och **fjärrskrivbord**.

    ![Student VM - anslutningsalternativ](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Ansluta med SSH eller RDP
Om du väljer den **SSH** alternativet kan du se följande **Anslut till den virtuella datorn** dialogrutan:  

![SSH-anslutningssträng](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Välj den **kopia** knappen bredvid textrutan för att kopiera den till Urklipp. Spara SSH-anslutningssträng. Använd den här anslutningssträngen från en SSH-terminalen (t.ex. [Putty](https://www.putty.org/)) att ansluta till den virtuella datorn.

Om du väljer den **RDP** hämtas en RDP-fil på din dator. Spara den och öppna den kan ansluta till datorn. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)

