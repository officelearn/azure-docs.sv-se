---
title: 'Självstudier: Azure Active Directory-integrering med & sanningen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och & sanningen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d702060-1b89-4e9d-9f01-ede4f1171c73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 8b95459e00296950924c7837890e2313bbcb183d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173113"
---
# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Självstudier: Azure Active Directory-integrering med & sanningen

I den här självstudien får du lära dig hur du integrerar & sanningen med Azure Active Directory (AD Azure).

Integrera och även med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till & sanningen
- Du kan ge användarna får automatiskt inloggade till & sanningen (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD behöver-integrering med & sanningen du du följande objekt:

- En Azure AD-prenumeration
- A & sanningen enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till & sanningen från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-frankly-from-the-gallery"></a>Att lägga till & sanningen från galleriet
Om du vill konfigurera integreringen av & sanningen i Azure AD, som du behöver lägga till & sanningen från galleriet i din lista över hanterade SaaS-appar.

**Så här lägger du till och även från galleriet, utför följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **& sanningen**.

    ![Skapa en Azure AD-användare för testning](./media/andfrankly-tutorial/tutorial_andfrankly_search.png)

5. I resultatpanelen väljer **& sanningen**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/andfrankly-tutorial/tutorial_andfrankly_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet konfigurerar du och testa Azure AD enkel inloggning med & sanningen baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, Azure AD måste veta vilka motsvarande användaren i & sanningen är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren måste i & sanningen upprättas.

I & sanningen tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD behöver enkel inloggning med & sanningen du för att slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en & sanningen testanvändare](#creating-a-frankly-test-user)**  – du har en motsvarighet för Britta Simon i & sanningen som är länkade till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din & sanningen program.

**För att konfigurera Azure AD enkel inloggning med & sanningen utför följande steg:**

1. I Azure-portalen på den **& sanningen** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/andfrankly-tutorial/tutorial_andfrankly_samlbase.png)

3. På den **& sanningen domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/andfrankly-tutorial/tutorial_andfrankly_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

4. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/andfrankly-tutorial/tutorial_andfrankly_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren inloggning och svars-URL. Kontakta [andfrankly supportteamet](mailto:help@andfrankly.com) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/andfrankly-tutorial/tutorial_andfrankly_certificate.png) 

6. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/andfrankly-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på **& sanningen** sida, som du behöver skicka de hämtade **XML-Metadata för** till [andfrankly supportteamet](mailto:help@andfrankly.com). 

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/andfrankly-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/andfrankly-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/andfrankly-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/andfrankly-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-frankly-test-user"></a>Skapa en & sanningen testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i & sanningen. Arbeta med [andfrankly supportteamet](mailto:help@andfrankly.com) att lägga till användare i & sanningen plattform.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till & sanningen.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon till & sanningen, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **& sanningen**.

    ![Konfigurera enkel inloggning](./media/andfrankly-tutorial/tutorial_andfrankly_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på & sanningen panelen i åtkomstpanelen, bör du få automatiskt loggat in på ditt & sanningen program

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/andfrankly-tutorial/tutorial_general_04.png

[100]: ./media/andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/andfrankly-tutorial/tutorial_general_201.png
[202]: ./media/andfrankly-tutorial/tutorial_general_202.png
[203]: ./media/andfrankly-tutorial/tutorial_general_203.png

