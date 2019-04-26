---
title: Uppdatera din information för grupper från portalen Mina appar - Azure Active Directory | Microsoft Docs
description: Lär dig att visa och uppdatera dina grupper-relaterad information, inklusive visa de grupper som du äger, skapa nya grupper, visa de grupper som du redan är medlem och ansluta till alla grupper som du inte redan tillhör.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578ca5fa1de338a92280e2dc7cc252fb616e2111
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60483416"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>Uppdatera din information för grupper från portalen Mina appar
Du kan använda ditt arbets- eller skolkonto med den webbaserade **Mina appar** portalen för att visa och starta många av organisationens molnbaserade appar, att uppdatera en del av din profil och konto information att se din **grupper** information och utföra **åtkomstgranskningar** för dina appar och grupper. Om du inte har åtkomst till den **Mina appar** portalen, måste du kontakta din supportavdelning för behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Innehållet är avsett för användare. Om du är administratör kan du hittar mer information om hur du konfigurerar och hanterar dina molnbaserade appar i den [dokumentation om Application Management](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Se information om din grupper
Om din administratör har gett dig behörighet att visa den **grupper** sida vid sida, kan du:

- **Som en gruppmedlem.** Visa information om eller lämna en grupp.

- **Som en gruppägare.** Visa information, skapa en ny grupp, lägga till eller ta bort medlemmar eller ta bort gruppen.

### <a name="to-view-your-groups-information"></a>Visa grupper-information

1.  Logga in på ditt arbets- eller skolkonto konto.

2.  Öppna webbläsaren och gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Exempelvis kan du dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Den **appar** visas, som visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

    ![Sidan appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Välj den **grupper** ikonen för att visa din grupp-relaterad information.

    ![Sidan med både ägs och medlemsgrupper](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Baserat på dina behörigheter kan du använda den **grupper** sidan till:

    - **Granska de grupper som du äger.** Visa information om eventuella grupper som du äger i din organisation från den **grupper som jag äger** området. Att välja en specifik gruppnamn får du mer information om gruppen, inklusive grupptypen, antalet medlemmar, join-principen och listan över aktiva medlemmar.

    - **Skapa en ny grupp.** Skapa en ny grupp med dig som ägare från den **grupper som jag äger** området. Specifika anvisningar finns i den [skapa en ny grupp](#create-a-new-group) i den här artikeln.

    - **Redigera en befintlig grupp.** Redigera informationen för någon av dina egna grupper. Specifika anvisningar finns i den [redigera en befintlig grupp](#edit-an-existing-group) i den här artikeln.

    - **Lägg till eller ta bort medlemmar.** Lägg till eller ta bort medlemmar för grupper som du äger. Specifika anvisningar finns i den [Lägg till eller ta bort medlemmar](#add-or-remove-a-member) i den här artikeln.

    - **Förnya ett Office 365-grupp.** Om din organisation tillåter det, kan du förnya din Office 365-grupper. Specifika anvisningar finns i den [förnya en Office 365-grupp](#renew-an-office-365-group) i den här artikeln. 

    - **Ta bort en grupp.** Ta bort alla grupper som du äger. Specifika anvisningar finns i den [ta bort en grupp](#delete-a-group) i den här artikeln.

    - **Granska de grupper som du är en del av.** Visa namnen i några grupper som du är medlem från den **grupper som jag i** området. Att välja en specifik gruppnamn får du mer information om gruppen, inklusive grupptypen, antalet medlemmar, join-principen och listan över aktiva medlemmar.

    - **Anslut till en grupp.** Ansluta till en befintlig grupp, som du inte redan är medlem i från den **grupper som jag i** området. Specifika anvisningar finns i den [ansluta till en befintlig grupp](#join-an-existing-group).

## <a name="create-a-new-group"></a>Skapa en ny grupp
1. På den **grupper** väljer **skapa en grupp** från den **grupper som jag äger** området.

    Den **Skapa grupp** visas.

    ![Skapa gruppruta](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Ange informationen som krävs:

    - **Grupptyp:**
        
        - **Säkerhet.** Används för att hantera medlems- och datoråtkomst till delade resurser för en grupp användare. Du kan till exempel skapa en säkerhetsgrupp för en specifik säkerhetsprincip. Genom att göra det på det sättet så kan du ge en uppsättning behörigheter till alla medlemmar på samma gång utan att behöva lägga till behörigheter till varje medlem individuellt.

        - **Office 365.** Ger samarbetsmöjligheter genom att ge medlemmar tillgång till en delad postlåda, kalender, filer, SharePoint-webbplats och mer. Det här alternativet låter dig även ge personer utanför din organisation tillgång till gruppen.

    - **Gruppnamn.** Lägg till ett namn för gruppen, något som du kommer ihåg och som låter logiskt.

    - **Gruppen beskrivning (valfritt).** Lägg till en valfri beskrivning till din grupp.

    - **Princip för gruppen.** Välj antingen Tillåt alla att ansluta till gruppen eller att endast tillåta ägaren att lägga till medlemmar i gruppen.

3. Välj **Skapa**.

    Den nya gruppen har skapats med dig som ägare och visas i din **grupper som jag äger** lista. Eftersom du äger den här gruppen även visas i den **grupper som jag i** lista.

## <a name="edit-an-existing-group"></a>Redigera en befintlig grupp
När du har skapat en grupp kan du redigera information om den och uppdatera den befintliga information.

### <a name="to-edit-your-details"></a>Så här redigerar du din information
1. Välj den grupp som du vill redigera från den **grupper** och välj sedan **redigera information** på den *&lt;group_name&gt;* sidan.

    Den **redigera information** visas och du kan uppdatera den information som du har lagt till när du först skapade gruppen.

2. Se alla dina ändringar och välj sedan **uppdatering**.

## <a name="add-or-remove-a-member"></a>Lägg till eller ta bort medlem
Du kan lägga till eller ta bort medlemmar för någon av dina egna grupper.

### <a name="to-add-or-remove-a-member"></a>Att lägga till eller ta bort medlem
1. Välj den grupp som du vill lägga till medlemmar till och välj sedan **+** på den *&lt;group_name&gt;* sidan.

    ![Lägg till gruppmedlem i med + logga markerat](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Sök efter den medlemmen som du vill lägga till, från den **lägga till medlemmar** och väljer sedan **Lägg till**.

    ![Lägg till medlemmar i rutan med ny medlem för att lägga till](media/my-apps-portal/my-apps-portal-add-member-page.png)

    En inbjudan skickas till den nya medlemmen för att komma igång åtkomst till organisationens appar.

3. Om du har lagt till en medlem av misstag eller om en medlem har lämnat organisationen, kan du ta bort medlemmen genom att välja **ta bort medlem** bredvid medlemmens namn på den *&lt;group_name&gt;* sidan.

    ![Ta bort en medlem med borttagning av länken markerad](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Förnya ett Office 365-grupp
Om din organisation tillåter det, kan du förnya ett Office 365-grupp, utöka dina upphör att gälla.

### <a name="to-renew-a-group"></a>Förnya en grupp
1. Välj Office 365-grupp som du vill förnya och välj sedan **förnya grupp**.

    ![Förnya ett Office 365-grupp som utökar utgångsdatumet](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Klicka på **OK** att Stäng bekräftelsemeddelandet.

    När du uppdaterar sidan visas det uppdaterade **senaste förnyas** och **gruppen upphör att gälla** datum.

## <a name="delete-a-group"></a>Ta bort en grupp
Du kan ta bort någon av dina egna grupper när som helst. Om du av misstag tar bort en grupp måste du dock skapa den och lägga till medlemmar igen.

### <a name="to-delete-the-group"></a>Att ta bort gruppen
1. Välj den grupp som du vill ta bort permanent och välj sedan **ta bort grupp** på den *&lt;group_name&gt;* sidan.

    ![< guppnamn > sidan med ta bort gruppen länken markerad](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Välj **Ja** i bekräftelsemeddelandet.

    Gruppen bort permanent. 
    
## <a name="join-an-existing-group"></a>Ansluta till en befintlig grupp
Du kan delta i en redan befintlig grupp från den **grupper** sidan.

### <a name="to-join-or-leave-a-group"></a>Att ansluta till eller lämna en grupp

1. På den **grupper** väljer **ansluter till en grupp** från den **grupper som jag i** området.

    Den **Anslut till grupper** visas.

    ![Gå med i gruppsidan med knappen för anslutning till grupp markerat](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. På den **Anslut till grupper** markerar du namnet på den grupp du vill ansluta till, visa information om associerade gruppen och sedan om gruppen är tillgängligt, väljer **ansluter till en grupp**.

    Om gruppen kräver gruppägare godkänna medlemskap måste du att ombes att ange en motivering för varför du behöver ansluta till gruppen och välj sedan **begära**. Om gruppen kräver godkännande kan du omedelbart har lagts till som en medlem och gruppen visas i din **grupper som jag i** lista.

3. Om du har anslutit en grupp av misstag eller om du inte längre behöver vara en del av det, kan du välja gruppnamnet från den **Anslut till grupper** och välj sedan **MNA grupp**.

    ![Träffa lämna grupperingsknappen markerad gruppsidan](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>Nästa steg

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md).

- [Ändra profilinformationen](my-apps-portal-end-user-update-profile.md).

- [Utföra egna åtkomstgranskningar](my-apps-portal-end-user-access-reviews.md).