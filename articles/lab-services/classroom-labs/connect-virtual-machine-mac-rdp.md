---
title: Så här ansluter du till en Azure Lab Services virtuell dator från Mac | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503093"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Ansluta till en virtuell dator med RDP på en Mac
Det här avsnittet visar hur en student kan ansluta till en virtuell dator i klass rummet från en Mac med hjälp av RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installera Microsoft Fjärrskrivbord på en Mac
1. Öppna App Store på din Mac och Sök efter **Microsoft fjärrskrivbord**.

    ![Microsoft Fjärrskrivbord](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installera den senaste versionen av Microsoft Fjärrskrivbord. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Få åtkomst till den virtuella datorn från din Mac med hjälp av RDP
1. Öppna den **RDP** -fil som har laddats ned på datorn med **Microsoft fjärrskrivbord** installerat. Den ska börja ansluta till den virtuella datorn. 

    ![Ansluta till virtuell dator](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Välj **Fortsätt** om du får följande varning. 

    ![Certifikat varning](../media/how-to-use-classroom-lab/certificate-error.png)
1. Du bör se den virtuella datorn. 

    > [!NOTE]
    > Följande exempel gäller för en virtuell CentOS Linux-dator. 

    ![Virtuell dator](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter till virtuella Linux-datorer med RDP finns i [använda fjärr skrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)


