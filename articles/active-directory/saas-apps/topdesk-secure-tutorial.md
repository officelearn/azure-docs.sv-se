---
title: 'Självstudier: Azure Active Directory-integration med TOPdesk – säker | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk – säker.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 8529dfda5ee4a7fc3360f91163b7f5f5bbf6c6ff
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054945"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Självstudier: Azure Active Directory-integration med TOPdesk – säker

I den här självstudien får du lära dig hur du integrerar TOPdesk - skydda med Azure Active Directory (AD Azure).

Integrera TOPdesk - säkert med Azure AD ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TOPdesk – säker.
- Du kan aktivera användarna att automatiskt få loggat in på TOPdesk – säker (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TOPdesk – säker, behöver du följande objekt:

- En Azure AD-prenumeration
- En TOPdesk – säker enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till TOPdesk - säkert från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-topdesk---secure-from-the-gallery"></a>Att lägga till TOPdesk - säkert från galleriet

Konfigurera integreringen av TOPdesk - skydda till Azure AD måste du lägga till TOPdesk: skydda från galleriet i listan över hanterade SaaS-appar.

**För att lägga till TOPdesk - säkert från galleriet, utför följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **TOPdesk – säker**väljer **TOPdesk – säker** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![TOPdesk - skydda i resultatlistan](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TOPdesk – säker baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TOPdesk – säker är att en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TOPdesk – säker upprättas.

I TOPdesk – säker, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TOPdesk – säker, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en TOPdesk – säker testanvändare](#create-a-topdesk---secure-test-user)**  – du har en motsvarighet för Britta Simon i TOPdesk – säker som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktiverar Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din TOPdesk – säker.

**Om du vill konfigurera Azure AD enkel inloggning med TOPdesk – säker, utför följande steg:**

1. I Azure-portalen på den **TOPdesk – säker** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. På den **TOPdesk - skydda domän och URL: er** avsnittet, utför följande steg:

    ![TOPdesk – säker domän och URL: er med enkel inloggning för information](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Svars-URL förklaras senare i självstudien. Kontakta [TOPdesk - skydda klienten supportteamet](http://www.topdesk.com/us/support) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/topdesk-secure-tutorial/tutorial_general_400.png)

6. På den **TOPdesk - skydda Configuration** klickar du på **konfigurera TOPdesk – säker** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![TOPdesk - konfigureringen av säker](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)

7. Logga in på din **TOPdesk – säker** företagets plats som administratör.

8. I den **TOPdesk** -menyn klickar du på **inställningar**.

    ![Inställningar för](./media/topdesk-secure-tutorial/ic790598.png "inställningar")

9. Klicka på **inloggningsinställningar**.

    ![Inloggningsinställningar](./media/topdesk-secure-tutorial/ic790599.png "inloggningsinställningar")

10. Expandera den **inloggningsinställningar** menyn och klicka sedan på **Allmänt**.

    ![Allmän](./media/topdesk-secure-tutorial/ic790600.png "Allmänt")

11. I den **Secure** delen av den **SAML-inloggningen** konfiguration och utför följande steg:

    ![Tekniska inställningar](./media/topdesk-secure-tutorial/ic790855.png "tekniska inställningar")

    a. Klicka på **hämta** att hämta offentliga metadatafilen och spara den lokalt på datorn.

    b. Öppna metadatafilen och välj sedan den **AssertionConsumerService** noden.

    ![Konsumenttjänst för försäkran](./media/topdesk-secure-tutorial/ic790856.png "Konsumenttjänst för försäkran")

    c. Kopiera den **AssertionConsumerService** värde, klistra in det här värdet i svars-URL-textrutan i **TOPdesk - skydda domän och URL: er** avsnittet.

12. Utför följande steg för att skapa en certifikatfil:

    ![Certifikatet](./media/topdesk-secure-tutorial/ic790606.png "certifikat")

    a. Öppna metadatafilen hämtade från Azure-portalen.

    b. Expandera den **RoleDescriptor** nod som har en **xsi: type** av **aggregeringsdesignprocessen: ApplicationServiceType**.

    c. Kopiera värdet för den **X509Certificate** noden.

    d. Spara den kopierade **X509Certificate** värdet lokalt på datorn i en fil.

13. I den **offentliga** klickar du på **Lägg till**.

    ![Lägg till](./media/topdesk-secure-tutorial/ic790607.png "Lägg till")

14. På den **SAML configuration assistent** dialogrutan utför följande steg:

    ![SAML-konfiguration assistent](./media/topdesk-secure-tutorial/ic790608.png "assistent för SAML-konfiguration")

    a. Ladda upp din hämtade metadatafilen från Azure-portalen under **Federationsmetadata**, klickar du på **Bläddra**.

    b. Ladda upp din certifikatfil under **certifikat (RSA)**, klickar du på **Bläddra**.

    c. För **privat nyckel (RSA, PKCS8, DER)**, du kan ladda upp en egen privata nyckeln eller kontakta [TOPdesk - skydda klienten supportteamet](http://www.topdesk.com/us/support) att hämta den privata nyckeln.

    d. Ladda upp logotypfilen som du fick från supporten TOPdesk under **logotyp ikonen**, klickar du på **Bläddra**.

    e. I den **användarnamnsattributet** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. I den **visningsnamn** textrutan anger du ett namn för din konfiguration.

    g. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/topdesk-secure-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/topdesk-secure-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/topdesk-secure-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/topdesk-secure-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-topdesk---secure-test-user"></a>Skapa en TOPdesk – säker testanvändare

För att aktivera Azure AD-användare att logga in på TOPdesk – säker och de måste etableras i TOPdesk – säker.  
När det gäller TOPdesk – säker, etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:

1. Logga in på din **TOPdesk – säker** företagets plats som administratör.

2. Klicka på menyn längst upp **TOPdesk \> New \> stödfiler \> operatorn**.

    ![Operatorn](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. På den **New-operatorn** dialogrutan utför följande steg:

    ![New-operatorn](./media/topdesk-secure-tutorial/ic790611.png "New-operatorn")

    a. Klicka på den **Allmänt** fliken.

    b. I den **efternamn** textrutan typ efternamn för användaren som **Simon**.

    c. Välj en **plats** för kontot i den **plats** avsnittet.

    d. I den **inloggningsnamn** textrutan för den **TOPdesk inloggning** Skriv ett inloggningsnamn för användaren.

    e. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra TOPdesk - verktyg för att skapa säkra användaren-konto eller API: er som tillhandahålls av TOPdesk - säkert att tillhandahålla AAD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TOPdesk – säker.

![Tilldela rollen][200] 

**Om du vill tilldela TOPdesk - Britta Simon säker, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **TOPdesk – säker**.

    ![TOPdesk - säker anslutning i listan med program](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på TOPdesk – säker panel i åtkomstpanelen, du bör få automatiskt loggat in på ditt TOPdesk - säkert program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-secure-tutorial/tutorial_general_203.png