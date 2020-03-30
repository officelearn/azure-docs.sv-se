---
title: Använda fjärrskrivbord för Linux i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du använder fjärrskrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services.
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585078"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Använda fjärrskrivbord för virtuella Linux-datorer i ett klassrumslabb med Azure Lab Services
Den här artikeln visar hur studenter kan ansluta till en virtuell Linux-dator (VM) i ett labb med hjälp av RDP/SSH. 

En lärare måste aktivera anslutningsfunktionen för fjärrskrivbord innan eleverna kan ansluta till klassrumslabbets virtuella dator. Instruktioner om hur en lärare kan aktivera anslutningsfunktionen för fjärrskrivbord finns i [Aktivera fjärrskrivbord för virtuella Linux-datorer](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Om du aktiverar **fjärrskrivbordsanslutning** öppnas endast **RDP-porten** på Linux-datorer. En lärare kan ansluta till Linux-datorn med SSH för första gången och installera RDP- och GUI-paket så att du kan ansluta till Linux-datorn med RDP senare. 

## <a name="connect-to-the-student-vm"></a>Anslut till den virtuella studenten
Studenter kan RDP i sina Linux VIRTUELLA datorer efter labbägaren (lärare / professor) **publicerar** mallen VM med RDP och GUI-paket installerade på datorn. Här är stegen: 

1. När en student loggar in på`https://labs.azure.com`Labs portalen direkt`https://labs.azure.com/register/<registrationCode>`( ) eller genom att använda en registreringslänk ( ), visas en panel för varje labb som studenten har tillgång till. 
2. På panelen växlar du knappen för att starta den virtuella datorn om den är i stoppat tillstånd. 
3. Välj **Anslut**. Du ser två alternativ för att ansluta till den virtuella datorn: **SSH** och **Fjärrskrivbord**.

    ![Student-VM - anslutningsalternativ](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Ansluta med SSH eller RDP
Om du väljer alternativet **SSH** visas följande dialogrutan **Anslut till den virtuella datorn:**  

![SSH-anslutningssträng](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Markera knappen **Kopiera** bredvid textrutan om du vill kopiera den till Urklipp. Spara SSH-anslutningssträngen. Använd den här anslutningssträngen från en SSH-terminal (som [Putty)](https://www.putty.org/)för att ansluta till den virtuella datorn.

Om du väljer alternativet **RDP** hämtas en RDP-fil till datorn. Spara den och öppna den för att ansluta till maskinen. 

## <a name="next-steps"></a>Nästa steg
Mer information om hur du aktiverar anslutningsfunktionen för fjärrskrivbord för virtuella Linux-datorer i ett klassrumslabb finns i [Aktivera fjärrskrivbord för virtuella Linux-datorer](how-to-enable-remote-desktop-linux.md). 

