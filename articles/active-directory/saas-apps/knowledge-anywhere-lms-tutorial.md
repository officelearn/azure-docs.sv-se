---
title: 'Självstudier: Azure Active Directory-integrering med Knowledge Anywhere LMS | Microsoft Docs'
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
ms.openlocfilehash: 34553c92f54992698a7e3930ac302ef970edd7c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236654"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Självstudier: Integrering av Knowledge var som helst LMS med Azure Active Directory

I de här självstudierna lär du dig att integrera LMS för Knowledge var som helst med Azure Active Directory (AD Azure). När du integrerar LMS för Knowledge var som helst med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till LMS för Knowledge var som helst.
* Ge dina användare att automatiskt inloggad till LMS för Knowledge var som helst med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Knowledge var som helst LMS enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Knowledge var som helst LMS **SP** initierad SSO och stöder **Just In Time** etableringen av användare.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Lägga till Knowledge Anywhere LMS från galleriet

När du konfigurerar integreringen av Knowledge Anywhere LMS i Azure Active Directory, måste du lägga till Knowledge Anywhere LMS från galleriet i din lista med hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Knowledge var som helst LMS** i sökrutan.
1. Välj **Knowledge var som helst LMS** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med kunskaper som helst LMS med en testanvändare kallas **B. Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i LMS för Knowledge var som helst.

Om du vill konfigurera och testa Azure AD enkel inloggning med kunskaper som helst LMS, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Knowledge var som helst LMS](#configure-knowledge-anywhere-lms)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B. Simon att använda Azure AD enkel inloggning.
5. **[Skapa LMS-Knowledge var som helst testanvändare](#create-knowledge-anywhere-lms-test-user)**  har en motsvarighet för B. Simon i Knowledge var som helst LMS som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Knowledge var som helst LMS** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    1. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren och svars-URL:en. Detta beskrivs senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Knowledge Anywhere LMS-klientens supportteam](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Knowledge var som helst LMS** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurera Knowledge var som helst LMS

1. Om du vill automatisera konfigurationen inom LMS för Knowledge var som helst, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Knowledge var som helst LMS** omdirigerar dig till programmet LMS-Knowledge var som helst. Ange administratörsautentiseringsuppgifter för att logga in på kunskap som helst LMS därifrån. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Knowledge var som helst LMS manuellt, öppna ett nytt webbläsarfönster och logga in på din LMS-Knowledge var som helst företagets webbplats som administratör och utför följande steg:

4. Välj fliken **Webbplats**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Välj på fliken **SAML-inställningar**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klicka på **Lägg till ny**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. På sidan **Lägg till/uppdatera SAML-inställningar** utför du följande steg:

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Ange IDP-namnet för din organisation. För exempelvis: `Azure`.

    b. I den **IDP entitets-ID** textrutan klistra in **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    c. I den **IDP URL** textrutan klistra in **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    d. Öppna den nedladdade certifikatfilen från Azure-portalen i Anteckningar, kopiera innehållet för certifikatet och klistra in det i textrutan **Certifikat**.

    e. I den **URL för utloggning** textrutan klistra in **URL för utloggning** värde, som du har kopierat från Azure-portalen.

    f. Välj **Huvudwebbplats** i listrutan för **Domän**.

    g. Kopiera värdet i **Entitets-ID för SP** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    h. Kopiera värdet för **SP-svar (ACS) URL** och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B. Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon att använda Azure enkel inloggning om du beviljar åtkomst till LMS för Knowledge var som helst.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer du **Knowledge Anywhere LMS**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B. Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Skapa testanvändare för Knowledge Anywhere LMS

I det här avsnittet skapas en användare som kallas B. Simon i LMS för Knowledge var som helst. Knowledge Anywhere LMS stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Knowledge Anywhere LMS, skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen LMS-Knowledge var som helst i åtkomstpanelen, bör det vara loggas in automatiskt till Knowledge var som helst LMS som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)