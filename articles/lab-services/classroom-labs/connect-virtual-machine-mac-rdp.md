---
title: Så här ansluter du till en virtuell azure lab services-dator från Mac | Microsoft-dokument
description: Den här artikeln
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503093"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Ansluta till en virtuell dator med RDP på en Mac
Det här avsnittet visar hur en elev kan ansluta till en virtuell klassrumslabb från en Mac med hjälp av RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installera Microsoft Remote Desktop på en Mac
1. Öppna App Store på din Mac och sök efter **Microsoft Remote Desktop**.

    ![Microsoft Fjärrskrivbord](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installera den senaste versionen av Microsoft Remote Desktop. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Komma åt den virtuella datorn från din Mac med RDP
1. Öppna **rdp-filen** som hämtas på datorn med **Microsoft Remote Desktop** installerat. Den ska börja ansluta till den virtuella datorn. 

    ![Ansluta till virtuell dator](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Välj **Fortsätt** om du får följande varning. 

    ![Varning för certifikat](../media/how-to-use-classroom-lab/certificate-error.png)
1. Du bör se den virtuella datorn. 

    > [!NOTE]
    > Följande exempel är för en CentOS Linux VM. 

    ![Virtuell dator](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Nästa steg
Mer information om hur du ansluter till Virtuella Linux-datorer med RDP finns i [Använda fjärrskrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)


