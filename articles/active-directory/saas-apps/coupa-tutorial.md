---
title: 'Självstudier: Azure Active Directory-integrering med Coupa | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 440caae63d5d4511b024dda951e76f2f4102f6a7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968869"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Självstudier: Azure Active Directory-integrering med Coupa

I kursen får lära du att integrera Coupa med Azure Active Directory (AD Azure).

Integrera Coupa med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Coupa.
- Du kan aktivera användarna att automatiskt hämta loggat in på Coupa (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Coupa, behöver du följande:

- En Azure AD-prenumeration
- En Coupa enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Coupa från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-coupa-from-the-gallery"></a>Att lägga till Coupa från galleriet
Du måste lägga till Coupa från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Coupa i Azure AD.

**Utför följande steg för att lägga till Coupa från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Coupa**väljer **Coupa** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Coupa i resultatlistan](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Coupa baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Coupa motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Coupa upprättas.

I Coupa, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Coupa, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Coupa](#create-a-coupa-test-user)**  – du har en motsvarighet för Britta Simon i Coupa som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Coupa program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Coupa:**

1. I Azure-portalen på den **Coupa** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

3. På den **Coupa domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Coupa domän med enkel inloggning information](./media/coupa-tutorial/tutorial_coupa_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `http://<companyname>.Coupa.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `<companyname>.coupahost.com`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<companyname>.coupahost.com/sp/ACS.saml2`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL, identifierare och Reply-URL. Kontakta [Coupa klienten supportteamet](https://success.coupa.com/Support/Contact_Us?) att hämta dessa värden. Du får svar URL-värdet från metadata, som beskrivs senare i självstudierna.

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/coupa-tutorial/tutorial_general_400.png)

6. Logga in på webbplatsen Coupa företag som administratör.

7. Gå till **installationsprogrammet \> säkerhetskontroll**.
   
   ![Säkerhetsåtgärder](./media/coupa-tutorial/ic791900.png "säkerhetsåtgärder")

8. I den **logga in med autentiseringsuppgifterna för Coupa** avsnittet, utför följande steg:

    ![Coupa SP metadata](./media/coupa-tutorial/ic791901.png "Coupa SP metadata")
    
    a. Välj **logga in med SAML**.
    
    b. Om du vill hämta metadatafil Coupa till datorn klickar du på **hämta och importera SP metadata**. Öppna metadata och kopiera den **AssertionConsumerService index-URL** värde, klistra in värdet till den **Reply URL** TextBox-kontroll i den **Coupa domän och URL: er** avsnitt. 
    
    c. Klicka på **Bläddra** att överföra metadata hämtas från Azure-portalen.
    
    d. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/coupa-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/coupa-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/coupa-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/coupa-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-coupa-test-user"></a>Skapa en testanvändare Coupa

För att aktivera Azure AD-användare att logga in på Coupa etableras de i Coupa.  

* När det gäller Coupa är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Coupa** företagets webbplats som administratör.

2. Klicka på menyn högst upp **installationsprogrammet**, och klicka sedan på **användare**.
   
   ![Användare](./media/coupa-tutorial/ic791908.png "användare")

3. Klicka på **Skapa**.
   
   ![Skapa användare](./media/coupa-tutorial/ic791909.png "skapa användare")

4. I den **användare skapa** avsnittet, utför följande steg:
   
   ![Användarinformation](./media/coupa-tutorial/ic791910.png "användarinformation")
   
   a. Typ av **inloggning**, **Förnamn**, **efternamn**, **ID för enkel inloggning**, **e-post** attribut för ett giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor.

   b. Klicka på **Skapa**.   
   
   >[!NOTE]
   >Azure Active Directory kontoinnehavaren får ett e-postmeddelande med en länk till bekräfta kontot innan den aktiveras. 
   > 

>[!NOTE]
>Du kan använda något annat Coupa användarens konto skapas verktyg eller API: er som tillhandahålls av Coupa att etablera AAD-användarkonton. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Coupa.

![Tilldela rollen][200] 

**Om du vill tilldela Coupa Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Coupa**.

    ![Länken Coupa i listan med program](./media/coupa-tutorial/tutorial_coupa_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Coupa på åtkomstpanelen du bör få automatiskt loggat in på ditt Coupa program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/coupa-tutorial/tutorial_general_01.png
[2]: ./media/coupa-tutorial/tutorial_general_02.png
[3]: ./media/coupa-tutorial/tutorial_general_03.png
[4]: ./media/coupa-tutorial/tutorial_general_04.png

[100]: ./media/coupa-tutorial/tutorial_general_100.png

[200]: ./media/coupa-tutorial/tutorial_general_200.png
[201]: ./media/coupa-tutorial/tutorial_general_201.png
[202]: ./media/coupa-tutorial/tutorial_general_202.png
[203]: ./media/coupa-tutorial/tutorial_general_203.png

