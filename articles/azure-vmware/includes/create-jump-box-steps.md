---
title: Skapa hopp rutan för Azure VMware-lösningen
description: Steg för att skapa en länk ruta för Azure VMware-lösningen.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 5d5a5d82cf6e70e7ad55bcfa10c7f85372131c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578433"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. I resurs gruppen väljer du **+ Lägg till** och sedan Sök och väljer **Microsoft Windows 10**. Välj sedan **skapa**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Lägg till en ny virtuell Windows 10-dator för en hopp ruta." border="true":::

1. Ange nödvändig information i fälten och välj sedan **Granska + skapa**. 

   Mer information om fälten finns i följande tabell.

   | Field | Värde |
   | --- | --- |
   | **Prenumeration** | Värdet är i förväg ifyllt med prenumerationen som tillhör resurs gruppen. |
   | **Resursgrupp** | Värdet är i förväg ifyllt för den aktuella resurs gruppen som du skapade i föregående självstudie.  |
   | **Namn på virtuell dator** | Ange ett unikt namn för den virtuella datorn. |
   | **Region** | Välj den geografiska platsen för den virtuella datorn. |
   | **Tillgängliga alternativ** | Låt standardvärdet vara markerat. |
   | **Avbildning** | Välj avbildningen av den virtuella datorn. |
   | **Storlek** | Lämna värdet för standard storlek. |
   | **Autentiseringstyp**  | Välj **lösen ord**. |
   | **Användarnamn** | Ange användar namnet för att logga in på den virtuella datorn. |
   | **Lösenord** | Ange lösen ordet för att logga in på den virtuella datorn. |
   | **Bekräfta lösenord** | Ange lösen ordet för att logga in på den virtuella datorn. |
   | **Offentliga inkommande portar** | Välj **Ingen**. Om du väljer Ingen kan du använda [JIT-åtkomst](../../security-center/security-center-just-in-time.md#jit-configure) för att kontrol lera åtkomsten till den virtuella datorn endast när du vill komma åt den.  |


1. När verifieringen har godkänts väljer du **skapa** för att starta processen för att skapa virtuella datorer.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Lägg till en ny virtuell Windows 10-dator för en hopp ruta." border="true":::