---
title: Återställa lösen ord för virtuella klass rums labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du återställer lösen ord för virtuella datorer i klass rums labb Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d63d14da8859c12fa797a04fa9a3c3230219adab
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445346"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Ange eller Återställ lösen ord för virtuella datorer i klass rum labb (studenter)
Den här artikeln visar hur studenter kan ange/återställa lösen ord för sina virtuella datorer. 

## <a name="enable-resetting-of-passwords"></a>Aktivera återställning av lösen ord
Vid tidpunkten för att skapa labbet kan labb ägaren aktivera eller inaktivera **Använd samma lösen ord för alla virtuella datorer**. Om det här alternativet har Aktiver ATS kan studenter inte återställa lösen ord. Alla virtuella datorer i labben har samma lösen ord som anges av instruktören. 

Om det här alternativet är inaktiverat måste användarna ange ett lösen ord när de försöker ansluta till den virtuella datorn för första gången. Studenter kan också återställa lösen ordet senare när som helst, vilket visas i det sista avsnittet i den här artikeln. 

## <a name="reset-password-for-the-first-time"></a>Återställ lösen ord för första gången
Om alternativet **Använd samma lösen ord för alla virtuella datorer** har inaktiverats, när användare (studenter) väljer knappen **Anslut** på labb panelen på sidan **mina virtuella datorer** , ser användaren följande dialog ruta för att ange lösen ordet för den virtuella datorn: 

![Återställ lösen ord för studenten](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Återställ lösen ord senare
Student kan också ange lösen ordet genom att klicka på spill-menyn (**lodräta tre punkter**) på labb rutan och välja **Återställ lösen ord**. 

![Återställ lösen ord senare](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Nästa steg
Information om andra elevers användnings alternativ som en labb ägare kan konfigurera finns i följande artikel: [Konfigurera elev användning](how-to-configure-student-usage.md).
