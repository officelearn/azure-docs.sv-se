---
title: 'Självstudier: Azure Active Directory-integrering med Asana | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: 1c4245dbb34fc7e4b3b2722e4a94bffaf4d7f66d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589917"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Självstudier: Azure Active Directory-integrering med Asana

I kursen får lära du att integrera Asana med Azure Active Directory (AD Azure).

Integrera Asana med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Asana
- Du kan aktivera användarna att automatiskt hämta loggat in på Asana (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Asana, behöver du följande:

- En Azure AD-prenumeration
- En Asana enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Asana från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-asana-from-the-gallery"></a>Att lägga till Asana från galleriet
Du måste lägga till Asana från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Asana i Azure AD.

**Utför följande steg för att lägga till Asana från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Asana**väljer **Asana** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Asana baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Asana motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Asana upprättas.

I Asana, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Asana, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Asana](#create-an-asana-test-user)**  – du har en motsvarighet för Britta Simon i Asana som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Asana program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Asana:**

1. I Azure-portalen på den **Asana** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. På den **Asana domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Asana domän med enkel inloggning information](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. I den **inloggnings-URL** textruta typen URL: `https://app.asana.com/`

    b. I den **identifierare** textruta TYPVÄRDE: `https://app.asana.com/`

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. På den **Asana Configuration** klickar du på **konfigurera Asana** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Asana konfiguration](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png)

7. I ett annat webbläsarfönster inloggning i tillämpningsprogrammet Asana. Om du vill konfigurera enkel inloggning i Asana att komma åt arbetsytan inställningar genom att klicka på namnet på arbetsytan i det övre högra hörnet på skärmen. Klicka på  **\<arbetsytans namn\> inställningar**.

    ![Asana sso-inställningar](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. På den **organisationsinställningar** -fönstret klickar du på **Administration**. Klicka på **medlemmar måste logga in via SAML** att aktivera SSO-konfigurationen. Utför följande steg:

    ![Konfigurera inställningar för enkel inloggning organisation](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. I den **inloggning Sidadress** textruta klistra in den **SAML inloggning tjänst-URL för enkel**.

     b. Högerklicka på certifikatet hämtas från Azure-portalen och sedan öppna certifikatfilen i anteckningar eller din önskade textredigerare. Kopiera innehåll mellan start- och slut-certifikat och klistra in den i den **X.509-certifikat** textruta.

9. Klicka på **Spara**. Gå till [Asana guide för att konfigurera enkel inloggning](https://asana.com/guide/help/premium/authentication#gl-saml) om du behöver ytterligare hjälp.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png)

4. På den **användaren** dialogrutan utför följande steg:

    ![Knappen Lägg till](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png)

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="create-an-asana-test-user"></a>Skapa en testanvändare Asana

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Asana. Asana stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](active-directory-saas-asana-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt gör du följande:**

I det här avsnittet skapar du en användare som kallas Britta Simon i Asana.

1. På **Asana**, gå till den **team** avsnitt i den vänstra panelen. Klicka på plustecknet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png)

2. Skriv e-postmeddelandet britta.simon@contoso.com i textrutan och välj sedan **bjuda in**.

3. Klicka på **skicka inbjudan**. Den nya användaren får ett e-postmeddelande till sin e-postkonto. Hon behöver du skapa och verifiera kontot.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Asana.

![Tilldela rollen][200]

**Om du vill tilldela Asana Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **Asana**.

    ![Länken Asana i listan med program](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png)

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa din Azure AD enkel inloggning.

Gå till sidan för Asana inloggning. Infoga den e-postadressen i textrutan e-postadress britta.simon@contoso.com. Lämna textrutan lösenord i tom och klickar sedan på **loggar In**. Du omdirigeras till inloggningssidan för Azure AD. Slutför Azure AD-autentiseringsuppgifterna. Nu kan är du inloggad Asana.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](active-directory-saas-asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png