---
title: 'Självstudier: Azure Active Directory-integrering med RingCentral | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: 2e4f45deb8c63640a328e38cebc2ecbe67233c3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143143"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Självstudier: Integrera RingCentral med Azure Active Directory

I de här självstudierna lär du dig att integrera RingCentral med Azure Active Directory (AD Azure). När du integrerar RingCentral med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till RingCentral.
* Ge dina användare att automatiskt inloggad till RingCentral med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration RingCentral enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för RingCentral **IDP** -initierad SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Att lägga till RingCentral från galleriet

För att konfigurera integrering av RingCentral i Azure AD, som du behöver lägga till RingCentral från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **RingCentral** i sökrutan.
1. Välj **RingCentral** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med RingCentral med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i RingCentral för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med RingCentral, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera RingCentral SSO](#configure-ringcentral-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa RingCentral testanvändare](#create-ringcentral-test-user)**  – du har en motsvarighet för Britta Simon i RingCentral som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **RingCentral** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    1. Klicka på **ladda upp metadatafilen**.
    1. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.
    1. När metadatafilen har överförts den **identifierare** och **svars-URL** värden får automatiskt ifylld i **SAML grundkonfiguration** avsnittet.

    > [!Note]
    > Du får den **tjänstleverantör metadatafil** på sidan RingCentral SSO-konfiguration som beskrivs senare i självstudien.

1. Om du inte har **tjänstleverantör metadatafil**, anger du värden för följande fält:

    a. I den **identifierare** textrutan anger du ett URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. I textrutan **Svars-URL** anger du en URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Konfigurera RingCentral SSO

1. I ett annat webbläsarfönster, loggar du in RingCentral som en administratör.

1. På överst, klickar du på **verktyg**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Gå till **enkel inloggning**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. På den **enkel inloggning** sidan under **SSO-konfiguration** avsnittet från **steg 1** klickar du på **redigera** och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. På den **Konfigurera enkel inloggning** utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klicka på **Bläddra** att ladda upp metadatafilen som du har hämtat från Azure-portalen.

    b. När du har överfört metadata hämta värdena fylls i **SSO allmän Information om** avsnittet.

    c. Under **attributmappning** väljer **kartan e-attributet till** som `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klicka på **Spara**.

    e. Från **steg 2** klickar du på **hämta** att ladda ned den **tjänstleverantör metadatafil** och ladda upp den i **SAML grundkonfiguration** avsnittet att automatiskt fylla i den **identifierare** och **svars-URL** värdena i Azure-portalen.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. På samma sida, navigerar du till **aktivera SSO** avsnittet och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Välj **aktivera SSO-tjänsten**.

    * Välj **Tillåt användare att logga in med autentiseringsuppgifter för enkel inloggning eller RingCentral**.

    * Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RingCentral.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **RingCentral**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ringcentral-test-user"></a>Skapa RingCentral testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i RingCentral. Arbeta med [RingCentral klienten supportteamet](https://success.ringcentral.com/RCContactSupp) att lägga till användare i RingCentral-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen RingCentral i åtkomstpanelen, bör det vara loggas in automatiskt till RingCentral som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
