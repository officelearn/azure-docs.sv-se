---
title: Konfigurera inställningar i klassrum labs av Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar antalet användare för labbet, få dem registrerad labbet, antalet timmar som de kan använda den virtuella datorn och mycket mer.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 577860baa74d9f0cd7f411a64d9e6bfa9322731c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894998"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurera inställningar och principer
Den här artikeln beskriver hur du lägga till användare i labbet, hämta dem registrerad labbet, antalet timmar som de kan använda den virtuella datorn och mycket mer. 


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet
Om du har den **begränsa åtkomsten** aktiverat, lägga till användare (e-postadresser) i listan.

1. Välj **Användare** på den vänstra menyn.
2. Välj **Lägg till användare** i verktygsfältet. 

    ![Lägg till användare knapp](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna
Följande procedur innehåller steg för att skicka en registreringslänk till användare. Om den **begränsa åtkomsten** har aktiverats för labbet, endast användare i listan över användare som kan använda registreringslänken för att registrera till labbet. 

1. Växla till den **användare** genom att välja **användare** på den vänstra menyn. 
2. Välj panelen **Hämta registreringslänk**.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. I dialogrutan **Användarregistrering** väljer du **Stäng**. 
4. Dela registreringslänken med en student så att studenten kan registrera sig för klassen. Om du har inställningen **Begränsa alternativ** aktiverad och har en lista över användare i listan gör du följande:
    1. Välj **e-postadressen** för användaren i listan. 
    2. Du ser ett fönster från ditt standard-e-postprogram med **TILL**-adressen ifylld. 
    3. Klistra in den **registreringswebbadress** du kopierade tidigare. 
    4. Skicka **e-postmeddelandet**. 

## <a name="view-users-registered-with-the-lab"></a>Visa användare som har registrerats med labbet

Välj **användare** på den vänstra menyn för att se en lista över användare som har registrerats med labbet. 

![Lista över användare som har registrerats med labbet](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Ange kvoter per användare
Du kan ange kvoter per användare med hjälp av följande steg: 

1. Välj **Användare** på den vänstra menyn.
2. Välj **kvot per användare: obegränsad** i verktygsfältet. 
3. På den **kvot per användare** väljer **begränsa antalet timmar som en användare kan använda en virtuell dator**. 
4. För **hur många timmar du vill ge till varje användare**anger hur många timmar och väljer **spara**. 

    ![Antal timmar per användare](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Du ser hur många timmar i verktygsfältet nu: **Kvot per användare: &lt;antal timmar&gt;**. 

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> Den [schemalagda körningstid för virtuella datorer](how-to-create-schedules.md) räknas inte mot kvoten som tilldelats till en användare. Kvoten är tiden utanför Schemalägg timmar som en student tillbringar på virtuella datorer. 

### <a name="add-users-by-uploading-a-csv-file"></a>Lägga till användare genom att ladda upp en CSV-fil
Du kan också lägga till användare genom att ladda upp en CSV-fil med e-postadresserna för användarna.

1. Välj **ladda upp CSV** i verktygsfältet.
2. Välj CSV-fil med användarnas e-postadresser. E-postadresser måste vara i en kolumn när du öppnar den i Excel. 

## <a name="manage-user-vms"></a>Hantera användarnas VM: ar
När studenter register med Azure Lab Services med hjälp av registreringen länka angivna dem ska du se de virtuella datorerna har tilldelats till studenter på **virtuella datorer** fliken. 

![Virtuella datorer som tilldelats studenter](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Du kan utföra följande uppgifter på en student VM: 

- Stoppa en virtuell dator om Virtuellt datorn körs. 
- Starta en virtuell dator om den virtuella datorn har stoppats. 
- Anslut till den virtuella datorn. 
- Ta bort den virtuella datorn. 
- Visa antalet timmar som den virtuella datorn på användare. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Uppdatera antalet virtuella datorer i labb
Om du vill uppdatera antalet virtuella datorer i labbet, gör du följande den **virtuella datorer** sidan:

1. Välj **virtuella datorer** på den vänstra menyn. 
2. Välj **Lab kapacitet: &lt;nummer&gt; dator(er)** i verktygsfältet. 
3. Ange den **nummer** av virtuella datorer.
4. Välj **Spara**.

    ![Virtuella datorer i labbet](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)
