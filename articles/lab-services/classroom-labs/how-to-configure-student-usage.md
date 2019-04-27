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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 494c285f1c096a84925d9d9a4fb98409960e5230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703786"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurera inställningar och principer
Den här artikeln beskriver hur du lägga till användare i labbet, hämta dem registrerad labbet, antalet timmar som de kan använda den virtuella datorn och mycket mer. 


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet
Om du har den **begränsa åtkomsten** aktiverat, lägga till användare (e-postadresser) i listan.

1. Välj **Användare** på den vänstra menyn.
2. Välj **Lägg till användare** i verktygsfältet. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Dela registreringslänk med studenter
Använd någon av följande metoder för att skicka registreringslänken för studenter. Den första metoden visar hur du skickar e-postmeddelanden till studenter registreringslänken och ett valfritt meddelande. Den andra metoden visar hur du hämtar registreringslänk som du kan dela med andra sätt som du vill. 

Om den **begränsa åtkomsten** har aktiverats för labbet, endast användare i listan över användare som kan använda registreringslänken för att registrera till labbet. Det här alternativet är aktiverat som standard. 

### <a name="send-email-to-users"></a>Skicka e-post till användare
Azure Lab Services tillåter lärare att e-labb inbjudningar till alla eller valda studenter utan att använda en annan e-postklient. Lärare kan hovra över enskilda student i listan för att se postikonen e-för varje elev eller välj en eller flera studenter och Använd **skicka inbjudan** i verktygsfältet. Den här funktionen skickar ett e-postmeddelande med en registreringslänk och ett meddelande (i förekommande fall) har lagts till av Lärarens. När inbjudan har skickats inbjudan tillståndet ändras till **inbjudan har skickats** så att lärare kan hålla reda på vilka studenter redan fått registreringslänken och det datum då den skickades.

1. Växla till **användarvyn** om du inte är på sidan redan. 
2. Välj specifika eller alla användare i listan. Välj specifika användare genom att markera kryssrutor i den första kolumnen i listan. För att välja alla användare, markerar du kryssrutan framför titeln på den första kolumnen (**namn**) eller markera alla kryssrutor för alla användare i listan. Du kan se status för den **inbjudan tillstånd** i den här listan.  I följande bild, inbjudan-status för alla studenter är inställd på **inbjudan har inte skickats**. 

    ![Välj elever](../media/tutorial-setup-classroom-lab/select-students.png)
1. Välj den **e-postikonen / kuvert-** i någon av raderna (eller) Välj **skicka inbjudan** i verktygsfältet. Du kan även hovra med musen över en Elevens namn i listan för att se postikonen e. 

    ![Skicka registreringslänk via e-post](../media/tutorial-setup-classroom-lab/send-email.png)
4. På den **skicka registreringslänk via e-post** utför de här stegen: 
    1. Skriv ett **valfritt meddelande** som du vill skicka till elever. E-postmeddelandet innehåller den automatiskt registreringslänken. 
    2. På den **skicka registreringslänk via e-post** väljer **skicka**. Du ser status för inbjudan ändrar till **skickar inbjudan** och sedan till **inbjudan har skickats**. 
        
        ![Inbjudningar som skickas](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Hämta registreringslänk
1. Växla till den **användare** genom att välja **användare** på den vänstra menyn. 
2. Välj panelen **Hämta registreringslänk**.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. I dialogrutan **Användarregistrering** väljer du **Stäng**. 
4. Dela den **registreringslänk** med en student så att studenter kan registreras för klassen. 

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
