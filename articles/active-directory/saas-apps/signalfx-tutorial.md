---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SignalFx | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149718dcd325ef6bd6a6754ba100ffdc34be0a07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136422"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SignalFx

I den här självstudien får du lära dig hur du integrerar SignalFx med Azure Active Directory (Azure AD). När du integrerar SignalFx med Azure AD kan du:

* Kontroll från Azure AD som har åtkomst till SignalFx;
* Gör det möjligt för användarna att automatiskt loggas in på SignalFx med sina Azure AD-konton. Och
* Hantera dina konton på en plats (Azure-portalen).

Mer information om SaaS-programintegrering med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

Innan du börjar behöver du:

* En Azure AD-prenumeration
    * Om du inte har en prenumeration kan du få ett [gratis konto här](https://azure.microsoft.com/free/).
* SignalFx enkel inloggning (SSO) aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SignalFx stöder **IDP** initierad SSO
* SignalFx stöder just in time-användaretablering **Just In Time**
* När du har konfigurerat SignalFx kan du framtvinga sessionskontroll, vilket skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Steg 1: Lägg till SignalFx-programmet i Azure

Använd dessa instruktioner för att lägga till SignalFx-programmet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **Azure Active Directory**i det vänstra navigeringsfönstret .
1. Välj **Företagsprogram**och välj sedan **Alla program**.
1. Välj **Nytt program**.
1. I avsnittet **Lägg till i galleriet** anger du i sökrutan och väljer **SignalFx**.
     * Du kan behöva vänta några minuter för att programmet ska läggas till din klient.
1. Lämna Azure-portalen öppen och öppna sedan en ny webbflik.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Steg 2: Börja SignalFx SSO-konfiguration

Använd dessa instruktioner för att påbörja konfigurationsprocessen för SignalFx SSO.

1. På den nyöppnade fliken kommer du åt och loggar in på SignalFx-användargränssnittet. 
1. Klicka på **Integrationer**på den övre menyn. 
1. Ange och välj Azure **Active Directory**i sökfältet .
1. Klicka på **Skapa ny integrering**.
1. I **Namn**anger du ett lättkännligt namn som användarna kommer att förstå.
1. Markera **Visa på inloggningssidan**.
    * Den här funktionen visar en anpassad knapp på inloggningssidan som användarna kan klicka på. 
    * Informationen du angav i **Namn** visas på knappen. Därför anger du ett **namn** som användarna känner igen. 
    * Det här alternativet fungerar bara om du använder en anpassad underdomän för Programmet SignalFx, till exempel **yourcompanyname.signalfx.com**. Kontakta SignalFx-supporten om du vill hämta en anpassad underdomän. 
1. Kopiera **integrations-ID.** Du behöver den här informationen i ett senare steg. 
1. Låt SignalFx-användargränssnittet vara öppet. 

## <a name="step-3-configure-azure-ad-sso"></a>Steg 3: Konfigurera Azure AD SSO

Använd dessa instruktioner för att aktivera Azure AD SSO i Azure-portalen.

1. Gå tillbaka till [Azure-portalen](https://portal.azure.com/)och leta reda på avsnittet **Hantera** på sidan **SignalFx-program** och välj sedan **Enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen penna (redigera) för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Fyll i följande fält på sidan **Konfigurera enkel inloggning med SAML:** 

    a. I **Identifierare**anger du `https://api.<realm>.signalfx.com/v1/saml/metadata` följande `<realm>` URL och ersätter med din SignalFx-sfär. 

    b. I **Svars-URL**anger `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` du `<realm>` följande URL och ersätter `<integration ID>` med din SignalFx-sfär, samt med **det integrations-ID** som du kopierade tidigare från SignalFx-användargränssnittet.

1. SignalFx-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. 
    
1. Granska och kontrollera att följande anspråk mappas till källattributen som fylls i i Active Directory. 

    | Namn |  Källattribut|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | Användare.e-post  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Den här processen kräver att Active Directory är konfigurerat med minst en verifierad anpassad domän, samt har åtkomst till e-postkonton i den här domänen. Om du är osäker eller behöver hjälp med den här konfigurationen kontaktar du SignalFx support.  

1. Leta reda på **certifikat (Base64)** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** och välj sedan **Hämta**. Hämta certifikatet och spara det på datorn. Kopiera sedan **url-värdet för App Federation Metadata.** du behöver denna information i ett senare steg i SignalFx UI. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera värdet för **Azure AD Identifier** i avsnittet **Konfigurera SignalFx.** Du behöver den här informationen i ett senare steg i SignalFx-användargränssnittet. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Steg 4: Skapa en Azure AD-testanvändare

Använd dessa instruktioner för att skapa en testanvändare i Azure-portalen som heter **B.Simon**.

1. I navigeringsfönstret på vänster sida i Azure-portalen väljer du **Azure Active Directory**och väljer sedan **Användare**och väljer sedan **Alla användare**.
1. Högst upp på sidan väljer du **Ny användare**.
1. I **användaregenskaperna:**
   1. I **Användarnamn**anger `username@companydomain.extension`du `b.simon@contoso.com`, till exempel .
   1. Skriv **Name**i `B.Simon`Namn .
   1. Markera **Visa lösenord**och kopiera sedan det visade värdet i **Lösenord**. Du behöver den här informationen i senare steg för att testa den här integrationen. 
   1. Klicka på **Skapa**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Steg 5: Tilldela Azure AD-testanvändaren

Använd dessa instruktioner för att göra det möjligt för testanvändaren att använda Azure single sign-on för SignalFx.

1. I Azure-portalen väljer du **Enterprise-program**och väljer sedan **Alla program**.
1. Välj **SignalFx**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer sedan **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. Välj **B.Simon**i dialogrutan **Användare** **och grupper** och klicka sedan på **Välj**längst ned på sidan.
1. Om du förväntar dig något rollvärde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på **Välj** längst ned på sidan.
1. Klicka på fliken **Tilldela**i dialogrutan **Lägg till tilldelning.**

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Steg 6: Slutför SignalFx SSO-konfigurationen 

1. Öppna föregående flik och gå tillbaka till SignalFx-användargränssnittet för att visa den aktuella Azure Active Directory-integrationssidan. 
1. Bredvid **Certifikat (Base64)** klickar du på **Ladda upp fil**och letar sedan reda på den **Base64-kodade certifikatfilen** som du tidigare har hämtat från Azure-portalen.
1. Klistra in **azure AD-identifierare** som du kopierade tidigare från Azure **AD-identifierare**och klistra in det azure AD-identifierare som du kopierade tidigare från Azure-portalen. 
1. Klistra in **url-värdet för appfederationsmetadata** som du kopierade tidigare från Azure-portalen bredvid URL för **federationsmetadata.** 
1. Klicka på **Spara**.

## <a name="step-7-test-sso"></a>Steg 7: Testa SSO

Granska följande information om hur du testar SSO, samt förväntningar på att logga in på SignalFx för första gången. 

### <a name="test-logins"></a>Testa inloggningar

* För att testa inloggningen bör du använda ett privat / inkognitofönster, eller så kan du logga ut från Azure-portalen. Om inte, cookies för användaren som konfigurerat programmet kommer att störa och förhindra en lyckad inloggning med testanvändaren.

* När en ny testanvändare loggar in för första gången tvingar Azure fram en lösenordsändring. När detta inträffar kommer SSO-inloggningsprocessen inte att slutföras. testanvändaren dirigeras till Azure-portalen. Om du vill felsöka bör testanvändaren ändra sitt lösenord och navigera till SignalFx-inloggningssidan eller till Åtkomstpanelen och försöka igen.
    * När du klickar på Panelen SignalFx på åtkomstpanelen bör du automatiskt loggas in i SignalFx. 
        * Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

* SignalFx ansökan kan nås från åtkomstpanelen eller via en anpassad inloggningssida som tilldelats organisationen. Testanvändaren bör testa integrationen från och med någon av dessa platser.
    * Testanvändaren kan använda de autentiseringsuppgifter **b.simon@contoso.com**som skapats tidigare i den här processen för .

### <a name="first-time-logins"></a>Första gången inloggningar

* När en användare loggar in på SignalFx från SAML SSO för första gången får användaren ett SignalFx-e-postmeddelande med en länk. Användaren måste klicka på länken för autentisering. Den här e-postvalideringen sker endast för förstagångsanvändare. 

* SignalFx stöder **just in time** användarskapande, vilket innebär att om en användare inte finns i SignalFx, då användarens konto kommer att skapas vid första inloggningsförsöket.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Prova SignalFx med Azure AD](https://aad.portal.azure.com/)