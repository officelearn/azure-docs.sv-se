---
title: 'Självstudier: Azure Active Directory-integrering med Envi MMIS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 008ba69384cd52d4d2d75978aec1602e1c2cce79
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35965803"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Självstudier: Azure Active Directory-integrering med Envi MMIS

I kursen får lära du att integrera Envi MMIS med Azure Active Directory (AD Azure).

Integrera Envi MMIS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Envi MMIS.
- Du kan aktivera användarna att automatiskt hämta loggat in på Envi MMIS (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Envi MMIS, behöver du följande:

- En Azure AD-prenumeration
- En Envi MMIS enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Envi MMIS från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-envi-mmis-from-the-gallery"></a>Att lägga till Envi MMIS från galleriet
Du måste lägga till Envi MMIS från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Envi MMIS i Azure AD.

**Utför följande steg för att lägga till Envi MMIS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Envi MMIS**väljer **Envi MMIS** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Envi MMIS i resultatlistan](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Envi MMIS baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Envi MMIS motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Envi MMIS upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Envi MMIS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Envi MMIS](#create-an-envi-mmis-test-user)**  – har en motsvarighet för Britta Simon Envi MMIS som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Envi MMIS.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Envi MMIS:**

1. I Azure-portalen på den **Envi MMIS** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. På den **Envi MMIS domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Envi MMIS domän med enkel inloggning information](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Envi MMIS domän med enkel inloggning information](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [Envi MMIS klienten supportteamet](mailto:support@ioscorp.com) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/tutorial_general_400.png)

7. Logga in på webbplatsen Envi MMIS som en administratör i en annan webbläsarfönster.

8. Klicka på **min domän** fliken.

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/configure1.png)

9. Klicka på **Redigera**.

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/configure2.png)

10. Välj **Använd fjärrautentiseringen** kryssrutan och välj sedan **HTTP-omdirigering** från den **autentiseringstyp** listrutan.

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/configure3.png)

11. Välj **resurser** och klicka sedan på **överföra Metadata**.

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/configure4.png)

12. I den **överföra Metadata** popup, utför följande steg:

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/configure5.png)

    a. Välj **filen** alternativet från den **Överför från** listrutan.

    b. Överföra metadatafilen hämtas från Azure-portalen genom att välja den **Välj ikon**.

    c. Klicka på **OK**.

13. Efter överföring av hämtade metadatafil fälten ska hämta fylls i automatiskt. Klicka på **uppdatering**

    ![Konfigurera enkel inloggning spara](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/envimmis-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/envimmis-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/envimmis-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/envimmis-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-envi-mmis-test-user"></a>Skapa en testanvändare Envi MMIS

Om du vill aktivera Azure AD-användare kan logga in på Envi MMIS etableras de i Envi MMIS.  
När det gäller Envi MMIS är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen Envi MMIS företag som administratör.

2. Klicka på **användarlistan** fliken.

    ![Lägga till medarbetare](./media/envimmis-tutorial/user1.png)

3. Klicka på **Lägg till användare** knappen.

    ![Lägga till medarbetare](./media/envimmis-tutorial/user2.png)

4. I den **Lägg till användare** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/envimmis-tutorial/user3.png)

    a. I den **användarnamn** textruta typ användarnamnet av Britta Simon konto som **brittasimon@contoso.com**.
    
    b. I den **Förnamn** textruta Ange först namnet på BrittaSimon som **Britta**.

    c. I den **efternamn** textruta efternamn sorts BrittaSimon som **Simon**.

    d. Ange rubriken för användaren i den **rubrik** i textrutan.
    
    e. I den **e-postadress** textruta typ e-postadressen av Britta Simon konto som **brittasimon@contoso.com**.

    f. I den **SSO-användarnamnet** textruta typ användarnamnet av Britta Simon konto som **brittasimon@contoso.com**.

    g. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Envi MMIS.

![Tilldela rollen][200] 

**Om du vill tilldela Envi MMIS Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Envi MMIS**.

    ![Länken Envi MMIS i listan med program](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Envi MMIS på åtkomstpanelen du bör få automatiskt loggat in på ditt Envi MMIS program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png

