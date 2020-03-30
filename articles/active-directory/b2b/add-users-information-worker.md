---
title: Lägga till B2B-samarbetsanvändare som informationsarbetare - Azure AD
description: B2B-samarbete gör det möjligt för informationsarbetare och appägare att lägga till gästanvändare i Azure AD för åtkomst | Microsoft-dokument
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565425"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Så här kan användare i organisationen bjuda in gästanvändare till en app

När en gästanvändare har lagts till i katalogen i Azure AD kan en programägare skicka gästanvändaren en direktlänk till appen som de vill dela. Azure AD-administratörer kan också konfigurera självbetjäningshantering för galleri- eller SAML-baserade appar i sin Azure AD-klientorganisation. På så sätt kan programägare hantera sina egna gästanvändare, även om gästanvändarna inte har lagts till i katalogen ännu. När en app är konfigurerad för självbetjäning använder programägaren sin åtkomstpanel för att bjuda in en gästanvändare till en app eller lägga till en gästanvändare i en grupp som har åtkomst till appen. Självbetjäning app hantering för galleri och SAML-baserade appar kräver vissa inledande installation av en administratör. Följande är en sammanfattning av installationsstegen (mer detaljerade instruktioner finns i [Förutsättningar](#prerequisites) senare på den här sidan):

 - Aktivera grupphantering med självbetjäning för din klientorganisation
 - Skapa en grupp som ska tilldelas appen och göra användaren till ägare
 - Konfigurera appen för självbetjäning och tilldela gruppen till appen

> [!NOTE]
> I den här artikeln beskrivs hur du konfigurerar självbetjäningshantering för galleri- och SAML-baserade appar som du har lagt till i din Azure AD-klientorganisation. Du kan också [konfigurera Office 365-grupper med självbetjäning](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) så att användarna kan hantera åtkomsten till sina egna Office 365-grupper. Fler sätt att dela Office-filer och -appar med gästanvändare finns [i Gäståtkomst i Office 365-grupper](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) och [Dela SharePoint-filer eller -mappar](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Bjuda in en gästanvändare till en app från åtkomstpanelen

När en app har konfigurerats för självbetjäning kan programägare använda sin egen åtkomstpanel för att bjuda in en gästanvändare till appen de vill dela. Gästanvändaren behöver inte nödvändigtvis läggas till i Azure AD i förväg. 

1. Öppna åtkomstpanelen genom `https://myapps.microsoft.com`att gå till .
2. Peka på appen, välj ellipserna (**...**) och välj sedan **Hantera app**.
 
   ![Skärmbild som visar undermenyn Hantera appen för Salesforce-appen](media/add-users-iw/access-panel-manage-app.png)
 
3. Högst upp i användarlistan **+** väljer du .
   
   ![Skärmbild som visar plussymbolen för att lägga till medlemmar i appen](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Skriv e-postadressen för gästanvändaren i sökrutan **Lägg till medlemmar.** Du kan också lägga till ett välkomstmeddelande.
   
   ![Skärmbild som visar fönstret Lägg till medlemmar för att lägga till en gäst](media/add-users-iw/access-panel-invitation.png)
   
5. Välj **Lägg till** om du vill skicka en inbjudan till gästanvändaren. När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Bjuda in någon att gå med i en grupp som har åtkomst till appen
När en app har konfigurerats för självbetjäning kan programägare bjuda in gästanvändare till de grupper de hanterar som har åtkomst till de appar de vill dela. Gästanvändarna behöver inte redan finnas i katalogen. Programägaren följer dessa steg för att bjuda in en gästanvändare till gruppen så att de kan komma åt appen.

1. Kontrollera att du äger den självbetjäningsgrupp som har åtkomst till appen som du vill dela.
2. Öppna åtkomstpanelen genom `https://myapps.microsoft.com`att gå till .
3. Välj appen **Grupper.**
   
   ![Skärmbild som visar appen Grupper på åtkomstpanelen](media/add-users-iw/access-panel-groups.png)
   
4. Under **Grupper som jag äger**väljer du den grupp som har åtkomst till appen som du vill dela.
   
   ![Skärmbild som visar var du vill välja en grupp under de grupper jag äger](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Högst upp i gruppmedlemslistan **+** väljer du .
   
   ![Skärmbild som visar plussymbolen för att lägga till medlemmar i gruppen](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Skriv e-postadressen för gästanvändaren i sökrutan **Lägg till medlemmar.** Du kan också lägga till ett välkomstmeddelande.
   
   ![Skärmbild som visar fönstret Lägg till medlemmar för att lägga till en gäst](media/add-users-iw/access-panel-invitation.png)
   
7. Välj **Lägg till** om du vill skicka inbjudan automatiskt till gästanvändaren. När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.


## <a name="prerequisites"></a>Krav

Självbetjäning apphantering kräver några inledande installation av en global administratör och en Azure AD-administratör. Som en del av den här konfigurationen konfigurerar du appen för självbetjäning och tilldelar en grupp till appen som programägaren kan hantera. Du kan också konfigurera gruppen så att vem som helst kan begära medlemskap men kräva godkännande av en gruppägare. (Läs mer om [grupphantering med självbetjäning](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Du kan inte lägga till gästanvändare i en dynamisk grupp eller i en grupp som synkroniseras med lokal Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Aktivera grupphantering med självbetjäning för din klientorganisation
1. Logga in på [Azure-portalen](https://portal.azure.com) som global administratör.
2. Välj Azure Active **Directory**på navigeringspanelen .
3. Välj **grupper**.
4. Under **Inställningar**väljer du **Allmänt**.
5. Under **Självbetjäningsgrupphantering**väljer du **Ja**bredvid Ägare som kan **hantera gruppmedlemskapsförfrågningar på åtkomstpanelen**.
6. Välj **Spara**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Skapa en grupp som ska tilldelas appen och göra användaren till ägare
1. Logga in på [Azure-portalen](https://portal.azure.com) som Azure AD-administratör eller global administratör.
2. Välj Azure Active **Directory**på navigeringspanelen .
3. Välj **grupper**.
4. Välj **Ny grupp**.
5. Under **Grupptyp**väljer du **Säkerhet**.
6. Ange ett **gruppnamn** och en **gruppbeskrivning**.
7. Under **Medlemstyp**väljer du **Tilldelad**.
8. Välj **Skapa**och stäng **sidan Gruppera.**
9. Öppna gruppen på sidan **Grupper - alla grupper.** 
10. Under **Hantera**väljer du **Ägare** > **Lägg till ägare**. Sök efter den användare som ska hantera åtkomsten till programmet. Markera användaren och klicka sedan på **Markera**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurera appen för självbetjäning och tilldela gruppen till appen
1. Logga in på [Azure-portalen](https://portal.azure.com) som Azure AD-administratör eller global administratör.
2. Välj Azure Active **Directory**i navigeringsfönstret .
3. Under **Hantera**väljer du **Företagsprogram** > **Alla program**.
4. Leta reda på och öppna appen i programlistan.
5. Under **Hantera**väljer du **Enkel inloggning**och konfigurerar programmet för enkel inloggning. (Mer information finns i [hur du hanterar enkel inloggning för företagsappar](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).)
6. Under **Hantera**väljer du **Självbetjäning**och konfigurerar appåtkomst för självbetjäning. (Mer information finns i [hur du använder självbetjäningsappåtkomst](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Välj den grupp som du skapade i föregående avsnitt för inställningen **Till vilken grupp som ska tilldelas användare?**
7. Under **Hantera**väljer du **Användare och grupper**och kontrollerar att självbetjäningsgruppen som du skapade visas i listan.
8. Om du vill lägga till appen på gruppägarens åtkomstpanel väljer du **Lägg till användare** > **användare och grupper**. Sök efter gruppägaren och välj användaren, klicka på **Välj**och klicka sedan på **Tilldela** om du vill lägga till användaren i appen.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Hur lägger Azure Active Directory-administratörer till B2B-samarbetsanvändare?](add-users-administrator.md)
- [B2B samarbete inbjudan inlösen](redemption-experience.md)
- [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
