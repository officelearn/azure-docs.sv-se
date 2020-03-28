---
title: 'Självstudiekurs: Azure Active Directory-integrering med Knowledge Anywhere LMS | Microsoft-dokument'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44324bbdd5af6675dfb4f5664cbbde2627edfec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098570"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Självstudiekurs: Integrera kunskap överallt LMS med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Knowledge Anywhere LMS med Azure Active Directory (Azure AD). När du integrerar Knowledge Anywhere LMS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Knowledge Anywhere LMS.
* Gör att användarna automatiskt loggas in på Knowledge Anywhere LMS med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Knowledge Anywhere LMS enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Knowledge Anywhere LMS stöder **SP** initierade SSO och stöder **just in** time-användaretablering.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Lägga till Knowledge Anywhere LMS från galleriet

När du konfigurerar integreringen av Knowledge Anywhere LMS i Azure Active Directory, måste du lägga till Knowledge Anywhere LMS från galleriet i din lista med hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Knowledge Anywhere LMS** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Kunskap överallt LMS** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Knowledge Anywhere LMS med en testanvändare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Knowledge Anywhere LMS.

Om du vill konfigurera och testa Azure AD SSO med Knowledge Anywhere LMS slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B. Simon för att använda Azure AD enkel inloggning.
5. **[Skapa kunskap överallt LMS-testanvändare](#create-knowledge-anywhere-lms-test-user)** för att ha en motsvarighet till B. Simon in Knowledge Anywhere LMS som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan **Kunskap överallt LMS-programintegration** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    1. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren och svars-URL:en. Detta beskrivs senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Knowledge Anywhere LMS-klientens supportteam](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera kunskap överallt.**

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurera kunskap var som helst LMS

1. Om du vill automatisera konfigurationen i Knowledge Anywhere LMS måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du på **Setup Knowledge Anywhere LMS** leder dig till Knowledge Anywhere LMS-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Knowledge Anywhere LMS. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Knowledge Anywhere LMS manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen Knowledge Anywhere LMS som administratör och utför följande steg:

4. Välj fliken **Webbplats**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Välj på fliken **SAML-inställningar**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klicka på **Lägg till ny**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. På sidan **Lägg till/uppdatera SAML-inställningar** utför du följande steg:

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Ange IDP-namnet för din organisation. För exempelvis: `Azure`.

    b. I **textrutan IDP-entitets-ID** klistrar du in **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I **textrutan URL i IDP** klistrar du in **url-värde** för inloggning, som du har kopierat från Azure-portalen.

    d. Öppna den nedladdade certifikatfilen från Azure-portalen i Anteckningar, kopiera innehållet för certifikatet och klistra in det i textrutan **Certifikat**.

    e. I textrutan **Utloggnings-URL:en** klistrar du in **URL-värdet för utloggning,** som du har kopierat från Azure-portalen.

    f. Välj **Huvudwebbplats** i listrutan för **Domän**.

    g. Kopiera värdet i **Entitets-ID för SP** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    h. Kopiera värdet för **SP-svar (ACS) URL** och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B. Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Knowledge Anywhere LMS.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I listan med program väljer du **Knowledge Anywhere LMS**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B. Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-knowledge-anywhere-lms-test-user"></a>Skapa testanvändare för Knowledge Anywhere LMS

I det här avsnittet skapas en användare som heter B. Simon i Knowledge Anywhere LMS. Knowledge Anywhere LMS stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Knowledge Anywhere LMS, skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Knowledge Anywhere LMS på åtkomstpanelen bör du automatiskt loggas in på den Knowledge Anywhere LMS som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)