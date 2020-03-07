---
title: Konfigurera användnings inställningar i klass labb labb med Azure Lab Services
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 9fda1295bcdcd44b8a92c101c2aa445197ce6d4a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358641"
---
# <a name="add-and-manage-lab-users"></a>Lägg till och hantera labbanvändare
Den här artikeln beskriver hur du lägger till användare i labbet, registrerar dem i labbet, styr antalet timmar som de kan använda den virtuella datorn och mer. 


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet

1. Välj **Användare** på den vänstra menyn. Som standard är alternativet **Begränsa åtkomst** aktiverat. När den här inställningen är aktiverad kan inte användaren registrera med labbet även om användaren har registreringslänken, såvida inte användaren finns i listan över användare. Endast användare i listan kan registreras med labbet genom att använda registreringslänken som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också stänga av **Begränsa åtkomst**, vilket gör att användare registrerar sig med labbet så länge de har registreringslänken. 
2. Välj **Lägg till användare** i verktygsfältet och välj sedan **Lägg till efter e-postadresser**. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Du ser namn på användare i listan när de har registrerats i labbet. Namnet som visas i listan skapas med hjälp av användarens för-och efter namn i Azure Active Directory. 

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
1. Växla till vyn **användare** om du inte redan är på sidan och välj **Bjud in alla** i verktygsfältet. 

    ![Välj studenter](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. På sidan **Skicka inbjudan via e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. E-postmeddelandet innehåller automatiskt registrerings länken. Du kan hämta den här registrerings länken genom att välja **... (tre punkter)** i verktygsfältet och **registrerings länken**. 

    ![Skicka registrerings länk via e-post](../media/tutorial-setup-classroom-lab/send-email.png)
4. Du ser status för **inbjudan** i listan **användare** . Statusen bör ändras till att **skickas** och sedan **skickas till \<datum >** . 

    Mer information om hur du lägger till studenter i en klass och hur du hanterar användningen av labbet finns i [så här konfigurerar du elev användning](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Bjud in valda användare

1. Välj en eller flera användare i listan. 
2. Välj sedan ikonen **Kuvert** som visas på den markerade raden (eller) Välj **Bjud** in i verktygsfältet. 

    ![Bjud in valda användare](../media/how-to-configure-student-usage/invite-selected-users.png)
3. I fönstret **Skicka inbjudan per e-post** anger du ett valfritt **meddelande**och väljer sedan **Skicka**. 

    ![Skicka e-post till markerade användare](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Du ser status för den här åtgärden i kolumnen **inbjudan** i listan **användare** . E-postinbjudan innehåller en registrerings länk som användarna kan använda för att registrera sig i labbet.

1. Växla till vyn **användare** om du inte redan är på sidan. 

## <a name="get-registration-link"></a>Hämta registrerings länk
Du kan också hämta registrerings länken från portalen och skicka den med ditt eget e-postklientprogram. 

1. Växla till vyn **användare** genom att välja **användare** på den vänstra menyn. 
2. Välj **... (tre punkter)** i verktygsfältet och välj sedan **registrerings länken**.

    ![Länk för elevregistrering](../media/how-to-configure-student-usage/registration-link-button.png)
1. I dialog rutan **användar registrering** väljer du knappen **Kopiera** . Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/how-to-configure-student-usage/registration-link.png)
2. I dialog rutan **användar registrering** väljer du **färdig**. 
4. Skicka **registrerings länken** till en student så att studenten kan registreras för klassen. 

## <a name="view-registered-users"></a>Visa registrerade användare

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton.
3. På sidan **My labs** (Mina labb) väljer du det labb som du vill spåra användningen för. 
4. Välj **Användare** på den vänstra menyn eller i panelen **Användare**. Du kan se studenter som har registrerats med ditt labb.  

    ![Registrerade användare](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Ange kvoter för användare
Du kan ange kvoter per användare med hjälp av följande steg: 

1. Välj **användare** på den vänstra menyn om sidan inte redan är aktiv. 
2. Välj **kvot per användare: \<antalet > timmar** i verktygsfältet. 
3. På sidan **kvot per användare** anger du antalet timmar som du vill ge varje användare (student) utanför den schemalagda klass tiden och väljer sedan **Spara**.

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Du ser de ändrade värdena i verktygsfältet nu: **kvot per användare: \<antalet timmar >** och även i listan användare.

    ![Kvot per användare – efter](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Den [schemalagda körningen av virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

## <a name="set-additional-quota-for-a-specific-user"></a>Ange ytterligare kvot för en speciell användare
Du kan ange ytterligare kvot för en användare. Den här kvoten är utöver den gemensamma kvot uppsättningen för alla användare i föregående avsnitt. Om du till exempel (som lärare) anger kvoten för alla användare till 10 timmar och anger ytterligare kvot på 5 timmar för en speciell användare får användarna 15 (10 + 5) timmar med kvot. Om du ändrar den gemensamma kvoten senare till, till exempel 15, får användaren 20 (15) timmars kvot. Kom ihåg att denna övergripande kvot ligger utanför den schemalagda tiden. Tiden som student tillbringar på en virtuell labb dator under schema tiden räknas inte mot den här kvoten. 

Det gör du genom att följa dessa steg:

1. Välj en användare (student) i listan med användare på sidan **användare** .
2. Välj sedan **Justera kvot** i verktygsfältet. 

    ![Knappen Justera kvot](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Ange antalet **ytterligare timmar** för den valda användaren eller användarna och välj sedan **Använd**. 

    ![Ytterligare kvot för en användare](../media/how-to-configure-student-usage/additional-quota.png)
4. Du ser den uppdaterade användningen för användaren i kolumnen **användning** . 

    ![Ny användning för användaren](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Elev konton
Om du vill lägga till studenter i ett klass rums labb använder du sina e-postkonton. Följande typer av e-postkonton kan användas:

- Ett student-e-postkonto som tillhandahålls av ditt universitets Office 365 Azure Active Directory (AAD). 
- Ett Microsoft-e-postkonto, till exempel `@outlook.com`, `@hotmail.com`, `@msn.com`eller `@live.com`.
- Ett e-postkonto som inte kommer från Microsoft, till exempel ett som tillhandahålls av Yahoo eller Google. Dessa typer av konton måste dock länkas till ett Microsoft-konto.
- Ett GitHub-konto. Kontot måste vara länkat till ett Microsoft-konto.

### <a name="using-a-non-microsoft-email-account"></a>Använda ett e-postkonto som inte kommer från Microsoft
Studenter kan använda e-postkonton som inte kommer från Microsoft för att registrera sig och logga in i ett klass rums labb.  Registreringen kräver dock att eleverna först skapar en Microsoft-konto som är länkad till den e-postadress som inte kommer från Microsoft.

Många studenter kanske redan har ett Microsoft-konto kopplat till sina e-postadresser som inte kommer från Microsoft. Studenter har till exempel redan en Microsoft-konto om de har använt sin e-postadress med Microsofts andra produkter eller tjänster, t. ex. Office, Skype, OneDrive eller Windows.  

När en student klickar på registrerings-URL: en för att logga in i ett klass rum uppmanas de att ange sina e-postadresser och lösen ord. Om student försök att logga in med en icke-Microsoft-konto som inte har någon Microsoft-konto länkad, får studenten följande fel meddelande: 

![Felmeddelande](../media/how-to-configure-student-usage/cant-find-account.png)

För att kunna registrera dig för en Microsoft-konto bör eleverna gå till [http://signup.live.com](http://signup.live.com).  

> [!IMPORTANT]
> När eleverna loggar in i ett klass rums labb får de inte möjlighet att skapa en Microsoft-konto. Vi rekommenderar därför att du inkluderar denna registrerings länk i e-postmeddelandet för klass rums labb registrering som du skickar till studenter som använder andra konton än Microsoft-konton.

### <a name="using-a-github-account"></a>Använda ett GitHub-konto
Studenter kan också använda ett befintligt GitHub-konto för att registrera sig och logga in i ett klass rums labb. Om studenten redan har en Microsoft-konto länkat till sitt GitHub-konto kan de logga in och ange sitt lösen ord som visas i föregående avsnitt. Om de inte har länkat sitt GitHub-konto till en Microsoft-konto bör de välja **inloggnings alternativ**:

![Länk för inloggnings alternativ](../media/how-to-configure-student-usage/signin-options.png)

På sidan **inloggnings alternativ** väljer du logga in **med GitHub**.

![Logga in med GitHub-länk](../media/how-to-configure-student-usage/signin-github.png)

Slutligen uppmanas de att skapa en Microsoft-konto som är länkad till sitt GitHub-konto. Det sker automatiskt när studenten väljer **Nästa**.  Studenten är sedan omedelbart inloggad och ansluten till klass rummets labb.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)
