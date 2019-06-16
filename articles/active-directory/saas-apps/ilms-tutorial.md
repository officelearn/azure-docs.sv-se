---
title: 'Självstudier: Azure Active Directory-integrering med iLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2e012a4ce8ac4a9a5afb895d545beb0a0b8946
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100624"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Självstudier: Integrera iLMS med Azure Active Directory

I de här självstudierna lär du dig att integrera iLMS med Azure Active Directory (AD Azure). När du integrerar iLMS med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till iLMS.
* Ge dina användare att automatiskt inloggad till iLMS med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* aktiverat prenumeration iLMS enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. har stöd för iLMS **SP och IDP** -initierad SSO

## <a name="adding-ilms-from-the-gallery"></a>Att lägga till iLMS från galleriet

För att konfigurera integrering av iLMS i Azure AD, som du behöver lägga till iLMS från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **iLMS** i sökrutan.
1. Välj **iLMS** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med iLMS med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i iLMS för SSO ska fungera.

Om du vill konfigurera och testa Azure AD SSO med iLMS, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera iLMS SSO](#configure-ilms-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare iLMS](#create-ilms-test-user)**  – du har en motsvarighet för Britta Simon i iLMS som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **iLMS** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, anger du värden för följande fält:

    a. I den **identifierare** textrutan, klistra in den **identifierare** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i administrationsportalen för iLMS.

    b. I den **svars-URL** textrutan, klistra in den **slutpunkt (URL)** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i iLMS admin-portalen med följande mönster `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I den **inloggnings-URL** textrutan, klistra in den **slutpunkt (URL)** värde som du kopierar från **tjänstleverantör** avsnittet för SAML-inställningar i administrationsportalen för iLMS som `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Om du vill aktivera JIT etablering förväntar programmets iLMS SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    > [!NOTE]
    > Du måste aktivera **skapa Un-recognized användarkonto** i iLMS att mappa dessa attribut. Följ instruktionerna [här](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) att få en uppfattning på konfigurationen av attribut.

1. Förutom ovanstående iLMS program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | --------|------------- |
    | division | user.department |
    | region | User.state |
    | Avdelning | user.jobtitle |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **konfigurera iLMS** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-ilms-sso"></a>Konfigurera iLMS SSO

1. I ett annat webbläsarfönster, loggar du in din **iLMS administrationsportalen** som administratör.

2. Klicka på **SSO:SAML** under **inställningar** flik för att öppna SAML-inställningar och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/1.png)

3. Expandera den **tjänstleverantör** avsnitt och kopiera den **identifierare** och **slutpunkt (URL)** värde.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/2.png) 

4. Under **identitetsprovidern** klickar du på **importera Metadata**.

5. Välj den **Federationsmetadata** har laddat ned från Azure portal från den **SAML-signeringscertifikat** avsnittet.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Om du vill aktivera JIT etablering för att skapa iLMS konton för un-identifiera användarna, följer du dessa steg:

    a. Kontrollera **skapa icke erkända användarkonto**.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mappa attribut i Azure AD med attribut i iLMS. Ange namnet på attribut eller standardvärdet i attributkolumnen.

    c. Gå till **affärsregler** fliken och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/5.png)

    d. Kontrollera **skapa Un-recognized regioner, avdelningar och avdelningar** skapa regioner, avdelningar och avdelningar som inte redan finns vid tidpunkten för enkel inloggning.

    e. Kontrollera **uppdatering användaren profilen under inloggning** att ange om användarens profil uppdateras med varje enkel inloggning.

    f. Om den **uppdatering tomma värden för icke obligatoriska fält i användarprofilen** alternativet är markerat, valfritt profil fält som är tom vid inloggningen kommer även orsaka användarens iLMS profil som innehåller tomma värden för dessa fält.

    g. Kontrollera **skicka fel e-postmeddelande** och ange e-postadress för användaren där du vill ta emot fel e-postmeddelandet.

7. Klicka på knappen **Spara** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/save.png)

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iLMS.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **iLMS**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ilms-test-user"></a>Skapa iLMS testanvändare

Programmet stöder bara i tid användaretablering och -autentiserade användare skapas automatiskt i programmet. JIT fungerar om du har klickat på den **skapa Un-recognized användarkonto** kryssrutan under SAML konfigurationsinställning i administrationsportalen för iLMS.

Följ stegen nedan om du vill skapa en användare manuellt:

1. Logga in på webbplatsen iLMS företag som administratör.

2. Klicka på **registrera användare** under **användare** fliken för att öppna **registrera användaren** sidan.

   ![Lägga till medarbetare](./media/ilms-tutorial/3.png)

3. På den **registrera användaren** utför följande steg.

    ![Lägga till medarbetare](./media/ilms-tutorial/create_testuser_add.png)

    a. I den **Förnamn** textrutan typnamn först som Britta.

    b. I den **efternamn** textrutan anger efternamn som Simon.

    c. I den **e-post-ID** textrutan typ e-postadressen för användaren som BrittaSimon@contoso.com.

    d. I den **Region** listrutan Välj värdet för regionen.

    e. I den **Division** listrutan Välj värdet för av.

    f. I den **avdelning** listrutan Välj värdet för avdelning.

    g. Klicka på **Spara**.

    > [!NOTE]
    > Du kan skicka e-post för registrering till användaren genom att välja **skicka e-post från registrering** kryssrutan.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen iLMS i åtkomstpanelen, bör det vara loggas in automatiskt till iLMS som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
