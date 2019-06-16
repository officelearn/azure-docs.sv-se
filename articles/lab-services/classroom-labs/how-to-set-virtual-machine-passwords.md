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
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123213"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Ange eller återställa lösenord för virtuella datorer i labb för klassrum
Den här artikeln innehåller flera olika sätt att inställningen och resettings lösenord för åtkomst till virtuella datorer i klassrum labs. 

## <a name="lab-owners-teachers"></a>Labbägare (lärare)
Labbägare (lärare) kan ange ett lösenord för virtuella datorer i labbet för den **ange autentiseringsuppgifter** i guiden Skapa labb.

![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)

Sedan labbägare kan återställa lösenordet (vid behov) på den **konfigurera mallen** i guiden Skapa labb. 

![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Labbägare kan också återställa lösenordet efter labbet har skapats på instrumentpanelen. 

![Återställ lösenord-menyn på startsidan](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

Ange sedan det nya lösenordet i den **att ange ett lösenord** och välj **att ange ett lösenord**. 

![Återställ lösenord-menyn på startsidan](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Labbanvändare (elever)
Vid tidpunkten för skapat labbet. På samma gång, kan labbägare aktivera eller inaktivera så att studenter för att ange sina egna lösenord för de virtuella datorerna. Om labbägare (lärare) gör det möjligt för det här alternativet, har lab-användare (student) ett alternativ för att ange lösenordet för den virtuella datorn när elevens loggar in på den virtuella datorn för första gången. På en **Windows VM**, tryck på **CTRL + ALT + END**, och välj **ändra lösenord**. 

## <a name="next-steps"></a>Nästa steg
Läs om andra alternativ för användning av student du (som labbägare) kan konfigurera, finns i följande artikel: [Konfigurera student användning](how-to-configure-student-usage.md).
