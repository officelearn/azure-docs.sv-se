---
title: 'Självstudier: Azure Active Directory-integrering med Litmos | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 8edf5d0661912050bd8366f38a2b6b75cb6fa819
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966448"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Självstudier: Azure Active Directory-integrering med Litmos

I kursen får lära du att integrera Litmos med Azure Active Directory (AD Azure).

Integrera Litmos med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Litmos.
- Du kan aktivera användarna att automatiskt hämta loggat in på Litmos (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Litmos, behöver du följande:

- En Azure AD-prenumeration
- En Litmos enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Litmos från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-litmos-from-the-gallery"></a>Att lägga till Litmos från galleriet
Du måste lägga till Litmos från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Litmos i Azure AD.

**Utför följande steg för att lägga till Litmos från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Litmos**väljer **Litmos** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Litmos i resultatlistan](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Litmos baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Litmos motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Litmos upprättas.

I Litmos, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Litmos, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Litmos](#create-a-litmos-test-user)**  – du har en motsvarighet för Britta Simon i Litmos som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Litmos program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Litmos:**

1. I Azure-portalen på den **Litmos** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

3. På den **Litmos domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Litmos domän med enkel inloggning information](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.litmos.com/account/Login`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL som beskrivs senare i självstudiekursen eller kontakta [Litmos supportteam](https://www.litmos.com/contact-us/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/litmos-tutorial/tutorial_litmos_certificate.png)

5. Som en del av konfigurationen, måste du anpassa den **SAML-Token attribut** för tillämpningsprogrammet Litmos.

    ![Attributet avsnitt](./media/litmos-tutorial/tutorial_attribute.png)
           
    | Attributnamn   | Attributvärde |   
    | ---------------  | ----------------|
    | Förnamn |User.givenName |
    | Efternamn  |User.surname |
    | E-post |User.Mail |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Lägg till attribut](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![Lägg till attribut Dailog](./media/litmos-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.     

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/litmos-tutorial/tutorial_general_400.png)

7. I ett annat webbläsarfönster inloggning till webbplatsen Litmos företag som administratör.

8. I navigeringsfältet till vänster klickar du på **konton**.
   
    ![Kontoavsnittet på App-sida][22] 

9. Klicka på den **integreringar** fliken.
   
    ![Fliken Integration][23] 

10. På den **integreringar** , bläddrar du ned till **3 part integreringar**, och klicka sedan på **SAML 2.0** fliken.
   
    ![SAML 2.0 avsnitt][24] 

11. Kopiera värdet under **i SAML-slutpunkten för litmos är:** och klistrar in det i den **Reply URL** TextBox-kontroll i den **Litmos domän och URL: er** avsnitt i Azure-portalen. 
   
    ![SAML-slutpunkt][26] 

12. I din **Litmos** program, utför följande steg:
    
     ![Litmos program][25] 
     
     a. Klicka på **aktivera SAML**.
    
     b. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **SAML X.509-certifikat** textruta.
     
     c. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/litmos-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/litmos-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/litmos-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/litmos-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-litmos-test-user"></a>Skapa en testanvändare Litmos

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Litmos.  
Litmos programmet stöder Just-in-Time-etablering. Det innebär ett konto skapas automatiskt om det behövs vid ett försök att komma åt programmet med hjälp av panelen åtkomst.

**Utför följande steg för att skapa en användare som kallas Britta Simon i Litmos:**

1. I ett annat webbläsarfönster inloggning till webbplatsen Litmos företag som administratör.

2. I navigeringsfältet till vänster klickar du på **konton**.
   
    ![Kontoavsnittet på App-sida][22] 

3. Klicka på den **integreringar** fliken.
   
    ![Fliken integreringar][23] 

4. På den **integreringar** , bläddrar du ned till **3 part integreringar**, och klicka sedan på **SAML 2.0** fliken.
   
    ![SAML 2.0][24] 
    
5. Välj **Autogenerate användare**
   
    ![Automatiskt genererat användare][27] 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Litmos.

![Tilldela rollen][200] 

**Om du vill tilldela Litmos Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Litmos**.

    ![Länken Litmos i listan med program](./media/litmos-tutorial/tutorial_litmos_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.  

När du klickar på panelen Litmos på åtkomstpanelen du bör få automatiskt loggat in på ditt Litmos program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png

