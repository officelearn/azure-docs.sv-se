---
title: 'Självstudier: Azure Active Directory-integration med LinkedIn höjer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn höjer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: f8f12263ca71b8e88033484bc03fc4cff9e25bc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435221"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Självstudier: Azure Active Directory-integration med LinkedIn höjer

I den här självstudien får lära du att integrera LinkedIn höjer med Azure Active Directory (AD Azure).

Integrera LinkedIn höjer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LinkedIn höjer
- Du kan aktivera användarna att automatiskt få loggat in på LinkedIn höjer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LinkedIn höjer, behöver du följande objekt:

- En Azure AD-prenumeration
- En LinkedIn höjer enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LinkedIn höjer från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Att lägga till LinkedIn höjer från galleriet
Om du vill konfigurera integreringen av LinkedIn utöka till Azure AD, som du behöver lägga till LinkedIn höjer från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till LinkedIn höjer från galleriet, utför du följande steg:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **LinkedIn höjer**. I resultatrutan, klickar du på **LinkedIn höjer** att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LinkedIn höjer baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användaren i LinkedIn upphöja till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LinkedIn höjer upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i LinkedIn höjer.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn höjer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare LinkedIn höjer](#creating-a-linkedin-elevate-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt LinkedIn höjer program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LinkedIn höjer:**

1. I hanteringsportalen för Azure på den **LinkedIn höjer** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. I ett annat webbläsarfönster inloggning till LinkedIn höjer-klienten som administratör.

1. I **Kontocenter**, klickar du på **globala inställningar** under **inställningar**. Markera också **utöka - höjer AAD Test** från den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **eller klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **entitets-Id** och **Assertion konsument Access (ACS)-Url**

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. På Azure Portal under **LinkedIn höjer domän och URL: er**, utför följande steg om du vill konfigurera enkel inloggning i **IdP-initierad** läge

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. I den **identifierare** textrutan anger du den **entitets-ID** kopieras från LinkedIn-portalen 

    b. I den **svars-URL** textrutan anger du den **Assertion konsument Access (ACS) Url** kopieras från LinkedIn-portalen

1. Om du vill konfigurera enkel inloggning i **SP-initierat**, klickar du på Visa avancerade URL alternativet med inställningen i konfigurationsavsnittet och konfigurera tecknet på URL: en med följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. LinkedIn utöka programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men LinkedIn höjer förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller Använd rätt attribut-värde baserat på konfigurationen för din organisation.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/updateusermail.png)

1. I **användarattribut** klickar du på **visa och redigera alla andra användarattribut** och ange attribut. Du måste lägga till en annan anspråk med namnet **avdelning** och värdet måste mappas till **user.department**.

    | Attributnamn | Attributvärde |
    | --- | --- |
    | Avdelning| User.Department |

      ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/userattribute.png)

      a. Klicka på Lägg till attribut så öppnas sidan med attributet Lägg till attributet avdelning som du ser nedan –

      ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Klicka på **Ok** att spara attributet.

      c. Ändra namnet på attributet **e-postadress** till **e-post**.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Gå till **LinkedIn administratörsinställningar** avsnittet. Ladda upp XML-filen som du precis har laddat ned från Azure portal genom att klicka på alternativet ladda upp XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Klicka på **på** att aktivera enkel inloggning. SSO status kommer att ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.

    ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Skapa en LinkedIn höjer testanvändare

LinkedIn utöka programmet stöder bara i tid användaretablering och -autentiserade användare kommer att skapas i programmet automatiskt. På administratören sidan Inställningar på LinkedIn höjer portal andra växeln **automatiskt tilldela licenser** till aktiva Just-in-time-etablering och detta kommer också tilldela en licens till användaren. LinkedIn höjer stöder även automatisk användaretablering, kan du hitta mer information om [här](linkedinelevate-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

   ![Skapa en Azure AD-användare för testning](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst LinkedIn höjer.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon att upphöja LinkedIn, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **LinkedIn höjer**.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LinkedIn höjer i åtkomstpanelen, bör du få sidan Azure inloggning och på efter lyckad inloggning, bör du få ditt LinkedIn höjer program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Självstudie: Konfigurera LinkedIn höjer för automatisk användarförsörjning med Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png