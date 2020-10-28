---
title: Så här ansluter du till en Azure Lab Services virtuell dator från Chromebook | Microsoft Docs
description: Lär dig hur du ansluter från en Chromebook till en virtuell dator i Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: ae2bc6e87f0b694e949057280233c19e0b98132c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892238"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Ansluta till en virtuell dator med Remote Desktop Protocol på en Chromebook
Det här avsnittet visar hur en student kan ansluta till en virtuell dator i klass rummet från en Chromebook med hjälp av RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Installera Microsoft Fjärrskrivbord på en Chromebook
1. Öppna App Store på din Chromebook och Sök efter **Microsoft fjärrskrivbord** .

    ![Microsoft Fjärrskrivbord](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Installera den senaste versionen av Microsoft Fjärrskrivbord. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Få åtkomst till den virtuella datorn från din Chromebook med RDP
1. Öppna den **RDP** -fil som har laddats ned på datorn med **Microsoft fjärrskrivbord** installerat. Den ska börja ansluta till den virtuella datorn. 

    ![Ansluta till virtuell dator](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Ange ditt lösen ord när du uppmanas till det.
    ![Skärm bild som visar inloggnings skärmen där du anger ditt användar namn och lösen ord.](./media/how-to-use-classroom-lab/password-chromebook.png)


1. Välj **Fortsätt** om du får följande varning. 

    ![Certifikat varning](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Du bör se Skriv bordet för den virtuella dator som du ansluter till.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du ansluter till virtuella Linux-datorer finns i [ansluta till virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)


