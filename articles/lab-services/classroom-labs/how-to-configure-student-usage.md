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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385611"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurera användnings inställningar och principer
Den här artikeln beskriver hur du lägger till användare i labbet, registrerar dem i labbet, styr antalet timmar som de kan använda den virtuella datorn och mer. 


## <a name="add-users-to-the-lab"></a>Lägga till användare i labbet
Om du har aktiverat **begränsa åtkomsten** lägger du till användare (e-postadresser) i listan.

1. Välj **Användare** på den vänstra menyn.
2. Välj **Lägg till användare** i verktygsfältet. 

    ![Knappen Lägg till användare](../media/how-to-configure-student-usage/add-users-button.png)
1. På sidan **Lägg till användare** anger du e-postadresser för användare i separata rader eller på en enda rad, avgränsade med semikolon. 

    ![Lägga till e-postadress till användare](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser användarnas e-postadresser och deras status (registrerad eller inte) i listan. 

    ![Användarlista](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Dela registrerings länk med studenter
Använd någon av följande metoder för att skicka registrerings länken till eleverna. Den första metoden visar hur du skickar e-post till studenter med registrerings länken och ett valfritt meddelande. Den andra metoden visar hur du hämtar registrerings länken som du kan dela med andra på det sätt du vill. 

Om alternativet **begränsa åtkomst** är aktiverat för labbet kan endast användare i listan med användare använda registrerings länken för att registrera sig på labbet. Det här alternativet är aktiverat som standard. 

### <a name="send-email-to-users"></a>Skicka e-post till användare
Azure Lab Services gör det möjligt för lärare att e-posta labb inbjudningar till alla eller valda studenter utan att behöva använda en annan e-postklient. Lärare kan hovra över en enskild elev i listan för att se e-postikonen för varje elev eller välja en eller flera studenter och använda **Skicka inbjudan** i verktygsfältet. Den här funktionen skickar ett e-postmeddelande med en registrerings länk och ett meddelande (om sådant finns) som lagts till av läraren. När inbjudan har skickats ändras inbjudan till inbjudan som **skickas** , så att lärare kan hålla reda på vilka studenter redan har tagit emot registrerings länken och det datum då den skickades.

1. Växla till **användarvyn** om du inte är på sidan redan. 
2. Välj vissa eller alla användare i listan. Markera kryss rutor i den första kolumnen i listan för att välja vissa användare. Markera kryss rutan framför rubriken för den första kolumnen (**namn**) eller markera alla kryss rutor för alla användare i listan för att markera alla användare. Du kan se status för Inbjudnings **tillståndet** i den här listan.  I följande bild har Inbjudnings läget för alla studenter angetts till inbjudan har **inte skickats**. 

    ![Välj studenter](../media/tutorial-setup-classroom-lab/select-students.png)
1. Välj **e-postikonen (kuvertet)** på en av raderna (eller) Välj **Skicka inbjudan** i verktygsfältet. Du kan också Hovra musen över ett elev namn i listan för att se e-postikonen. 

    ![Skicka registrerings länk via e-post](../media/tutorial-setup-classroom-lab/send-email.png)
4. Följ dessa steg på sidan **Skicka registrerings länk via e-post** : 
    1. Skriv ett **valfritt meddelande** som du vill skicka till eleverna. E-postmeddelandet innehåller automatiskt registrerings länken. 
    2. På sidan **Skicka registrerings länk via e-post** väljer du **Skicka**. Du ser status för inbjudan att ändra för att **Skicka inbjudan** och sedan för att **Skicka**inbjudan. 
        
        ![Inbjudningar har skickats](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Hämta registrerings länk
1. Växla till vyn **användare** genom att välja **användare** på den vänstra menyn. 
2. Välj panelen **Hämta registreringslänk**.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. I dialogrutan **Användarregistrering** väljer du **Stäng**. 
4. Dela **registrerings länken** med en student så att studenten kan registreras för klassen. 

## <a name="view-users-registered-with-the-lab"></a>Visa användare som har registrerats med labbet

Välj **användare** på den vänstra menyn för att visa en lista över användare som registrerats i labbet. 

![Lista med användare som registrerats i labbet](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Ange kvoter för användare
Du kan ange kvoter per användare med hjälp av följande steg: 

1. Välj **användare** på den vänstra menyn om sidan inte redan är aktiv. 
2. Välj **kvot per användare: 10 timmar** i verktygsfältet. 
3. På sidan **kvot per användare** anger du antalet timmar som du vill ge varje användare (student): 
    1. **Totalt antal labb timmar per användare**. Användare kan använda sina virtuella datorer för det angivna antalet timmar (anges för det här fältet) **utöver den schemalagda tiden**. Om du väljer det här alternativet anger du **antalet timmar** i text rutan. 

        ![Antal timmar per användare](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 timmar (endast schema)** . Användare kan endast använda sina virtuella datorer under den schemalagda tiden eller när du som labb ägare aktiverar virtuella datorer för dem.

        ![Noll timmar – endast schemalagd tid](../media/how-to-configure-student-usage/zero-hours.png)
    4. Välj **Spara**. 
5. Nu visas ändrade värden i verktygsfältet: **Kvot per användare: &lt;antal timmar&gt;** . 

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> Innan du skickar registrerings länken till eleverna, måste lärare antingen ange schemat för klassen om de väljer 0 kvot timmar eller ange kvot timmar för labbet.
>
> Den [schemalagda körningen av virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

### <a name="add-users-by-uploading-a-csv-file"></a>Lägg till användare genom att ladda upp en CSV-fil
Du kan också lägga till användare genom att ladda upp en CSV-fil med e-postadresser till användare.

1. Skapa en CSV-fil med e-postadresser till användare i en kolumn.

    ![Kvot per användare](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. På sidan **användare** i labbet väljer du **Ladda upp CSV** i verktygsfältet.

    ![Knappen Ladda upp CSV](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Välj CSV-filen med användarens e-postadresser. När du väljer **Öppna** när du har valt CSV-filen visas följande **Lägg till användare** -fönster. Listan med e-postadresser är fylld med e-postadresser från CSV-filen. 

    ![Fönstret Lägg till användare som har fyllts med e-postadresser från CSV-filen](../media/how-to-configure-student-usage/add-users-window.png)
4. Välj **Spara** i fönstret **Lägg till användare** . 
5. Bekräfta att du ser användare i listan över användare. 

    ![Lista över tillagda användare](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Hantera virtuella användar datorer
När eleverna registrerar sig för Azure Lab Services med hjälp av registrerings länken du fick till dem, ser du de virtuella datorer som har tilldelats till eleverna på fliken **virtuella datorer** . 

![Virtuella datorer som har tilldelats studenter](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Du kan utföra följande uppgifter på en elev-VM: 

- Stoppa en virtuell dator om den virtuella datorn körs. 
- Starta en virtuell dator om den virtuella datorn har stoppats. 
- Anslut till den virtuella datorn. 
- Ta bort den virtuella datorn. 
- Visa antalet timmar som användare har använt den virtuella datorn. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Uppdatera antalet virtuella datorer i labbet
För att uppdatera antalet virtuella datorer i labbet, utför följande steg på sidan **Virtual Machines** :

1. Välj **virtuella datorer** på den vänstra menyn. 
2. Välj **labb kapacitet: &lt;Number&gt; Machine (s)** i verktygsfältet. 
3. Ange **antalet** virtuella datorer.
4. Välj **Spara**.

    ![Virtuella datorer i labbet](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Som labb användare, åtkomst till klass rum labb](how-to-use-classroom-lab.md)
