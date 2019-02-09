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
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964822"
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
3. På den **kvot per användare** väljer du något av följande alternativ: 
    1. **Ingen**. Användare kan använda sina virtuella datorer under schema eller när labbägare sätter på virtuella datorer för dessa.
    2. **Obegränsat (standard)**. Användare kan använda sina virtuella datorer utan några tidsbegränsningar.
    3. **Ange antal timmar per användare**. Användare kan använda sina virtuella datorer för ett angivet antal timmar (anges nedan) utöver den schemalagda tiden. Om du väljer det här alternativet anger du den **antal timmar** i textrutan. 

        ![Antal timmar per användare](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Välj **Spara**. 
5. Du ser uppdaterade värden i verktygsfältet nu: **Kvot per användare: &lt;antal timmar&gt;**. 

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> Den [schemalagda körningstid för virtuella datorer](how-to-create-schedules.md) räknas inte mot kvoten som tilldelats till en användare. Kvoten är tiden utanför Schemalägg timmar som en student tillbringar på virtuella datorer. 

### <a name="add-users-by-uploading-a-csv-file"></a>Lägga till användare genom att ladda upp en CSV-fil
Du kan också lägga till användare genom att ladda upp en CSV-fil med e-postadresserna för användarna.

1. Skapa en CSV-fil med e-postadresser för användare i en kolumn.

    ![Kvot per användare](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. På den **användare** sidan labbet, väljer **ladda upp CSV** i verktygsfältet.

    ![Ladda upp CSV-knappen](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Välj CSV-fil med användarnas e-postadresser. När du väljer **öppna** efter att CSV-fil, visas följande **lägga till användare** fönster. E-postadress listan fylls med e-postadresser från CSV-filen. 

    ![Lägg till användarfönstret fylls i med e-postadresser från CSV-fil](../media/how-to-configure-student-usage/add-users-window.png)
4. Välj **spara** i den **lägga till användare** fönster. 
5. Kontrollera att du ser användare i listan över användare. 

    ![Lista över användare som har lagts till](../media/how-to-configure-student-usage/list-of-added-users.png)

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
