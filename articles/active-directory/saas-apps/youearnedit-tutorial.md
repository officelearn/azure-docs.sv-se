---
title: 'Självstudier: Azure Active Directory-integrering med YouEarnedIt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och YouEarnedIt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3011d44d-dfcf-4061-888f-cff90fbc8150
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: 5af3195f8543e5ef3763666b3a997ffb4cd25713
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213790"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>Självstudier: Azure Active Directory-integrering med YouEarnedIt

I kursen får lära du att integrera YouEarnedIt med Azure Active Directory (AD Azure).

Integrera YouEarnedIt med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till YouEarnedIt.
- Du kan aktivera användarna att automatiskt hämta loggat in på YouEarnedIt (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med YouEarnedIt, behöver du följande:

- En Azure AD-prenumeration
- En YouEarnedIt enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till YouEarnedIt från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-youearnedit-from-the-gallery"></a>Att lägga till YouEarnedIt från galleriet
Du måste lägga till YouEarnedIt från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av YouEarnedIt i Azure AD.

**Utför följande steg för att lägga till YouEarnedIt från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **YouEarnedt**väljer **YouEarnedt** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![YouEarnedIt i resultatlistan](./media/youearnedit-tutorial/tutorial_youearnedit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med YouEarnedIt baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i YouEarnedIt motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i YouEarnedIt upprättas.

I YouEarnedIt, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med YouEarnedIt, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare YouEarnedIt](#create-a-youearnedit-test-user)**  – du har en motsvarighet för Britta Simon i YouEarnedIt som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt YouEarnedIt program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med YouEarnedIt:**

1. I Azure-portalen på den **YouEarnedIt** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/youearnedit-tutorial/tutorial_youearnedit_samlbase.png)

3. På den **YouEarnedIt domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och YouEarnedIt domän med enkel inloggning information](./media/youearnedit-tutorial/tutorial_youearnedit_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: 
    | Miljö  | Mönster  |
    |:--- |:--- |
    | Produktion | `https://<company name>.youearnedit.com/users/sign_in` |
    | Begränsat läge  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    b. I den **identifierare** textruta Skriv en URL med följande mönster:
    | Miljö  | Mönster  |
    |:--- |:--- |
    | Produktion | `https://<company name>.youearnedit.com` |
    | Begränsat läge  |`https://<company name>.sandbox.youearnedit.com` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [YouEarnedIt klienten supportteamet](https://youearnedit.freshdesk.com/support/tickets/new) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/youearnedit-tutorial/tutorial_youearnedit_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/youearnedit-tutorial/tutorial_general_400.png)

6. På den **YouEarnedIt Configuration** klickar du på **konfigurera YouEarnedIt** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![YouEarnedIt konfiguration](./media/youearnedit-tutorial/tutorial_youearnedit_configure.png) 

7. Konfigurera enkel inloggning på **YouEarnedIt** sida, måste du skicka den hämtade **Certificate(Base64)** och **SAML enkel inloggning Tjänstwebbadress** till [YouEarnedIt supportteamet](https://youearnedit.freshdesk.com/support/tickets/new). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/youearnedit-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/youearnedit-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/youearnedit-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/youearnedit-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-youearnedit-test-user"></a>Skapa en testanvändare YouEarnedIt

I det här avsnittet skapar du en användare som kallas Britta Simon i YouEarnedIt. Se tillsammans med YouEarnedIt supportteamet att lägga till användare i YouEarnedIt-plattformen.

>[!NOTE]
>YouEarnedIt räknar identitetsleverantören ange en e-postadress eller ett användarnamn i attributet NameID. Autentiseringen misslyckas om en motsvarande användarnamn eller e-postadress finns inte i databasen eller inte matchar varandra exakt. Detta kräver att konton importeras till YouEarnedIt-systemet innan SSO-integration (vanligtvis antingen via API: et eller CSV-import).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till YouEarnedIt.

![Tilldela rollen][200] 

**Om du vill tilldela YouEarnedIt Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **YouEarnedIt**.

    ![Länken YouEarnedIt i listan med program](./media/youearnedit-tutorial/tutorial_youearnedit_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen YouEarnedIt på åtkomstpanelen du bör få automatiskt loggat in på ditt YouEarnedIt program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/youearnedit-tutorial/tutorial_general_04.png

[100]: ./media/youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/youearnedit-tutorial/tutorial_general_201.png
[202]: ./media/youearnedit-tutorial/tutorial_general_202.png
[203]: ./media/youearnedit-tutorial/tutorial_general_203.png

