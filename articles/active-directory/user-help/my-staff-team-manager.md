---
title: Hantera lösenord och telefonnummer med Min personal (förhandsversion) – Azure AD | Microsoft-dokument
description: Hantera lösenord och telefonnummer för dina användare med Min personal
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 29c27c876e90bce6a38226f68adf44a26465cfea
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770859"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>Delegera användarhantering med Min personal (förhandsgranskning)

Din organisation kan använda **Min personal** för att delegera användarhanteringsuppgifter till auktoritetssiffror, till exempel en butikschef eller gruppledare, för att hjälpa sina anställda att komma åt de program som de behöver. Om gruppmedlemmen inte kan komma åt ett program på grund av att de glömmer ett lösenord går produktiviteten förlorad. Detta driver också upp supportkostnader och orsakar en flaskhals i dina administrativa processer.  Med Min personal kan en gruppmedlem som inte kan komma åt sitt konto få åtkomst igen med bara ett par klick, utan administratörshjälp krävs.

## <a name="manage-your-staff-in-my-staff"></a>Hantera din personal i Min personal

Det är enkelt att hantera dina medarbetare i Min personal. Börja med [att gå till Min personal,](https://aka.ms/mystaff)välja ett team eller en plats och välj sedan en användare. Platser och gruppmedlemmarna på en plats bestäms av IT-administratören och du kan inte ändra dem.

Om du hanterar mer än en plats måste du när du går till Min personal välja en plats för att se den gruppmedlem som är tilldelad platsen.

Om du ännu inte har tillräcklig behörighet för att komma åt Min personal visas följande meddelande "Oj, du verkar inte ha behörighet att se Min personal just nu. Kontakta din administratör för mer information."

### <a name="find-a-staff-member-in-my-staff"></a>Hitta en anställd i Min personal

Du måste öppna profilen för en anställd innan du kan börja hantera dem.

1. [Öppna Min personal](https://aka.ms/mystaff) och välj vid behov en plats.

    ![Välj en plats för en gruppmedlem i Min personal](media/my-staff-team-manager/allaus.png)

1. Öppna en gruppmedlems profil.

    ![Välj en av användarna på en plats i Min personal](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>Återställa ett användarlösenord

Om din organisation har gett dig behörighet kan du återställa lösenord för dina anställda.

1. [Öppna min personal.](https://aka.ms/mystaff)
1. Öppna en anställds profil.
1. Välj **Återställ lösenord**.

    ![Återställa ett användarlösenord i Min personal](media/my-staff-team-manager/resetpassword1.png)

1. Generera eller ange det nya lösenordet. Du kan visas ett automatiskt genererat tillfälligt lösenord eller så kan du bli ombedd att ange ett tillfälligt lösenord för användaren.

    ![Kopiera det tillfälliga användarlösenordet efter en återställning i Min personal](media/my-staff-team-manager/resetpassword2.png)

När du har återställt användarens lösenord ger du användaren det tillfälliga lösenordet. När användaren loggar in med sitt tillfälliga lösenord måste de ändra det.

## <a name="manage-a-users-phone-number"></a>Hantera en användares telefonnummer

Om din organisation har gett dig behörighet kan du hantera telefonnummer för dina anställda.

### <a name="add-a-phone-number"></a>Lägga till ett telefonnummer

1. [Öppna min personal.](https://aka.ms/mystaff)
1. Öppna en anställds profil.
1. Välj **Lägg till telefonnummer**.

    ![Lägga till ett telefonnummer för användare i Min personal](media/my-staff-team-manager/addphone1.png)

1. Lägg till telefonnumret och välj **Spara**.

    ![Spara det tillagda användartelefonnumret i Min personal](media/my-staff-team-manager/addphone2.png)

När du har registrerat ett telefonnummer för en användare kan de använda det för att logga in med SMS, utföra tvåstegsverifiering eller återställa sitt lösenord på egen hand, beroende på organisationens inställningar.

![Nytt telefonnummer registrerat hos Min personal](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>Redigera ett telefonnummer

1. [Öppna min personal.](https://aka.ms/mystaff)
1. Öppna en anställds profil.
1. Välj **Redigera telefonnummer**.

    ![Välj Redigera från användarprofilen i Min personal](media/my-staff-team-manager/editphone2.png)

1. Ange det nya telefonnumret och välj **Spara**.

    ![Redigera ett telefonnummer till en anställd i Min personal](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>Aktivera inloggning av telefonnummer för en användare

Om inloggning med ett telefonnummer som användarnamn (SMS-inloggning) är aktiverat i organisationen kan du lägga till den här autentiseringen i ett befintligt användartelefonnummer.

1. [Öppna min personal.](https://aka.ms/mystaff)
1. Öppna en anställds profil.
1. Om det finns ett meddelande längst ned på skärmen där det står att det är tillgängligt att logga in med ditt telefonnummer som användarnamn väljer du **Aktivera** för att påbörja processen. Det här meddelandet visas om användaren har aktiverats för att logga in med sitt telefonnummer.

    ![Visa meddelandet när telefon inloggning stöds på en plats i Min personal](media/my-staff-team-manager/enableforms1.png)

1. Välj **OK** när du är klar.

    ![Ta bort ett telefonnummer till en anställd i Min personal](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>Ta bort ett telefonnummer

1. [Öppna min personal.](https://aka.ms/mystaff)
1. Öppna en anställds profil.
1. Välj **Ta bort telefonnummer**.
1. Välj **Ta bort** när du är klar.

    ![Ta bort ett telefonnummer till en anställd i Min personal](media/my-staff-team-manager/deletephone1.png)
