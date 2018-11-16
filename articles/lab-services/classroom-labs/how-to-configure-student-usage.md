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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 30c033b487fe58d017080b02c257502f82338164
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710048"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurera inställningar och principer
Den här artikeln beskriver hur du konfigurerar antalet användare för labbet, få dem registrerad labbet, antalet timmar som de kan använda den virtuella datorn och mycket mer. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Ange hur många användare som tillåts i laboratoriet

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna

1. Växla till den **användare** genom att välja **användare** på den vänstra menyn. 
2. Välj **Get registreringslänk** panelen.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. I dialogrutan **Användarregistrering** väljer du **Stäng**. 
4. Dela registreringslänken med en student så att studenten kan registrera sig för klassen. Om du har den **begränsa alternativet** inställningen aktiverad och har en lista över användare i listan, göra följande:
    1. Välj den **e-postadress** för användaren i listan. 
    2. Du ser ett fönster från ditt e-postprogram med den **till** adress fyllt i. 
    3. Klistra in den **Registreringswebbadress** du kopierade tidigare. 
    4. Skicka den **e-post**. 

## <a name="view-users-registered-with-the-lab"></a>Visa användare som har registrerats med labbet

Välj **användare** på den vänstra menyn för att se en lista över användare som har registrerats med labbet. 

![Lista över användare som har registrerats med labbet](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Ange kvoter per användare

1. Välj **användare** på den vänstra menyn.
2. Välj **kvot per användare: obegränsad** i verktygsfältet. 
3. På den **kvot per användare** väljer **begränsa antalet timmar som en användare kan använda en virtuell dator**. 
4. För **hur många timmar du vill ge till varje användare**anger hur många timmar och väljer **spara**. 

    ![Antal timmar per användare](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Du ser hur många timmar i verktygsfältet nu: **kvot per användare: &lt;antal timmar&gt;**. 

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="add-users-to-the-lab"></a>Lägga till användare i laboratoriet
Om du har den **begränsa åtkomsten** aktiverat, lägga till användare (e-postadresser) i listan.

1. Välj **användare** på den vänstra menyn.
2. Välj **lägga till användare** i verktygsfältet. 
3. På den **lägga till användare** anger e-postadresser för användare i separata rader eller i en enda rad, avgränsade med semikolon. 

    ![Lägg till användarnas e-postadresser](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Välj **Spara**. Du ser e-postadresserna för användarna och deras status (registrerad eller inte) i listan. 

    ![Användarlistan](../media/how-to-configure-student-usage/users-list-new.png)

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


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Som administratör kan du skapa och hantera lab-konton](how-to-manage-lab-accounts.md)
- [Som labbägare kan du skapa och hantera labb](how-to-manage-classroom-labs.md)
- [Som labbägare kan du konfigurera och publicera mallar](how-to-create-manage-template.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)
