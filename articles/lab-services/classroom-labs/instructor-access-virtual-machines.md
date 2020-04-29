---
title: Instruktör åtkomst till virtuella datorer i Azure Lab Services
description: 'Den här artikeln visar hur instruktörer kan komma åt sina elev-VM: ar från vyn instruktör. En undervisnings assistent kan till exempel vara en instruktör för en klass, men en student för andra klasser.'
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641945"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Få åtkomst till virtuella datorer som student från vyn instruktör
Den här artikeln visar hur instruktörer har åtkomst till sina virtuella datorer för klasser som de deltar i studenter. 

Här är ett scenario där den här funktionen kommer att hjälpa dig. En undervisnings assistent är en instruktör för en klass men en student i andra klasser. Dessutom vill undervisnings instruktören Visa och få åtkomst till de virtuella student datorerna från vyn instruktör som visar de labb som de äger. 

## <a name="access-vms-from-instructor-view"></a>Få åtkomst till virtuella datorer från vyn instruktör

1. Logga in på [Azure Lab Services webbplats](https://labs.azure.com). Du ser de labb du äger. Dessa labb kan vara labb som du själv har skapat eller de labb som administratören har tilldelat dig som ägare. Mer information finns i [så här lägger du till ytterligare ägare i ett befintligt labb](how-to-add-user-lab-owner.md)
2. Om du vill komma åt virtuella datorer för klasser som du deltar som student väljer du dator ikonen i det övre högra hörnet. Bekräfta att du ser virtuella datorer som du har åtkomst till som student. I följande exempel är användaren en undervisnings assistent för python-labbet, men en student av Java-labbet. Användaren kan alltså se den virtuella datorn från Java-labbet i list rutan. Användaren kan starta den virtuella datorn och ansluta till den. 
    
    ![Åtkomst till student virtuella datorer](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Ansluta till en virtuell dator](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ansluta till en virtuell dator med RDP på en Mac](connect-virtual-machine-mac-rdp.md)
- [Använd fjärr skrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)
