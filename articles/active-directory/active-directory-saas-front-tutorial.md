---
title: "Självstudier: Azure Active Directory-integrering med framför | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och fram."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: d936bc50a66ac2a3c17038ff08351edf9902c99f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Självstudier: Azure Active Directory-integrering med framför

I kursen får lära du att integrera framför med Azure Active Directory (AD Azure).

Integrera framför med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till fram.
- Du kan aktivera användarna att automatiskt hämta inloggade längst fram (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med framför behöver du följande:

- En Azure AD-prenumeration
- En framför enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till framför från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-front-from-the-gallery"></a>Att lägga till framför från galleriet
Du måste lägga till framför från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av framför i Azure AD.

**Utför följande steg för att lägga till framför från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **främre**väljer **främre** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Framför i resultatlistan](./media/active-directory-saas-front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med framför baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren fram till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren fram upprättas.

Framför, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med framför, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare framför](#create-a-front-test-user)**  – du har en motsvarighet för Britta Simon fram som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program fram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med framför:**

1. I Azure-portalen på den **främre** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-front-tutorial/tutorial_front_samlbase.png)

3. På den **främre domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-front-tutorial/tutorial_front_url1.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<companyname>.frontapp.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<companyname>.frontapp.com/sso/saml/callback`

4. Kontrollera **visa avancerade inställningar för URL: en**, om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-front-tutorial/tutorial_front_url2.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.frontapp.com`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare, Reply URL och inloggnings-URL som beskrivs senare i självstudiekursen eller kontakta [främre klienten supportteamet](mailto:support@frontapp.com) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-front-tutorial/tutorial_front_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-front-tutorial/tutorial_general_400.png)
    
7. På den **främre Configuration** klickar du på **konfigurera främre** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-front-tutorial/tutorial_front_configure.png) 

8. Inloggning till framför-klient som administratör.

9. Gå till **inställningar (kugghjulet ikonen längst ned i den vänstra sidopanelen) > Inställningar**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

10. Klicka på **enkel inloggning** länk.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

11. Välj **SAML** i listrutan för **enkel inloggning**.
   
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

12. I den **startpunkten** textruta ange värdet för **inloggning tjänst-URL för enkel** från guiden Konfigurera program för Azure AD.
    
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

13. Öppna din hämtade **Certificate(Base64)** fil i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **signeringscertifikat** textruta.
    
    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

14. På den **tjänstinställningar providern** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning på App-sida](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

    a. Kopiera värdet för **enhets-ID** och klistrar in det i den **identifierare** TextBox-kontroll i **främre domän och URL: er** avsnitt i Azure-portalen.

    b. Kopiera värdet för **ACS URL** och klistrar in det i den **inloggnings-URL** TextBox-kontroll i **främre domän och URL: er** avsnitt i Azure-portalen.
    
15. Klicka på **spara** knappen.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-front-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-front-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-front-test-user"></a>Skapa en testanvändare framför

I det här avsnittet skapar du en användare som kallas Britta Simon fram. Arbeta med [främre klienten supportteamet](mailto:support@frontapp.com) att lägga till användare i fram-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till fram.

![Tilldela rollen][200] 

**Om du vill tilldela framför Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **främre**.

    ![Länken längst fram i listan med program](./media/active-directory-saas-front-tutorial/tutorial_front_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa din Azure AD-SSOconfiguration med åtkomstpanelen.

När du klickar på panelen fram på åtkomstpanelen du ska hämta automatiskt loggat in på ditt program fram. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-front-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png

