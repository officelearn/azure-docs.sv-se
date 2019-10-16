---
title: Konfigurera användnings inställningar i klass rummets labb Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar antalet användare för labbet, registrera dem med labbet, styr antalet timmar som de kan använda den virtuella datorn och mer.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324459"
---
# <a name="add-and-manage-lab-users"></a>Lägga till och hantera labb användare
Den här artikeln beskriver hur du lägger till användare i labbet, registrerar dem i labbet, styr antalet timmar som de kan använda den virtuella datorn och mer. 


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

1. Välj **Användare** på den vänstra menyn. Som standard är alternativet **Begränsa åtkomst** aktiverat. När den här inställningen är aktiverad kan inte användaren registrera med labbet även om användaren har registreringslänken, såvida inte användaren finns i listan över användare. Endast användare i listan kan registreras med labbet genom att använda registreringslänken som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också stänga av **Begränsa åtkomst**, vilket gör att användare registrerar sig med labbet så länge de har registreringslänken. 
2. Välj **Lägg till användare** i verktygsfältet och välj sedan **Lägg till efter e-postadresser**. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Lägg till användare genom att ladda upp en CSV-fil
Du kan också lägga till användare genom att ladda upp en CSV-fil med e-postadresser till användare.

1. Skapa en CSV-fil med e-postadresser till användare i en kolumn.

    ![CSV-fil med användare](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. På sidan **användare** i labbet väljer du **Lägg till användare** i verktygsfältet och väljer sedan **Ladda upp CSV**.

    ![Knappen Ladda upp CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Välj CSV-filen med användarens e-postadresser. När du väljer **Öppna** när du har valt CSV-filen visas följande **Lägg till användare** -fönster. Listan med e-postadresser är fylld med e-postadresser från CSV-filen. 

    ![Fönstret Lägg till användare som har fyllts med e-postadresser från CSV-filen](../media/how-to-configure-student-usage/add-users-window.png)
4. Välj **Spara** i fönstret **Lägg till användare** . 
5. Bekräfta att du ser användare i listan över användare. 

    ![Lista över tillagda användare](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Skicka inbjudningar till användare
Använd någon av följande metoder för att skicka registrerings länken till eleverna. Den första metoden visar hur du skickar e-post till studenter med registrerings länken och ett valfritt meddelande. Den andra metoden visar hur du hämtar registrerings länken som du kan dela med andra på det sätt du vill. 

Om alternativet **begränsa åtkomst** är aktiverat för labbet kan endast användare i listan med användare använda registrerings länken för att registrera sig på labbet. Det här alternativet är aktiverat som standard. 

### <a name="invite-all-users"></a>Bjud in alla användare

1. Växla till sidan **användare** i labbet. 
2. Välj **Bjud in alla** från verktygsfältet. 
3. Ange ett **meddelande** för användarna. Detta är ett valfritt steg.
4. Välj sedan **Skicka**.

    ![Bjud in alla användare](../media/how-to-configure-student-usage/invite-all.png)

    Status för den här åtgärden visas i kolumnen **inbjudan** i listan **användare** . E-postinbjudan kommer att innehålla den registrerings länk som användarna kan använda för att registrera sig i labbet. 

### <a name="invite-selected-users"></a>Bjud in valda användare

1. Välj en eller flera användare i listan. 
2. Välj sedan ikonen **Kuvert** som visas på den markerade raden (eller) Välj **Bjud** in i verktygsfältet. 

    ![Bjud in valda användare](../media/how-to-configure-student-usage/invite-selected-users.png)
3. I fönstret **Skicka inbjudan per e-post** anger du ett valfritt **meddelande**och väljer sedan **Skicka**. 

    ![Skicka e-post till markerade användare](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Status för den här åtgärden visas i kolumnen **inbjudan** i listan **användare** . E-postinbjudan kommer att innehålla den registrerings länk som användarna kan använda för att registrera sig i labbet.

1. Växla till **användarvyn** om du inte är på sidan redan. 

## <a name="get-registration-link"></a>Hämta registrerings länk
Du kan också hämta registrerings länken från portalen och skicka den med ditt eget e-postklientprogram. 

1. Växla till vyn **användare** genom att välja **användare** på den vänstra menyn. 
2. Välj **... (tre punkter)** i verktygsfältet och välj sedan **registrerings länken**.

    ![Länk för elevregistrering](../media/how-to-configure-student-usage/registration-link-button.png)
1. I dialog rutan **användar registrering** väljer du knappen **Kopiera** . Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/how-to-configure-student-usage/registration-link.png)
2. I dialog rutan **användar registrering** väljer du **färdig**. 
4. Skicka **registrerings länken** till en student så att studenten kan registreras för klassen. 

## <a name="view-users-registered-with-the-lab"></a>Visa användare som har registrerats med labbet

Välj **användare** på den vänstra menyn för att visa en lista över användare som registrerats i labbet. 

![Lista med användare som registrerats i labbet](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Ange kvoter för användare
Du kan ange kvoter per användare med hjälp av följande steg: 

1. Välj **användare** på den vänstra menyn om sidan inte redan är aktiv. 
2. Välj **kvot per användare: &lt;number @ no__t-2 timmar** i verktygsfältet. 
3. På sidan **kvot per användare** anger du antalet timmar som du vill ge varje användare (student) utanför den schemalagda klass tiden och väljer sedan **Spara**.

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Du ser de ändrade värdena i verktygsfältet nu: **kvot per användare: &lt;number timmar @ no__t-2**. 

    ![Kvot per användare-efter](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Den [schemalagda körningen av virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

## <a name="set-additional-quota-for-a-specific-user"></a>Ange ytterligare kvot för en speciell användare
Du kan ange en separat kvot för en användare. Det gör du genom att följa dessa steg:

1. Välj en användare (student) i listan med användare på sidan **användare** .
2. Välj sedan **Justera kvot** i verktygsfältet. 

    ![Knappen Justera kvot](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Ange antalet **ytterligare timmar** för den valda användaren eller användarna och välj sedan **Använd**. 

    ![Ytterligare kvot för en användare](../media/how-to-configure-student-usage/additional-quota.png)
4. Den uppdaterade användningen för användaren visas i kolumnen **användning** . 

    ![Ny användning för användaren](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)
