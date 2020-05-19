---
title: Använd fjärr skrivbord för Linux i Azure Lab Services | Microsoft Docs
description: Lär dig hur du använder fjärr skrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services.
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
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588113"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Använd fjärr skrivbord för virtuella Linux-datorer i ett klass rums labb i Azure Lab Services
Den här artikeln visar hur studenter kan ansluta till en virtuell Linux-dator (VM) i ett labb med hjälp av RDP/SSH. 

En instruktör måste aktivera funktionen anslutning till fjärr skrivbord innan eleverna kan ansluta till klass rummets virtuella dator. Anvisningar om hur en instruktör kan aktivera funktionen anslutning till fjärr skrivbord finns i [Aktivera fjärr skrivbord för virtuella Linux-datorer](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> När du aktiverar **anslutning till fjärr skrivbord** öppnas **RDP** -porten på Linux-datorer. En instruktör kan ansluta till Linux-datorn med SSH för första gången och installera RDP-och GUI-paket så att du kan ansluta till Linux-datorn med RDP senare. 

## <a name="connect-to-the-student-vm"></a>Anslut till den virtuella student datorn
Studenter kan RDP i sina virtuella Linux-datorer när labb ägaren (lärare) **publicerar** mallen VM med RDP-och GUI-paket som är installerade på datorn. Gör så här: 

1. När en student loggar in på labb portalen direkt ( `https://labs.azure.com` ) eller genom att använda en registrerings länk ( `https://labs.azure.com/register/<registrationCode>` ), visas en panel för varje labb som eleven har åtkomst till. 
2. På panelen växlar du knappen för att starta den virtuella datorn om den är i stoppat läge. 
3. Välj **Anslut**. Du ser två alternativ för att ansluta till den virtuella datorn: **SSH** och **fjärr skrivbord**.

    ![Elev VM – anslutnings alternativ](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Anslut med SSH eller RDP
Om du väljer **SSH** -alternativet visas följande dialog ruta **för att ansluta till din virtuella dator** :  

![SSH-anslutningssträng](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Välj **kopierings** knappen bredvid text rutan för att kopiera den till Urklipp. Spara SSH-anslutningssträngen. Använd den här anslutnings strängen från en SSH-Terminal (t. ex. [SparaTillFil](https://www.putty.org/)) för att ansluta till den virtuella datorn.

Om du väljer alternativet **RDP** laddas en RDP-fil ned till datorn. Spara den och öppna den för att ansluta till datorn. 

## <a name="next-steps"></a>Nästa steg
Information om hur du aktiverar funktionen fjärr skrivbords anslutning för virtuella Linux-datorer i ett klass rums labb finns i [Aktivera fjärr skrivbord för virtuella Linux-datorer](how-to-enable-remote-desktop-linux.md). 

