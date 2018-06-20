---
title: 'Självstudier: Azure Active Directory-integrering med ån | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ån.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.openlocfilehash: 0f8621ec55ee58e818a60dc74ea5c4f111909b8a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210283"
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>Självstudier: Azure Active Directory-integrering med ån

I kursen får lära du att integrera ån med Azure Active Directory (AD Azure).

Integrera ån med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ån.
- Du kan aktivera användarna att automatiskt hämta loggat in på ån (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ån behöver du följande:

- En Azure AD-prenumeration
- En ån enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ån från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-versal-from-the-gallery"></a>Att lägga till ån från galleriet
Du måste lägga till ån från galleriet i listan över hanterade SaaS-appar för att konfigurera ån till Azure AD-integrering.

**Utför följande steg för att lägga till ån från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ån**väljer **ån** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Ån i resultatlistan](./media/versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ån baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ån motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ån upprättas.

I ån, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ån, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en ån testanvändare](#create-a-versal-test-user)**  – har en motsvarighet för Britta Simon ån som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet ån.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ån:**

1. I Azure-portalen på den **ån** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/versal-tutorial/tutorial_versal_samlbase.png)

3. På den **ån domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och versal domän med enkel inloggning information](./media/versal-tutorial/tutorial_versal_url.png)

    a. I den **identifierare** textruta Skriv värdet: `VERSAL`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE] 
    > Svars-URL-värdet är inte verkliga. Uppdatera det här värdet med det faktiska Reply-URL. Kontakta [ån supportteamet](https://support.versal.com/hc/) att hämta det här värdet.
    
4. Ditt program förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Följande skärmbild visar ett exempel för det här. Standardvärdet för **användar-ID** är **user.userprincipalname** men **ån** förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller använda rätt attribut-värde baserat på konfigurationen för din organisation.
    
    ![Listrutan för användar-ID](./media/versal-tutorial/tutorial_versal_attribute.png)

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/versal-tutorial/tutorial_versal_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/versal-tutorial/tutorial_general_400.png)
    
7. Konfigurera enkel inloggning på **ån** sida, måste du skicka den hämtade **XML-Metadata för** och **SAML-signeringscertifikat** till [ån supportteamet ](https://support.versal.com/hc/). De kommer att konfigurera organisationen ån för SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/versal-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/versal-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/versal-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/versal-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-versal-test-user"></a>Skapa en ån testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i ån. Följ den [skapar en SAML testanvändare](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user) supportguide för att skapa användaren Britta Simon inom din organisation. Användare måste skapas och aktiveras i ån innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ån.

![Tilldela rollen][200] 

**Om du vill tilldela ån Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ån**.

    ![Ån länken i listan med program](./media/versal-tutorial/tutorial_versal_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan testa du Azure AD enkel inloggning konfigurationen med hjälp av en ån kurs är inbäddad i din webbplats.
Finns det [bädda in organisationens kurser](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **SAML enkel inloggning** stöd guide för instruktioner om hur du bäddar in en ån kurs med stöd för Azure AD enkel inloggning. 

Du behöver skapa en kurs, dela den med din organisation och publicera för att testa kursen bädda in. Se [skapar en kurs](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course), [publicerar en kurs](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course), och [kursen och deltagaren](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management) för mer information.  
                     

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/versal-tutorial/tutorial_general_01.png
[2]: ./media/versal-tutorial/tutorial_general_02.png
[3]: ./media/versal-tutorial/tutorial_general_03.png
[4]: ./media/versal-tutorial/tutorial_general_04.png

[100]: ./media/versal-tutorial/tutorial_general_100.png

[200]: ./media/versal-tutorial/tutorial_general_200.png
[201]: ./media/versal-tutorial/tutorial_general_201.png
[202]: ./media/versal-tutorial/tutorial_general_202.png
[203]: ./media/versal-tutorial/tutorial_general_203.png

