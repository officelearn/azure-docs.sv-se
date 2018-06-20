---
title: 'Självstudier: Azure Active Directory-integrering med MobileIron | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: jeedes
ms.openlocfilehash: 3963928614f18b25d7e8bce4a8ece3b077863898
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227344"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Självstudier: Azure Active Directory-integrering med MobileIron

I kursen får lära du att integrera MobileIron med Azure Active Directory (AD Azure).

Integrera MobileIron med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till MobileIron.
- Du kan aktivera användarna att automatiskt hämta loggat in på MobileIron (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med MobileIron, behöver du följande:

- En Azure AD-prenumeration
- En MobileIron enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till MobileIron från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mobileiron-from-the-gallery"></a>Att lägga till MobileIron från galleriet
Du måste lägga till MobileIron från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av MobileIron i Azure AD.

**Utför följande steg för att lägga till MobileIron från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **MobileIron**väljer **MobileIron** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![MobileIron i resultatlistan](./media/mobileiron-tutorial/tutorial_mobileiron_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med MobileIron baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i MobileIron motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i MobileIron upprättas.

I MobileIron, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med MobileIron, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare MobileIron](#create-a-mobileiron-test-user)**  – du har en motsvarighet för Britta Simon i MobileIron som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt MobileIron program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med MobileIron:**

1. I Azure-portalen på den **MobileIron** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/mobileiron-tutorial/tutorial_mobileiron_samlbase.png)

3. På den **MobileIron domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och MobileIron domän med enkel inloggning information](./media/mobileiron-tutorial/tutorial_mobileiron_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://www.mobileiron.com/<key>`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och MobileIron domän enkel inloggning](./media/mobileiron-tutorial/tutorial_mobileiron_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<host>.mobileiron.com/user/login.html`
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Du får värdena för nyckeln och värden från den administrativa MobileIron som beskrivs senare i självstudierna-portalen.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/mobileiron-tutorial/tutorial_mobileiron_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/mobileiron-tutorial/tutorial_general_400.png)

7. I en annan webbläsarfönster loggar du in på webbplatsen MobileIron företag som administratör.

8. Gå till **Admin** > **identitet**.

   * Välj **AAD** alternativet i den **information om konfiguration av Cloud IDP** fältet.

    ![Konfigurera enkel inloggning Admin-knapp](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

9. Kopiera värdena i **nyckeln** och **värden** och klistra in dem för att slutföra URL: er i den **MobileIron domän och URL: er** avsnitt i Azure-portalen.

    ![Konfigurera enkel inloggning Admin-knapp](./media/mobileiron-tutorial/key.png)

10. I den **metadata exportfilen från AAD och importera till MobileIron moln fält** klickar du på **Välj fil** att ladda upp den hämtade metadatan från Azure-portalen. Klicka på **klar** överförs en gång.
 
    ![Konfigurera enkel inloggning admin metadata knapp](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/mobileiron-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/mobileiron-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/mobileiron-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/mobileiron-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-mobileiron-test-user"></a>Skapa en testanvändare MobileIron

Om du vill aktivera Azure AD-användare kan logga in på MobileIron etableras de i MobileIron.  
När det gäller MobileIron är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen MobileIron företag som administratör.

2. Gå till **användare** och klicka på **lägga till** > **enkel användaren**.

    ![Konfigurera enkel inloggning användaren knapp](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

3. På den **”enanvändarläge”** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning för att lägga till användaren](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. I **e-postadress** text Ange e-postadressen för användaren som brittasimon@contoso.com.

    b. I **Förnamn** text Ange först namnet på användaren som Britta.

    c. I **efternamn** text Ange efternamn för användaren som Simon.
    
    d. Klicka på **Klar**.  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MobileIron.

![Tilldela rollen][200] 

**Om du vill tilldela MobileIron Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **MobileIron**.

    ![Länken MobileIron i listan med program](./media/mobileiron-tutorial/tutorial_mobileiron_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen MobileIron på åtkomstpanelen du bör få automatiskt loggat in på ditt MobileIron program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/mobileiron-tutorial/tutorial_general_01.png
[2]: ./media/mobileiron-tutorial/tutorial_general_02.png
[3]: ./media/mobileiron-tutorial/tutorial_general_03.png
[4]: ./media/mobileiron-tutorial/tutorial_general_04.png

[100]: ./media/mobileiron-tutorial/tutorial_general_100.png

[200]: ./media/mobileiron-tutorial/tutorial_general_200.png
[201]: ./media/mobileiron-tutorial/tutorial_general_201.png
[202]: ./media/mobileiron-tutorial/tutorial_general_202.png
[203]: ./media/mobileiron-tutorial/tutorial_general_203.png

