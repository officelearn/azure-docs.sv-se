---
title: Återställa lösenord för virtuella datorer i klassrumslabb i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du återställer lösenord för virtuella datorer i klassrumslaboratorier i Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583657"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Ange eller återställa lösenord för virtuella datorer i klassrumslabb (deltagare)
Den här artikeln visar hur deltagarna kan ange/återställa lösenord för sina virtuella datorer. 

## <a name="enable-resetting-of-passwords"></a>Aktivera återställning av lösenord
När labbägaren skapas kan du aktivera eller inaktivera **använda samma lösenord för alla virtuella datorer**. Om det här alternativet var aktiverat kan deltagarna inte återställa lösenordet. Alla virtuella datorer i labben har samma lösenord som anges av läraren. 

Om det här alternativet är inaktiverat måste användarna ange ett lösenord när de försöker ansluta till den virtuella datorn för första gången. Deltagarna kan också återställa lösenordet senare när som helst enligt det sista avsnittet i den här artikeln. 

## <a name="reset-password-for-the-first-time"></a>Återställa lösenord för första gången
Om alternativet **Använd samma lösenord för alla virtuella datorer** inaktiverades när användare (deltagare) väljer knappen **Anslut** på labbpanelen på sidan Mina **virtuella datorer,** ser användaren följande dialogruta för att ange lösenordet för den virtuella datorn: 

![Återställa lösenord för eleven](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Återställa lösenordet senare
Studenten kan också ange lösenordet genom att klicka på spillmenyn **(lodräta tre punkter)** på labbpanelen och välja **Återställ lösenord**. 

![Återställa lösenordet senare](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Nästa steg
Mer information om andra alternativ för elevanvändning som en labbägare kan konfigurera finns i följande artikel: [Konfigurera elevanvändning](how-to-configure-student-usage.md).
