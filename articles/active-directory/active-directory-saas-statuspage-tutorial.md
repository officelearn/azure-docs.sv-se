---
title: 'Självstudier: Azure Active Directory-integrering med StatusPage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 4b28e9c90da60aa9fb2113c5a179095156d16e02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Självstudier: Azure Active Directory-integrering med StatusPage

I kursen får lära du att integrera StatusPage med Azure Active Directory (AD Azure).

Integrera StatusPage med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till StatusPage
- Du kan aktivera användarna att automatiskt hämta loggat in på StatusPage (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med StatusPage, behöver du följande:

- En Azure AD-prenumeration
- En StatusPage enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till StatusPage från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-statuspage-from-the-gallery"></a>Att lägga till StatusPage från galleriet
Du måste lägga till StatusPage från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av StatusPage i Azure AD.

**Utför följande steg för att lägga till StatusPage från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **StatusPage**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_search.png)

5. Välj i resultatpanelen **StatusPage**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med StatusPage baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i StatusPage motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i StatusPage upprättas.

I StatusPage, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med StatusPage, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare StatusPage](#creating-a-statuspage-test-user)**  – du har en motsvarighet för Britta Simon i StatusPage som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt StatusPage program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med StatusPage:**

1. I Azure-portalen på den **StatusPage** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. På den **StatusPage domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Kontakta supportteamet StatusPage på [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)att begära metadata som krävs för att konfigurera enkel inloggning. 
    >
    >a. Kopiera värdet utfärdaren från metadata, och klistrar in det i den **identifierare** textruta.
    >
    >b. Kopiera den URL som svar från metadata, och klistrar in det i den **Reply URL** textruta.

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_general_400.png)

6. På den **StatusPage Configuration** klickar du på **konfigurera StatusPage** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_configure.png) 

7. I ett nytt webbläsarfönster inloggning till webbplatsen StatusPage företag som administratör.

8. I verktygsfältet klickar du på **Hantera konto**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 

10. Klicka på den **enkel inloggning** fliken. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 

11. Utför följande steg på konfigurationssidan för enkel inloggning:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. I den **mål-URL för SSO** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    b. Öppna din hämtat certifikat i anteckningar, kopiera innehållet och klistrar in det i den **certifikat** textruta. 

    c. Klicka på **Spara konfiguration**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-statuspage-test-user"></a>Skapa en testanvändare StatusPage

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i StatusPage.

StatusPage stöder just-in-time-etablering. Du har redan aktiverats i [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

**Utför följande steg för att skapa en användare som kallas Britta Simon i StatusPage:**

1. Inloggning på webbplatsen StatusPage företag som administratör.

2. Klicka på menyn högst upp **Hantera konto**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png)

3. Klicka på den **gruppmedlemmar** fliken. 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 

4. Klicka på **Lägg till TEAMMEDLEM**. 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 

5. Typ av **e-postadress**, **Förnamn**, och **Sur namn** av en giltig användare som du vill etablera i relaterade textrutor. 
   
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 

6. Som **rollen**, Välj **administratör**.

7. Klicka på **skapa konto**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till StatusPage.

![Tilldela användare][200] 

**Om du vill tilldela StatusPage Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **StatusPage**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen StatusPage på åtkomstpanelen du bör få automatiskt loggat in på ditt StatusPage program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png

