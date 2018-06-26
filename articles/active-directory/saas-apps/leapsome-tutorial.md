---
title: 'Självstudier: Azure Active Directory-integrering med Leapsome | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.openlocfilehash: b23a93db7912aa25b420157241c41533f4f48a27
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938441"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Självstudier: Azure Active Directory-integrering med Leapsome

I kursen får lära du att integrera Leapsome med Azure Active Directory (AD Azure).

Integrera Leapsome med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Leapsome.
- Du kan aktivera användarna att automatiskt hämta loggat in på Leapsome (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Leapsome, behöver du följande:

- En Azure AD-prenumeration
- En Leapsome enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Leapsome från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-leapsome-from-the-gallery"></a>Att lägga till Leapsome från galleriet
Du måste lägga till Leapsome från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Leapsome i Azure AD.

**Utför följande steg för att lägga till Leapsome från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Leapsome**väljer **Leapsome** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Leapsome i resultatlistan](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Leapsome baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Leapsome motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Leapsome upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Leapsome, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Leapsome](#create-a-leapsome-test-user)**  – du har en motsvarighet för Britta Simon i Leapsome som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Leapsome program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Leapsome:**

1. I Azure-portalen på den **Leapsome** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

3. På den **Leapsome domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Leapsome domän med enkel inloggning information](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. I den **identifierare** textruta, ange ett URL-Adressen: `https://www.leapsome.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Leapsome domän med enkel inloggning information](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Det föregående Reply URL och inloggning URL-värdet är inte verkliga värde. Uppdaterar du dem med de faktiska värdena som förklaras senare under kursen.

5. Leapsome program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel.
    
    ![Konfigurera enkel inloggning](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde | Namnrymd |
    | ---------------| --------------- | --------- |   
    | Förnamn | User.givenName | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Efternamn | User.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | rubrik | User.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | bild | URL till den anställde bild | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Värdet för bild-attributet är inte verkliga. Uppdatera det här värdet med faktiska bild-URL. Att hämta värdet kontakten [Leapsome klienten supportteamet](mailto:support@leapsome.com).
    
    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. I den **Namespace** textruta skriver namnområdes-uri för den raden.
    
    e. Klicka på **Ok**

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/leapsome-tutorial/tutorial_general_400.png)
    
9. På den **Leapsome Configuration** klickar du på **konfigurera Leapsome** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Leapsome konfiguration](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

10. I en annan webbläsarfönstret, logga in på Leapsome som en administratör.

11. Klicka på inställningar logotyp längst upp till höger, och klicka sedan på **administrationsinställningar**. 

    ![Ange Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

12. Klicka på den vänstra menyraden **enkel inloggning (SSO)**, och på den **SAML-baserade enkel inloggning (SSO)** sidan utför följande steg:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Välj **aktivera SAML-baserade enkel inloggning**.

    b. Kopiera den **inloggnings-URL (peka användarna här för att starta inloggning)** värdet och klistrar in det i den **inloggnings-URL** TextBox-kontroll i **Leapsome domän och URL: er** avsnitt på Azure-portalen.

    c. Kopiera den **Reply URL (får svar från din identitetsleverantör)** värdet och klistrar in det i den **Reply URL** TextBox-kontroll i **Leapsome domän och URL: er** avsnitt på Azure-portalen.

    d. I den **inloggnings-URL för enkel inloggning (från identitetsleverantören)** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du kopierade från Azure-portalen.

    e. Kopiera certifikatet du har hämtat från Azure-portalen utan--börjar certifikat och END certifikat, kommentarer och klistra in den i den **certifikat (som tillhandahålls av identitetsleverantör)** textruta.

    f. Klicka på **SSO UPPDATERINGSINSTÄLLNINGAR**.
    
### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/leapsome-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/leapsome-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/leapsome-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/leapsome-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-leapsome-test-user"></a>Skapa en testanvändare Leapsome

I det här avsnittet skapar du en användare som kallas Britta Simon i Leapsome. Arbeta med [Leapsome klienten supportteamet](mailto:support@leapsome.com) att lägga till användare eller domän, som måste vara godkända i Leapsome-plattformen. Om domänen har lagts till av teamet, kommer användarna få automatiskt tillhandahållas för Leapsome-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Leapsome.

![Tilldela rollen][200] 

**Om du vill tilldela Leapsome Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Leapsome**.

    ![Länken Leapsome i listan med program](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Leapsome på åtkomstpanelen du bör få automatiskt loggat in på ditt Leapsome program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

