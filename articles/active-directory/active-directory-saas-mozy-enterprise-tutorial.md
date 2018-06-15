---
title: 'Självstudier: Azure Active Directory-integrering med Mozy Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: 37b0cdce9b77b304a24ff3419f618f193fb2ad52
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349054"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Självstudier: Azure Active Directory-integrering med Mozy Enterprise

I kursen får lära du att integrera Mozy Enterprise med Azure Active Directory (AD Azure).

Integrera Mozy Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mozy Enterprise
- Du kan aktivera användarna att automatiskt hämta loggat in på Mozy Enterprise (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Mozy Enterprise, behöver du följande:

- En Azure AD-prenumeration
- En Mozy Enterprise enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Mozy Enterprise från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Att lägga till Mozy Enterprise från galleriet
Du måste lägga till Mozy Enterprise från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Mozy Enterprise i Azure AD.

**Utför följande steg för att lägga till Mozy Enterprise från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Mozy Enterprise**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. Välj i resultatpanelen **Mozy Enterprise**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
Du konfigurera och testa Azure AD enkel inloggning med Mozy Enterprise baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till användaren i Mozy Enterprise motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Mozy Enterprise upprättas.

I Mozy Enterprise, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mozy Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i Mozy företag som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Mozy Enterprise-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mozy Enterprise:**

1. I Azure-portalen på den **Mozy Enterprise** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. På den **Mozy företagsdomänen och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Mozy Enterprise-klienten supportteamet](http://support.mozy.com/) att hämta det här värdet.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. På den **Mozy företagskonfiguration** klickar du på **konfigurera Mozy Enterprise** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. Logga in på webbplatsen Mozy Enterprise företag som en administratör i en annan webbläsarfönster.

8. I den **Configuration** klickar du på **autentiseringsprincip**.
   
   ![Autentiseringsprincip](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "autentiseringsprincip")

9. På den **autentiseringsprincip** avsnittet, utför följande steg:
   
   ![Autentiseringsprincip](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "autentiseringsprincip")
   
   a. Välj **katalogtjänsten** som **Provider**.
   
   b. Välj **använder LDAP Push**.
   
   c. Klicka på den **SAML-autentisering** fliken.
   
   d. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **autentiserings-URL för** textruta.
   
   e. Klistra in **SAML enhets-ID**, som du har kopierat från Azure-portalen i den **SAML Endpoint** textruta.
   
   f. Öppna din hämtade Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet till **SAML certifikat** textruta.
   
   g. Välj **aktivera enkel inloggning för administratörer att logga in med sina nätverksinloggningsuppgifter**.
   
   h. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Skapa en testanvändare Mozy Enterprise

För att aktivera Azure AD-användare att logga in på Mozy Enterprise, måste de etableras i Mozy Enterprise. När det gäller Mozy Enterprise är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda något annat Mozy Enterprise användarens konto skapas verktyg eller API: er som tillhandahålls av Mozy Enterprise etablera AAD-användarkonton.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på ditt **Mozy Enterprise** klient.

2. Klicka på **användare**, och klicka sedan på **Lägg till nya användare**.
   
   ![Användare](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "användare")
   
   >[!NOTE]
   >Den **Lägg till nya användare** alternativet visas bara om **Mozy** är markerad som providern under **autentiseringsprincip**. Om SAML-autentisering är konfigurerad, sedan användare läggs till automatiskt på sin första inloggning via enkel inloggning på.
    
3. I användardialogrutan ny utför du följande steg:
   
   ![Lägg till användare](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "lägga till användare")
   
   a. Från den **väljer du en grupp** väljer du en grupp.
   
   b. Från den **vilken typ av användare** väljer du en typ.
   
   c. I den **användarnamn** textruta skriver du namnet på Azure AD-användare.
   
   d. I den **e-post** textruta Skriv e-postadress för Azure AD-användare.
   
   e. Välj **skicka e-post för användaren instruktion**.
   
   f. Klicka på **lägga till användare**.

     >[!NOTE]
     > Efter att användaren kan skickas ett e-postmeddelande till Azure AD-användare som innehåller en länk för att bekräfta kontot innan den aktiveras.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mozy Enterprise.

![Tilldela användare][200] 

**Om du vill tilldela Mozy Enterprise Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Mozy Enterprise**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Mozy Enterprise på åtkomstpanelen bör du hämta inloggningssidan Mozy affärsprogram.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png

