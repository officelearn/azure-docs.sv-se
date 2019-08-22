---
title: Ange lösen ord för virtuella datorer i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ställer in och återställer lösen ord för virtuella datorer i klass rum labb Azure Lab Services.
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
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645015"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>Ange eller Återställ lösen ord för virtuella datorer i klass rum labb (lärare)
En labb ägare (lärare) kan ange/återställa lösen ordet för virtuella datorer vid tidpunkten för att skapa labbet (guiden Skapa labb) eller när du har skapat labbet (på instrument panelen). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Ange lösen ord vid tidpunkten för att skapa labbet
En labb ägare (lärare) kan ange ett lösen ord för virtuella datorer i labbet på sidan **ange autentiseringsuppgifter** i guiden Skapa labb.

![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)

Genom att aktivera/inaktivera alternativet **Använd samma lösen ord för alla virtuella datorer** på den här sidan kan en lärare välja att använda samma lösen ord för alla virtuella datorer i labbet eller tillåta studenter att ange lösen ord för sina virtuella datorer. Den här inställningen är aktive rad som standard för alla Windows-och Linux-operativsystem avbildningar utom Ubuntu. När den här inställningen är inaktive rad uppmanas eleverna att ange ett lösen ord när de försöker ansluta till den virtuella datorn för första gången. 

Labb ägaren kan återställa lösen ordet (om det behövs) på sidan **Konfigurera mall** i guiden Skapa labb. 

![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Labb ägaren kan också återställa lösen ordet när labbet har skapats, på instrument panelen. 

## <a name="reset-password-on-the-dashboard"></a>Återställ lösen ord på instrument panelen

1. Välj menyn spill (lodräta tre punkter) på labb rutan och välj **Återställ lösen ord**. 

    ![Återställa lösen ords menyn på Start Sidan](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. I dialog rutan **Ange lösen ord** anger du ett lösen ord och väljer **Ange lösen ord**.
    
    ![Dialog rutan Ange lösen ord](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Nästa steg
Information om andra alternativ för elev användning som du (som labb ägare) kan konfigurera finns i följande artikel: [Konfigurera elev användning](how-to-configure-student-usage.md).

Information om hur studenter kan återställa lösen ord för sina virtuella datorer finns i [Ange eller återställa lösen ord för virtuella datorer i klass rum labb (studenter)](how-to-set-virtual-machine-passwords-student.md).