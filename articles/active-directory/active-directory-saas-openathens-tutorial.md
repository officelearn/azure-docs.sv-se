---
title: "Självstudier: Azure Active Directory-integrering med OpenAthens | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpenAthens."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 2819e300d94a0bc3b0900419218561fc846effcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Självstudier: Azure Active Directory-integrering med OpenAthens

I kursen får lära du att integrera OpenAthens med Azure Active Directory (AD Azure).

Integrera OpenAthens med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OpenAthens.
- Du kan aktivera användarna att logga in automatiskt till OpenAthens (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med OpenAthens, behöver du följande:

- En Azure AD-prenumeration
- En OpenAthens enkel inloggning aktiverad prenumeration

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till OpenAthens från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-openathens-from-the-gallery"></a>Att lägga till OpenAthens från galleriet
Du måste lägga till OpenAthens från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av OpenAthens i Azure AD.

**Lägga till OpenAthens från galleriet**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Bläddra till **företagsprogram**, och gå till **alla program**.

    ![Fönstret Enterprise program][2]
    
3. Om du vill lägga till nya program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **OpenAthens**väljer **OpenAthens** resultatrutan och välj sedan den **Lägg till** knappen.

    ![OpenAthens i resultatlistan](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med OpenAthens baserat på en användare med namnet ”Britta Simon”.

Azure AD måste du känna till användaren i OpenAthens motsvarighet till användaren i Azure AD för enkel inloggning ska fungera. Med andra ord måste du upprätta en länk förhållandet mellan en Azure AD-användare och relaterade användaren i OpenAthens.

I OpenAthens, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med OpenAthens, måste du utföra följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on), för att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user), för att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare OpenAthens](#create-a-openathens-test-user), har en motsvarighet för Britta Simon OpenAthens som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user), för att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on), för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt OpenAthens program.

**Konfigurera Azure AD enkel inloggning med OpenAthens**

1. I Azure-portalen på den **OpenAthens** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länken][4]

2. Aktivera enkel inloggning, i den **enkel inloggning** dialogrutan **SAML-baserade inloggning** som den **läge**.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. I den **OpenAthens domän och URL: er** ange värdet `https://login.openathens.net/saml/2/metadata-sp` i den **identifierare** textruta.

    ![URL: er och OpenAthens domän med enkel inloggning information](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**, och spara sedan metadatafilen på datorn.

    ![Signeringscertifikat för AMSL Hämta länk](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Välj knappen **Spara**.

    ![Den enkel inloggning spara knappen](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. I en annan webbläsarfönster loggar du in på webbplatsen OpenAthens företag som administratör.

7. Välj **anslutningar** i listan under den **Management** fliken. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Välj **SAML 1.1/2.0**, och välj sedan den **konfigurera** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Om du vill lägga till konfigurationen, Välj den **Bläddra** om du vill överföra metadata XML-filen som du hämtade från Azure-portalen och välj sedan **Lägg till**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Utför följande steg under den **information** fliken.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. I **visa Namnmappningen**väljer **attribut**.

    b. I den **visa namnattributet** text Ange värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. I **unika mappning**väljer **attribut**.

    d. I den **unika användarattribut** text Ange värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. I **Status**, markera alla tre kryssrutor.

    f. I **skapa lokala konton**väljer **automatiskt**.

    g. Välj **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Mer information om funktionen inbäddade dokumentationen finns det [Azure AD inbäddade dokumentationen](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas ”Britta Simon”.

   ![Skapa en testanvändare i Azure AD][100]

**Skapa en testanvändare i Azure AD**

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-knappen](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** textrutan anger du e-postadress för Britta Simon.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** textruta.

    d. Välj **Skapa**.
  
### <a name="create-an-openathens-test-user"></a>Skapa en testanvändare OpenAthens

OpenAthens stöder just-in-time-etablering och användare skapas automatiskt efter en lyckad autentisering. Du behöver inte utföra alla åtgärder i det här avsnittet.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till OpenAthens.

![Tilldela rollen][200] 

**Att tilldela OpenAthens Britta Simon**

1. Bläddra till vyn katalog i Azure-portalen, öppna program visa och gå till **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. I den **program** väljer **OpenAthens**.

    ![Länken OpenAthens i listan med program](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg uppdrag** fönstret.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** väljer **Britta Simon**.

6. Välj den **Välj** knappen i den **användare och grupper** lista.

7. Välj den **tilldela** knappen i den **Lägg uppdrag** fönstret.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **OpenAthens** panelen i panelen åtkomst ska vara loggas du automatiskt ditt OpenAthens program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* En lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory finns [SaaS app integration självstudier för användning med Azure AD](active-directory-saas-tutorial-list.md).
* Mer information om programåtkomst och enkel inloggning med Azure Active Directory finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

