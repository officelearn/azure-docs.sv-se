---
title: 'Självstudier: Azure Active Directory-integrering med LinkedIn höjer | Microsoft Docs'
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
ms.openlocfilehash: 9f0d7b2a46cf99c6e1f772a91ed07f7ca91d77a7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227742"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Självstudier: Azure Active Directory-integrering med LinkedIn höjer

Lär dig hur du integrerar LinkedIn höjer med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera LinkedIn höjer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LinkedIn höjer
- Du kan aktivera användarna att automatiskt hämta loggat in på LinkedIn höjer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LinkedIn höjer behöver du följande:

- En Azure AD-prenumeration
- En LinkedIn höjer enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till LinkedIn höjer från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Att lägga till LinkedIn höjer från galleriet
Du måste lägga till LinkedIn höjer från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av LinkedIn höjer i Azure AD.

**Om du vill lägga till LinkedIn höjer från galleriet, utför du följande steg:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LinkedIn höjer**. I resultatrutan, klickar du på **LinkedIn höjer** lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med LinkedIn höjer baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i LinkedIn upphöja till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i LinkedIn höjer upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i LinkedIn höjer.

Om du vill konfigurera och testa Azure AD enkel inloggning med LinkedIn höjer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LinkedIn höjer](#creating-a-linkedin-elevate-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt LinkedIn höjer program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LinkedIn höjer:**

1. I Azure-hanteringsportalen på den **LinkedIn höjer** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

3. I en annan webbläsarfönster inloggning till din LinkedIn höjer klient som administratör.

4. I **Kontocenter**, klickar du på **globala inställningar** under **inställningar**. Markera också **utöka - höjer AAD Test** från den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

5. Klicka på **eller klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **enhets-Id** och **Assertion konsumenten Access (ACS) Url**

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

6. På Azure Portal under **LinkedIn höja domän och URL: er**, utför följande steg om du vill konfigurera enkel inloggning i **IdP initierade** läge

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. I den **identifierare** textruta, ange den **enhets-ID** kopieras från LinkedIn-portalen 

    b. I den **Reply URL** textruta ange den **Assertion konsumenten Access (ACS) Url** kopieras från LinkedIn-portalen

7. Om du vill konfigurera enkel inloggning i **SP-initierad**, klickar du på Visa avancerade URL inställningen alternativ i konfigurationsavsnittet och konfigurera tecknet på URL: en med följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

8. Tillämpningsprogrammet LinkedIn höjer förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här. Standardvärdet för **användar-ID** är **user.userprincipalname** men LinkedIn höjer förväntar detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller använda rätt attribut-värde baserat på konfigurationen för din organisation.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/updateusermail.png)

9. I **användarattribut** klickar du på **visa och redigera andra användarattribut** och ange attribut. Du måste lägga till en annan anspråk med namnet **avdelning** och värdet måste mappas till **user.department**.

    | Attributnamn | Attributvärde |
    | --- | --- |
    | avdelning| User.Department |

      ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/userattribute.png)

      a. Klicka på Lägg till attribut för att öppna sidan attributet Lägg till attributet avdelning enligt nedan-

      ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Klicka på **Ok** att spara attributet.

      c. Ändra namnet på attributet **emailaddress** till **e-post**.

10. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_general_400.png)

12. Gå till **LinkedIn admininställningar** avsnitt. Överför den XML-fil som du precis har laddat ned från Azure portal genom att klicka på alternativet överför XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Klicka på **på** att aktivera enkel inloggning. SSO status kommer att ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.

    ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.

    ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Skapa en LinkedIn höjer testanvändare

LinkedIn höjer programmet stöder bara i tid användaretablering och authentication-användare kommer automatiskt att skapas i programmet. Om administratören sidan Inställningar på portalen Vänd LinkedIn höjer växeln **automatiskt tilldela licenser** till aktiva enbart i tid etablering och detta kan också tilldela en licens till användaren. LinkedIn höjer stöder även automatisk användaretablering, kan du hitta mer information [här](linkedinelevate-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

   ![Skapa en testanvändare i Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till LinkedIn höjer.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon LinkedIn höjer, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **LinkedIn höjer**.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen LinkedIn höjer på åtkomstpanelen du bör få sidan Azure inloggning och på efter lyckad inloggning kan du bör få till LinkedIn höjer programmet.

## <a name="additional-resources"></a>Ytterligare resurser

* [Självstudier: Konfigurera LinkedIn höjer för automatisk användaretablering med Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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