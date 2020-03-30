---
title: Uppdatera information om dina grupper på portalen Mina appar - Azure AD
description: Lär dig hur du visar och uppdaterar grupprelaterad information, inklusive att visa de grupper du äger, skapa nya grupper, visa de grupper som du redan är medlem i och gå med i alla grupper som du inte redan ingår i.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022332"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Uppdatera information om dina grupper på portalen Mina appar

Du kan använda ditt arbets- eller skolkonto med den webbaserade **Portalen Mina Appar,** för att visa och starta många av organisationens molnbaserade appar, för att uppdatera en del av din profil- och kontoinformation, för att se din **gruppinformation** och för att utföra **åtkomstgranskningar** för dina appar och grupper. Om du inte har tillgång till Portalen **Mina appar** måste du kontakta din helpdesk för att få behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Innehållet är avsett för användare. Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar dina molnbaserade appar i [dokumentationen för programhantering](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>Visa information om dina grupper

Om administratören har gett dig behörighet att visa panelen **Grupper** kan du:

- **Som gruppmedlem.** Visa information eller lämna en grupp.

- **Som gruppägare.** Visa information, skapa en ny grupp, lägg till eller ta bort medlemmar eller ta bort gruppen.

### <a name="to-view-your-groups-information"></a>Så här visar du information om dina grupper

1. Logga in på ditt arbets- eller skolkonto.

2. Öppna webbläsaren och gå https://myapps.microsoft.comtill , eller använd länken som tillhandahålls av din organisation. Du kan till exempel dirigeras till en anpassad https://myapps.microsoft.com/contoso.comsida för din organisation, till exempel .

    Sidan **Appar** visas som visar alla molnbaserade appar som ägs av din organisation och är tillgängliga för dig att använda.

    ![Sidan Appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Välj panelen **Grupper** om du vill visa grupprelaterad information.

    ![Sidan Grupper med både ägda grupper och medlemsgrupper](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Baserat på dina behörigheter kan du använda sidan **Grupper** för att:

    - **Granska de grupper du äger.** Visa information om alla grupper som du äger inom din organisation från området **Grupper I.** Om du väljer ett visst gruppnamn får du mer information om gruppen, inklusive grupptypen, antalet medlemmar, anslutningsprincipen och listan aktiva medlemmar.

    - **Skapa en ny grupp.** Skapa en ny grupp med dig som ägare från det **gruppera jag egna** området. Specifika steg finns i avsnittet [Skapa en ny grupp](#create-a-new-group) i den här artikeln.

    - **Redigera en befintlig grupp.** Redigera informationen för någon av dina egna grupper. Specifika steg finns i avsnittet [Redigera en befintlig grupp](#edit-an-existing-group) i den här artikeln.

    - **Lägg till eller ta bort medlemmar.** Lägg till eller ta bort medlemmar för grupper som du äger. Specifika steg finns i avsnittet [Lägg till eller ta bort en medlem i](#add-or-remove-a-member) den här artikeln.

    - **Förnya en Office 365-grupp.** Om din organisation tillåter det kan du förnya dina Office 365-grupper. Specifika steg finns i avsnittet [Förnya en Office 365-grupp](#renew-an-office-365-group) i den här artikeln. 

    - **Ta bort en grupp.** Ta bort alla grupper som du äger. Specifika steg finns i avsnittet [Ta bort en grupp](#delete-a-group) i den här artikeln.

    - **Granska de grupper du är en del av.** Visa namnen på alla grupper som du är medlem i från de **grupper jag är i.** Om du väljer ett visst gruppnamn får du mer information om gruppen, inklusive grupptypen, antalet medlemmar, anslutningsprincipen och listan aktiva medlemmar.

    - **Gå med i en grupp.** Gå med i en befintlig grupp, som du inte redan är medlem **för,** från de grupper jag är i området. Specifika steg finns i [Koppla till en befintlig grupp](#join-an-existing-group).

## <a name="create-a-new-group"></a>Skapa en ny grupp

1. På sidan **Grupper** väljer du **Skapa en grupp** i området Grupper som jag **äger.**

    Rutan **Skapa grupp** visas.

    ![Skapa gruppruta](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Ange den information som krävs:

    - **Grupptyp:**

        - **Säkerhet.** Används för att hantera medlems- och datoråtkomst till delade resurser för en grupp användare. Du kan till exempel skapa en säkerhetsgrupp för en specifik säkerhetsprincip. Genom att göra det på det sättet så kan du ge en uppsättning behörigheter till alla medlemmar på samma gång utan att behöva lägga till behörigheter till varje medlem individuellt.

        - **Office 365.** Ger samarbetsmöjligheter genom att ge medlemmar tillgång till en delad postlåda, kalender, filer, SharePoint-webbplats och mer. Det här alternativet låter dig även ge personer utanför din organisation tillgång till gruppen.

    - **Gruppens namn.** Lägg till ett namn för gruppen, något som du kommer ihåg och som låter logiskt.

    - **Gruppbeskrivning (valfritt).** Lägg till en valfri beskrivning till din grupp.

    - **Grupprincip.** Välj antingen att tillåta alla att gå med i gruppen eller att bara tillåta att gruppens ägare lägger till medlemmar.

3. Välj **Skapa**.

    Den nya gruppen skapas med dig som ägare och den visas i listan **Grupper I.** Eftersom du är ägare visas den här gruppen också i listan **Grupper i.**

## <a name="edit-an-existing-group"></a>Redigera en befintlig grupp

När du har skapat en grupp kan du redigera dess information, inklusive uppdatering av all befintlig information.

1. Markera den grupp som du vill redigera på sidan **Grupper** och välj sedan **Redigera information** på * &lt;sidan group_name.&gt; *

    Rutan **Redigera information** visas och du kan uppdatera informationen som du lade till när du först skapade gruppen.

2. Gör alla dina ändringar och välj sedan **Uppdatera**.

## <a name="add-or-remove-a-member"></a>Lägga till eller ta bort en medlem

Du kan lägga till eller ta bort medlemmar för alla grupper som du äger.

1. Markera den grupp som du vill lägga **+** till medlemmar i och välj sedan på * &lt;sidan group_name.&gt; *

    ![Lägga till en gruppmedlem med + tecken markerat](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Sök efter den medlem du vill lägga till i rutan **Lägg till medlemmar** och välj sedan Lägg **till**.

    ![Lägg till medlemsruta, med ny medlem att lägga till](media/my-apps-portal/my-apps-portal-add-member-page.png)

    En inbjudan skickas till den nya medlemmen för att komma igång med organisationens appar.

3. Om du har lagt till en medlem av misstag, eller om en medlem har lämnat organisationen, kan du ta bort medlemmen genom att välja **Ta bort medlemmen** bredvid medlemmens namn på sidan * &lt;group_name.&gt; *

    ![Ta bort en medlem, med borttagningslänken markerad](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Förnya en Office 365-grupp

Om din organisation tillåter det kan du förnya en Office 365-grupp och förlänga utgångsdatumet.

1. Välj den Office 365-grupp som du vill förnya och välj sedan **Förnya grupp**.

    ![Förnya en Office 365-grupp, vilket förlänger utgångsdatumet](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Stäng bekräftelsemeddelandet genom att klicka på **OK.**

    När du har uppdaterat sidan visas dina uppdaterade **sista förnyade** och **gruppförfallodatum.**

## <a name="delete-a-group"></a>Ta bort en grupp

Du kan när som helst ta bort någon av dina egna grupper. Men om du tar bort en grupp av misstag måste du skapa den och lägga till medlemmar igen.

1. Markera den grupp som du vill ta bort permanent och välj sedan **Ta bort grupp** på sidan * &lt;group_name.&gt; *

    ![<Group_name> sida med länken Ta bort grupp markerad](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Välj **Ja** i bekräftelsemeddelandet.

    Gruppen tas bort permanent.

## <a name="join-an-existing-group"></a>Gå med i en befintlig grupp

Du kan gå med i eller lämna en redan befintlig grupp från sidan **Grupper.**

1. På sidan **Grupper** väljer du **Gå med i gruppen** i området Grupper som jag är **i.**

    Sidan **Gå med i grupper** visas.

    ![Sidan Koppla till grupper, med knappen Koppla till grupp markerad](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. På sidan **Gå med i grupper** väljer du namnet på den grupp som du vill gå med i, visar den associerade gruppinformationen och väljer sedan Gå med i **gruppen**om gruppen är tillgänglig.

    Om gruppen kräver att gruppägaren godkänner medlemskapet uppmanas du att ange en affärsmotivering för varför du måste gå med i gruppen och väljer sedan **Begär**. Om gruppen inte kräver godkännande läggs du omedelbart till som medlem och gruppen visas i listan **Grupper i.**

3. Om du gick med i en grupp av misstag eller om du inte längre behöver vara en del av den kan du välja gruppnamnet på sidan **Gå med i grupper** och sedan välja Lämna **grupp**.

    ![Sidan Koppla till grupper, med knappen Lämna grupp markerad](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Nästa steg

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md).

- [Ändra din profilinformation](my-apps-portal-end-user-update-profile.md).

- [Utför dina egna åtkomstrecensioner](my-apps-portal-end-user-access-reviews.md).
