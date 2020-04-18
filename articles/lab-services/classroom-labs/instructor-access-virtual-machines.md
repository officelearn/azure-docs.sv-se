---
title: Lärare som använder virtuella datorer i Azure Lab Services
description: Den här artikeln visar hur lärare kan komma åt sina virtuella elev-datorer från lärarvyn. En lärarassistent kan till exempel vara lärare för en klass men en elev för andra klasser.
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
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641945"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Få tillgång till virtuella datorer som student från lärarvyn
Den här artikeln visar hur lärare kan komma åt sina virtuella datorer för klasser som de deltar som deltagare. 

Här är ett scenario där den här funktionen hjälper. En lärarassistent är lärare för en klass men en elev i andra klasser. Och lärarläraren vill visa och komma åt elevernas virtuella datorer från lärarvyn som visar de labb de äger. 

## <a name="access-vms-from-instructor-view"></a>Få tillgång till virtuella datorer från lärarvyn

1. Logga in på [Webbplatsen för Azure Lab Services](https://labs.azure.com). Du ser labben som du äger. Dessa labb kan vara labb som du har skapat själv eller de labb som du har tilldelats som ägare. Mer information finns i [Så här lägger du till ytterligare ägare i ett befintligt labb](how-to-add-user-lab-owner.md)
2. Om du vill komma åt virtuella datorer för klasser som du deltar i som deltagare väljer du datorikonen i det övre högra hörnet. Bekräfta att du ser virtuella datorer som du kan komma åt som deltagare. I följande exempel är användaren en lärarassistent för Python-labbet, men en student i Java-labbet. Så ser användaren den virtuella datorn från Java-labbet i listrutan. Användaren kan starta den virtuella datorn och ansluta till den. 
    
    ![Få tillgång till virtuella datorer för elever](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Ansluta till en virtuell dator](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ansluta till en virtuell dator med RDP på en Mac](connect-virtual-machine-mac-rdp.md)
- [Använda fjärrskrivbord för virtuella Linux-datorer](how-to-use-remote-desktop-linux-student.md)
