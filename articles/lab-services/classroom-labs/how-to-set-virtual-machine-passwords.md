---
title: Ange lösenord för virtuella datorer i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ställer och återställa lösenord för virtuella datorer (VM) i klassrum labs av Azure Lab Services.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144115"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Ange eller återställa lösenord för virtuella datorer i labb för klassrum
Den här artikeln innehåller flera olika sätt att inställningen och resettings lösenord för åtkomst till virtuella datorer i klassrum labs. 

## <a name="lab-owners-teachers"></a>Labbägare (lärare)
Labbägare (lärare) kan set/återställning av lösenordet för virtuella datorer vid tidpunkten för att skapa labbet (lab-guide) eller när du har skapat labbet (på instrumentpanelen). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Ange ett lösenord när den skapas i labb
Labbägare (lärare) kan ange ett lösenord för virtuella datorer i labbet för den **ange autentiseringsuppgifter** i guiden Skapa labb.

![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)

Genom att aktivera/inaktivera den **Använd samma lösenord för alla virtuella datorer** alternativet på den här sidan kan en lärare kan välja att använda samma lösenord för alla virtuella datorer i labbet eller tillåta studenter att ange lösenord för sina virtuella datorer. Den här inställningen aktiveras som standard för alla Windows- och Linux-avbildningar utom Ubuntu. När den här inställningen inaktiveras uppmanas studenter att ange ett lösenord när de försöker ansluta till den virtuella datorn för första gången. 

Labbägare kan återställa lösenordet (vid behov) på den **konfigurera mallen** i guiden Skapa labb. 

![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Labbägare kan också återställa lösenordet efter labbet har skapats på instrumentpanelen. 

### <a name="reset-password-on-the-dashboard"></a>Återställ lösenord på instrumentpanelen

1. Välj Spill-menyn (lodrät tre punkter) på panelen labb och **Återställ lösenord**. 

    ![Återställ lösenord-menyn på startsidan](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. På den **att ange ett lösenord** dialogrutan, ange ett lösenord och välj **att ange ett lösenord**.
    
    ![Dialogrutan för ange lösenord](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Labbanvändare (elever)
Vid tidpunkten för skapat labbet, labbägare kan aktivera eller inaktivera den **Använd samma lösenord för alla virtuella datorer**. Om det här alternativet är aktiverat, kan inte studenter återställa lösenord. Alla virtuella datorer i labs har samma lösenord som anges av Lärarens. 

Om det här alternativet är inaktiverat, måste användare ange ett lösenord när du försöker ansluta till den virtuella datorn för första gången. När användare (studenter) väljer den **Connect** knappen på panelen lab på den **Mina virtuella datorer** sida, ser användaren följande dialogrutan för att ange lösenordet för den virtuella datorn: 

![Återställa lösenord för studenter](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Student också kan ange lösenordet genom att klicka på menyn spill (**tre lodräta punkterna**) på labb-panelen och välja **Återställ lösenord**. 

## <a name="next-steps"></a>Nästa steg
Läs om andra alternativ för användning av student du (som labbägare) kan konfigurera, finns i följande artikel: [Konfigurera student användning](how-to-configure-student-usage.md).
