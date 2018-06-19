---
title: 'Självstudier: Azure Active Directory-integrering med Sedgwick CMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sedgwick CMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 957931e0-e426-47e7-9904-3ed98d3f504c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jeedes
ms.openlocfilehash: c58d973f878058b9bdc2d1d030e6fe44ba097b67
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963804"
---
# <a name="tutorial-azure-active-directory-integration-with-sedgwick-cms"></a>Självstudier: Azure Active Directory-integrering med Sedgwick CMS

I kursen får lära du att integrera Sedgwick CMS med Azure Active Directory (AD Azure).

Integrera Sedgwick CMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Sedgwick CMS.
- Du kan aktivera användarna att automatiskt hämta loggat in på Sedgwick CMS (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Sedgwick CMS, behöver du följande:

- En Azure AD-prenumeration
- En Sedgwick CMS enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Sedgwick CMS från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sedgwick-cms-from-the-gallery"></a>Att lägga till Sedgwick CMS från galleriet
Du måste lägga till Sedgwick CMS från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Sedgwick CMS i Azure AD.

**Utför följande steg för att lägga till Sedgwick CMS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Sedgwick CMS**väljer **Sedgwick CMS** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Sedgwick CMS i resultatlistan](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Sedgwick CMS baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Sedgwick CMS motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Sedgwick CMS upprättas.

I Sedgwick CMS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sedgwick CMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Sedgwick CMS](#create-a-sedgwick-cms-test-user)**  – har en motsvarighet för Britta Simon Sedgwick CMS som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Sedgwick CMS-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Sedgwick CMS:**

1. I Azure-portalen på den **Sedgwick CMS** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_samlbase.png)

3. På den **Sedgwick CMS domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Sedgwick CMS domän med enkel inloggning information](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_url.png)

    a. I den **identifierare** textruta anger du URL: 
    | |
    |--|
    | `expresspreview.sedgwickcms.net/voe/sso` |
    | `claimlookup.com/Voe/sso` |

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<subdomain>.sedgwickcms.net/voe/sso` |
    | `https://claimlookup.com/Voe/sso` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Sedgwick CMS supportteamet](https://www.sedgwick.com/contact/Pages/contactform.aspx) att hämta dessa värden.
 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/sedgwickcms-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **Sedgwick CMS** sida, måste du skicka den hämtade **XML-Metadata för** till [Sedgwick CMS supportteamet](https://www.sedgwick.com/contact/Pages/contactform.aspx). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/sedgwickcms-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/sedgwickcms-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/sedgwickcms-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/sedgwickcms-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-sedgwick-cms-test-user"></a>Skapa en Sedgwick CMS testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Sedgwick CMS. Arbeta med [Sedgwick CMS supportteamet](https://www.sedgwick.com/contact/Pages/contactform.aspx) att lägga till användare i Sedgwick CMS-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Sedgwick CMS.

![Tilldela rollen][200] 

**Om du vill tilldela Sedgwick CMS Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Sedgwick CMS**.

    ![Sedgwick CMS-länken i listan med program](./media/sedgwickcms-tutorial/tutorial_sedgwickcms_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Sedgwick CMS på åtkomstpanelen du bör få automatiskt loggat in på ditt Sedgwick CMS-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sedgwickcms-tutorial/tutorial_general_01.png
[2]: ./media/sedgwickcms-tutorial/tutorial_general_02.png
[3]: ./media/sedgwickcms-tutorial/tutorial_general_03.png
[4]: ./media/sedgwickcms-tutorial/tutorial_general_04.png

[100]: ./media/sedgwickcms-tutorial/tutorial_general_100.png

[200]: ./media/sedgwickcms-tutorial/tutorial_general_200.png
[201]: ./media/sedgwickcms-tutorial/tutorial_general_201.png
[202]: ./media/sedgwickcms-tutorial/tutorial_general_202.png
[203]: ./media/sedgwickcms-tutorial/tutorial_general_203.png

