---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med TimeOffManager | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06aa2ddf3e7168147ec091ef6fb9826025f23364
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561821"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med TimeOffManager

I den här självstudien får du lära dig hur du integrerar TimeOffManager med Azure Active Directory (Azure AD). När du integrerar TimeOffManager med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TimeOffManager.
* Gör att användarna automatiskt loggas in på TimeOffManager med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* TimeOffManager enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.


* TimeOffManager stöder **IDP-initierad** SSO

* TimeOffManager stöder just in time-användaretablering **Just In Time**

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Lägga till TimeOffManager från galleriet

Om du vill konfigurera integreringen av TimeOffManager i Azure AD måste du lägga till TimeOffManager från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **TimeOffManager** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **TimeOffManager** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Konfigurera och testa en azure AD-inloggning för TimeOffManager

Konfigurera och testa Azure AD SSO med TimeOffManager med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i TimeOffManager.

Så här konfigurerar och testar du Azure AD SSO med TimeOffManager:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera TimeOffManager SSO](#configure-timeoffmanager-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa TimeOffManager-testanvändare](#create-timeoffmanager-test-user)** – om du vill ha en motsvarighet till B.Simon i TimeOffManager som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **TimeOffManager-programintegrering** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Du kan få det här värdet från **Single Sign on settings sida** som förklaras senare i handledningen eller Kontakta [TimeOffManager supportteam](https://www.purelyhr.com/contact-us). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. TimeOffManager-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovanstående förväntar sig TimeOffManager-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | --- | --- |
    | Förnamn |User.givenname |
    | Efternamn |Användare.efternamn |
    | E-post |User.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera TimeOffManager.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TimeOffManager.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **TimeOffManager**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-timeoffmanager-sso"></a>Konfigurera TimeOffManager SSO

1. I ett annat webbläsarfönster loggar du in på din TimeOffManager-företagswebbplats som administratör.

2. Gå till **kontokontoalternativ \> \> med enkel inloggning**.
   
    ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795917.png "Inställningar för enkel inloggning")

3. Gör följande i avsnittet **Inställningar för enkel inloggning:**
   
    ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795918.png "Inställningar för enkel inloggning")
   
    a. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det i Urklipp och klistra sedan in hela certifikatet i textrutan **X.509 Certifikat.**
   
    b. I **textrutan Idp Issuer** klistrar du in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
   
    c. Klistra in värdet **för inloggnings-URL** som du har kopierat från Azure-portalen i **IdP-url-url-textrutan.**
   
    d. Som **Framtvinga SAML**väljer du **Nej**.
   
    e. När **användare skapa automatiskt**väljer du **Ja**.
   
    f. I textrutan **Logout URL** klistrar du in värdet **för url för utloggning** som du har kopierat från Azure-portalen.
   
    g. Klicka på **Spara ändringar**.

4. På sidan **Inställningar för enkel inloggning** kopierar du värdet för **URL:en för kontroll av konsumenttjänst** och klistrar in det i textrutan **Svara url** under Avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. 

      ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795915.png "Inställningar för enkel inloggning")

### <a name="create-timeoffmanager-test-user"></a>Skapa TimeOffManager-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i TimeOffManager. TimeOffManager stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i TimeOffManager skapas en ny efter autentisering.

>[!NOTE]
>Du kan använda andra Verktyg eller API:er för att skapa ett annat TimeOffManager-användarkonto som tillhandahålls av TimeOffManager för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TimeOffManager på åtkomstpanelen ska du automatiskt loggas in på den TimeOffManager som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova TimeOffManager med Azure AD](https://aad.portal.azure.com/)

