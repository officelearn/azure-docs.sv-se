---
title: Så här ansluter du till en Azure Lab Services virtuell dator från Mac | Microsoft Docs
description: Lär dig hur du ansluter från en Mac till en virtuell dator i Azure Lab Services.
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
ms.openlocfilehash: 091acbab20723ec33d52085a717d23adf261254e
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897385"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Ansluta till en virtuell dator med hjälp av Remote Desktop Protocol på en Mac
Det här avsnittet visar hur en student kan ansluta till en virtuell dator i klass rummet från en Mac med hjälp av RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installera Microsoft Fjärrskrivbord på en Mac
1. Öppna App Store på din Mac och Sök efter **Microsoft fjärrskrivbord**.

    ![Microsoft Fjärrskrivbord](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installera den senaste versionen av Microsoft Fjärrskrivbord. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Få åtkomst till den virtuella datorn från din Mac med hjälp av RDP
1. Öppna den **RDP** -fil som har laddats ned på datorn med **Microsoft fjärrskrivbord** installerat. Den ska börja ansluta till den virtuella datorn. 

    ![Ansluta till virtuell dator](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Välj **Fortsätt** om du får följande varning. 

    ![Certifikat varning](./media/how-to-use-classroom-lab/certificate-error.png)
1. Du bör se den virtuella datorn. 

    > [!NOTE]
    > Följande exempel gäller för en virtuell CentOS Linux-dator. 

    ![Virtuell dator](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Nästa steg
Information om hur du ansluter till virtuella Linux-datorer med RDP finns i [använda fjärr skrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)


