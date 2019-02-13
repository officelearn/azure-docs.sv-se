---
title: Lägga till användare i B2B-samarbetet som en informationsanställd - Azure Active Directory | Microsoft Docs
description: B2B-samarbete tillåter informationsarbetare och app ägare för att lägga till gästanvändare till Azure AD för åtkomst | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16caf8b6fd48b5dca676b1c8ee046ad01264ca03
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211435"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Hur användare i din organisation kan bjuda in gästanvändare till en app

När du har en gäst har användare lagts till katalogen i Azure AD en programmets ägare kan skicka gästanvändaren en direktlänk till appen som de vill dela. Azure AD-administratörer kan också ställa in självbetjäningshantering för galleriet eller SAML-baserade appar i sina Azure AD-klient. På så sätt kan programmet ägare kan hantera sina egna gästanvändare även om gästanvändare inte har lagts till katalogen ännu. När en app har konfigurerats för självbetjäning, används programmets ägare sin åtkomstpanel att bjuda in gästanvändare till en app eller Lägg till en gästanvändare till en grupp som har åtkomst till appen. Självbetjäning apphantering för galleriet och SAML-baserade appar kräver vissa installationen av en administratör. Följande är en sammanfattning av installationsanvisningarna (mer detaljerade instruktioner finns i [krav](#prerequisites) senare på den här sidan):

 - Aktivera hantering av Självbetjäning för din klient
 - Skapa en grupp om du vill tilldela till appen och göra användaren ägare
 - Konfigurera appen för självbetjäning och tilldela gruppen till appen

> [!NOTE]
> Den här artikeln beskriver hur du ställer in självbetjäning hantering för galleriet och SAML-baserade appar som du har lagt till din Azure AD-klient. Du kan också [ställer in självbetjäning Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) så att användarna kan hantera åtkomst till sina egna Office 365-grupper. Fler sätt användare kan dela Office-filer och appar med gästanvändare, finns i [gäståtkomst i Office 365-grupper](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) och [delar SharePoint-filer eller mappar](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Bjud in en gästanvändare till en app från åtkomstpanelen

När en app har konfigurerats för självbetjäning, använda programägare sina egna åtkomstpanelen att bjuda in gästanvändare till appen som de vill dela. Gästanvändaren behöver inte nödvändigtvis att läggas till i förväg Azure AD. 

1. Öppna din åtkomstpanelen genom att gå till `https://myapps.microsoft.com`.
2. Peka på appen, Välj ellipserna (**...** ), och välj sedan **hantera app**.
 
   ![Åtkomstpaneler hantera app](media/add-users-iw/access-panel-manage-app.png)
 
3. Överst i listan över användare, Välj **+**.
   
   ![Åtkomstpanelen Lägg till en användare](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. I den **lägga till medlemmar** sökrutan, Skriv e-postadress för gästanvändare. Du kan också lägga till ett välkomstmeddelande.
   
   ![Få åtkomst till panelen inbjudan](media/add-users-iw/access-panel-invitation.png)
   
5. Välj **Lägg till** att skicka en inbjudan till gästanvändaren. När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Bjud in andra att delta i en grupp som har åtkomst till appen
När en app har konfigurerats för självbetjäning kan programägare kan bjuda in gästanvändare till de grupper som de hanterar och som har åtkomst till appar som de vill dela. Gästanvändare inte redan finns i katalogen. Programmets ägare följer stegen nedan för att bjuda in gästanvändare till gruppen så att de kan komma åt appen.

1. Kontrollera att du är ägare av gruppen självbetjäning som har åtkomst till appen som du vill dela.
2. Öppna din åtkomstpanelen genom att gå till `https://myapps.microsoft.com`.
3. Välj den **grupper** app.
   
   ![Få åtkomst till panelen grupper appen](media/add-users-iw/access-panel-groups.png)
   
4. Under **grupper som jag äger**, Välj den grupp som har åtkomst till appen som du vill dela.
   
   ![Åtkomstpanelen grupper jag äger](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Överst i listan över gruppmedlemmar, Välj **+**.
   
   ![Åtkomstpanelen grupper Lägg till medlem](media/add-users-iw/access-panel-groups-add-member.png)
   
6. I den **lägga till medlemmar** sökrutan, Skriv e-postadress för gästanvändare. Du kan också lägga till ett välkomstmeddelande.
   
   ![Få åtkomst till panelen grupp inbjudan](media/add-users-iw/access-panel-invitation.png)
   
7. Välj **Lägg till** att skicka inbjudan till gästanvändaren automatiskt. När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.


## <a name="prerequisites"></a>Förutsättningar

Självbetjäning av kräver vissa installationen av en Global administratör och en Azure AD-administratör. Som en del av den här konfigurationen kan du konfigurera appen för självbetjäning och tilldela en grupp till den app som programmets ägare kan hantera. Du kan också konfigurera grupp om du vill att vem som helst att begära medlemskap men kräver en gruppägare godkännande. (Läs mer om [självbetjäningsgrupphantering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Du kan inte lägga till gästanvändare till en dynamisk grupp eller en grupp som har synkroniserats med den lokala Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Aktivera hantering av Självbetjäning för din klient
1. Logga in på den [Azure-portalen](https://portal.azure.com) som Global administratör.
2. I navigeringsfönstret, väljer **Azure Active Directory**.
3. Välj **grupper**.
4. Under **inställningar**väljer **Allmänt**.
5. Under **Self Service grupphantering**, bredvid **ägare kan hantera begäranden om gruppmedlemskap i åtkomstpanelen**väljer **Ja**.
6. Välj **Spara**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Skapa en grupp om du vill tilldela till appen och göra användaren ägare
1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör eller Global administratör.
2. I navigeringsfönstret, väljer **Azure Active Directory**.
3. Välj **grupper**.
4. Välj **ny grupp**.
5. Under **grupptyp**väljer **Security**.
6. Ange ett **gruppnamn** och **Gruppbeskrivning**.
7. Under **Medlemskapstyp**väljer **tilldelad**.
8. Välj **skapa**, och Stäng den **grupp** sidan.
9. På den **grupper – alla grupper** öppnar gruppen. 
10. Under **hantera**väljer **ägare** > **lägga till ägare**. Sök efter den användare som ska hantera åtkomst till programmet. Välj användaren och klicka sedan på **Välj**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurera appen för självbetjäning och tilldela gruppen till appen
1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör eller Global administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **företagsprogram** > **alla program**.
4. Hitta och öppna appen i programlistan med.
5. Under **hantera**väljer **enkel inloggning**, och konfigurera programmet för enkel inloggning. (Mer information finns i [hur du hanterar enkel inloggning för företagsappar](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).)
6. Under **hantera**väljer **självbetjäning**, och Ställ in åtkomst till själva appen. (Mer information finns i [hur du använder självbetjäning appåtkomst](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > För inställningen **till vilken grupp ska tilldelade användare läggas?** Välj den grupp som du skapade i föregående avsnitt.
7. Under **hantera**väljer **användare och grupper**, och kontrollera att gruppen självbetjäning som du har skapat visas i listan.
8. Om du vill lägga till appen i gruppägarens åtkomstpanelen, Välj **Lägg till användare** > **användare och grupper**. Sök efter gruppägare och Välj användaren, klickar du på **Välj**, och klicka sedan på **tilldela** att lägga till användaren i appen.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](add-users-administrator.md)
- [B2B-samarbete inlösning av inbjudan](redemption-experience.md)
- [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
