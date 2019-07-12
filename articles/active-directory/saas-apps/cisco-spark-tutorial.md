---
title: 'Självstudier: Azure Active Directory-integrering med Cisco Webex | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bf8b4d696a3784988a92fc24de1ae6964786e07
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836389"
---
# <a name="tutorial-integrate-cisco-webex-with-azure-active-directory"></a>Självstudier: Integrera Cisco Webex med Azure Active Directory

I de här självstudierna lär du dig att integrera Cisco Webex med Azure Active Directory (AD Azure). När du integrerar Cisco Webex med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Cisco Webex.
* Ge dina användare att automatiskt inloggad till Cisco Webex med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Cisco Webex enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Cisco Webex **SP och IDP** initierad SSO och stöder **automatisk** etableringen av användare.

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

För att konfigurera integrering av Cisco Webex i Azure AD måste du lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Cisco Webex** i sökrutan.
1. Välj **Cisco Webex** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Cisco Webex med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Cisco Webex för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco Webex, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Cisco Webex](#configure-cisco-webex)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa Cisco Webex testanvändare](#create-cisco-webex-test-user)**  har en motsvarighet för B.Simon i Cisco Webex som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Cisco Webex** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    >[!Note]
    >Du får Service Provider metadatafilen från den **Cisco Webex** Portal. 

    a. Klicka på **ladda upp metadatafilen**.

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    I den **inloggnings-URL** textrutan klistra in värdet för **svars-URL**, som hämtar autofilled SP av metadata filuppladdningen.

5. Cisco Webex-programmet förväntar sig att hitta SAML-försäkringar i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattributen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att lägga till attributen.

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig Cisco Webex-programmet att några fler attribut skickas tillbaka i SAML-svaret. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Namn |  Källattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **konfigurera Cisco Webex** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-cisco-webex"></a>Konfigurera Cisco Webex

1. Logga in i [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Välj **Settings** (Inställningar) och klicka på **Modify** (Ändra) under **Authentication** (Autentisering).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
3. Välj **Integrate a 3rd-party identity provider. (Avancerat)** (Integrera en tredjeparts identitetsprovider. (Avancerat)) och gå till nästa skärm.

4. På sidan **Import Idp Metadata** (Importera ldp-metadata) kan du antingen dra och släppa Azure AD-metadatafilen på sidan eller använda filbläddraren för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare) och klicka på **Next** (Nästa).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

5. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

6. Gå tillbaka till webbläsarfliken **Cisco Cloud Collaboration Management** (Ciscos samarbetshantering i molnet). Om testet lyckades väljer du **This test was successful. Enable Single Sign-On option** (Testet lyckades. Aktivera alternativet för enkel inloggning) och klickar på **Next** (Nästa).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cisco Webex.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. Välj **Cisco Webex** i listan över program.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cisco-webex-test-user"></a>Skapa en testanvändare i Cisco Webex

I det här avsnittet skapar du en användare med namnet Britta Simon i Cisco Webex. I det här avsnittet skapar du en användare med namnet Britta Simon i Cisco Webex.

1. Logga in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Klicka på **Users** (Användare) och sedan på **Manage Users** (Hantera användare).
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. I rutan **Manage Users** (Hantera användare) väljer du **Manually add or modify users** (Ändra eller lägga till användare manuellt) och klickar på **Next** (Nästa).

4. Välj alternativet **Name and Email address** (Namn och e-postadress). Fyll sedan i textrutan så här:

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. I den **Förnamn** textrutan Ange först namnet på användaren som **B**.

    b. I textrutan för **efternamn** skriver du efternamnet: **Simon**.

    c. I den **e-postadress** textrutan typ användarens e-postadress som b.simon@contoso.com.

5. Klicka på plustecknet för att lägga till Britta Simon. Klicka sedan på **Nästa**.

6. I rutan **Add Services for Users** (Lägg till tjänster för användare) klickar du på **Save** (Spara) och sedan på **Finish** (Slutför).

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Cisco Webex i åtkomstpanelen, bör det vara loggas in automatiskt till Cisco-Webex som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)