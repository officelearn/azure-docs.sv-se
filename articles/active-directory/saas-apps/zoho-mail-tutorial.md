---
title: 'Självstudier: Azure Active Directory-integrering med Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: bf76b2d3142a28c69c71568af8426360250bd33b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222798"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Självstudier: Azure Active Directory-integrering med Zoho

I kursen får lära du att integrera Zoho med Azure Active Directory (AD Azure).

Integrera Zoho med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zoho.
- Du kan aktivera användarna att automatiskt hämta loggat in på Zoho (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Zoho, behöver du följande:

- En Azure AD-prenumeration
- En Zoho enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Zoho från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zoho-from-the-gallery"></a>Att lägga till Zoho från galleriet
Du måste lägga till Zoho från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Zoho i Azure AD.

**Utför följande steg för att lägga till Zoho från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zoho**väljer **Zoho** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Zoho i resultatlistan](./media/zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Zoho baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Zoho motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Zoho upprättas.

I Zoho, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zoho, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Zoho](#create-a-zoho-test-user)**  – du har en motsvarighet för Britta Simon i Zoho som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zoho program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zoho:**

1. I Azure-portalen på den **Zoho** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. På den **Zoho domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Zoho domän med enkel inloggning information](./media/zoho-mail-tutorial/tutorial_zoho_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Zoho klienten supportteamet](https://www.zoho.com/mail/contact.html) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/zoho-mail-tutorial/tutorial_general_400.png)

6. På den **Zoho Configuration** klickar du på **konfigurera Zoho** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, ändra lösenord URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Zoho konfiguration](./media/zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. Logga in på webbplatsen för företagets Zoho e-post som en administratör i en annan webbläsarfönster.

8. Gå till den **Kontrollpanelen**.
   
    ![Kontrollpanelen](./media/zoho-mail-tutorial/ic789607.png "Kontrollpanelen")

9. Klicka på den **SAML-autentisering** fliken.
   
    ![SAML-autentisering](./media/zoho-mail-tutorial/ic789608.png "SAML-autentisering")

10. I den **information om autentisering av SAML** avsnittet, utför följande steg:
   
    ![Information om autentisering av SAML](./media/zoho-mail-tutorial/ic789609.png "SAML-autentisering-information")
   
    a. I den **inloggnings-URL** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    b. I den **logga ut URL** textruta klistra in **Sign-Out URL** som du har kopierat från Azure-portalen.
   
    c. I den **ändra lösenord URL** textruta klistra in **ändra lösenord URL** som du har kopierat från Azure-portalen.
       
    d. Öppna din Base64-kodade certifikat hämtas från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **PublicKey** textruta.
   
    e. Som **algoritmen**väljer **RSA**.
   
    f. Klicka på **OK**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zoho-mail-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/zoho-mail-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zoho-mail-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zoho-mail-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-zoho-test-user"></a>Skapa en testanvändare Zoho

För att aktivera Azure AD-användare att logga in på Zoho e-post, måste de etableras i Zoho e-post. När det gäller Zoho e-post är etablering en manuell aktivitet.

> [!NOTE]
> Du kan använda något annat Zoho e användarens konto skapas verktyg eller API: er som tillhandahålls av Zoho e-post till etablera AAD-användarkonton.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg om du vill konfigurera ett användarkonto:

1. Logga in på ditt **Zoho e** företagets webbplats som administratör.

2. Gå till **Kontrollpanelen \> e-post och dokument**.

3. Gå till **användarinformation \> lägga till användare**.
   
    ![Lägg till användare](./media/zoho-mail-tutorial/ic789611.png "lägga till användare")

4. På den **lägga till användare** dialogrutan, utför följande steg:
   
    ![Lägg till användare](./media/zoho-mail-tutorial/ic789612.png "lägga till användare")
   
    a. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textruta typen efternamn för användaren som **Simon**.

    c. I den **e-post-ID** textruta e-post-id typ av användare som **brittasimon@contoso.com**.

    d. I den **lösenord** textruta ange lösenord för användaren.
   
    e. Klicka på **OK**.  
      
    > [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande med en länk till bekräfta kontot innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zoho.

![Tilldela rollen][200] 

**Om du vill tilldela Zoho Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Zoho**.

    ![Länken Zoho i listan med program](./media/zoho-mail-tutorial/tutorial_zoho_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Zoho på åtkomstpanelen du bör få automatiskt loggat in på ditt Zoho program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/zoho-mail-tutorial/tutorial_general_203.png

