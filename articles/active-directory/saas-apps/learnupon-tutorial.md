---
title: 'Självstudier: Azure Active Directory-integration med LearnUpon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 3409545169208adc87eb63358082afc6c6d8d575
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042120"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Självstudier: Azure Active Directory-integration med LearnUpon

I den här självstudien får du lära dig hur du integrerar LearnUpon med Azure Active Directory (AD Azure).

Integrera LearnUpon med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till LearnUpon
- Du kan aktivera användarna att automatiskt få loggat in på LearnUpon (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LearnUpon, behöver du följande objekt:

- En Azure AD-prenumeration
- En LearnUpon enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till LearnUpon från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-learnupon-from-the-gallery"></a>Att lägga till LearnUpon från galleriet
För att konfigurera integrering av LearnUpon i Azure AD, som du behöver lägga till LearnUpon från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till LearnUpon från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **LearnUpon**.

    ![Skapa en Azure AD-användare för testning](./media/learnupon-tutorial/tutorial_learnupon_search.png)

5. I resultatpanelen väljer **LearnUpon**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med LearnUpon baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i LearnUpon är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i LearnUpon upprättas.

I LearnUpon, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med LearnUpon, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare LearnUpon](#creating-a-learnupon-test-user)**  – du har en motsvarighet för Britta Simon i LearnUpon som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt LearnUpon program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med LearnUpon:**

1. I Azure-portalen på den **LearnUpon** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. På den **LearnUpon domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera det här värdet med faktiska svars-URL. Att hämta det här värdet Kontakta [LearnUpon supportteamet](https://www.learnupon.com/features/support/).



4. På den **SAML-signeringscertifikat** avsnittet, leta upp den **tumavtryck** – detta kommer att läggas till dina LearnUpon SAML-inställningar.

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_general_400.png)

6. På den **LearnUpon Configuration** klickar du på **konfigurera LearnUpon** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Öppna en annan instans av webbläsare och logga in till LearnUpon med ett administratörskonto. 

8. Klicka på den **inställningar** fliken.
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_06.png)

9. Klicka på **Single Sign On - SAML**, och klicka sedan på **allmänna inställningar** att konfigurera SAML-inställningar.
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

10. I den **allmänna inställningar** avsnittet, utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Välj **aktiverat**.

    b. Välj **Version** som **2.0**.

    c. Välj **hoppa över villkor** som **nr**.

    d. I den **SAML-Token efter parameternamn** textrutan skriver namnet på begäran efter parameter till URL för SAML-konsument som nämns ovan som innehåller SAML-försäkran som ska verifieras och autentiseras – till exempel **SAMLResponse** .

    e. I den **Format för namn på identifierare** textrutan typ som det värde som anger var i SAML-försäkran användare identifierare (e-postadress) finns – till exempel **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**.
  
    f. I den **identifiera Providerplats** textrutan skriver du det värde som anger där användarna skickas till om de klickar på överförda ikonen från dina Azure-portalen inloggningsskärmen.
  
    g. I den **utloggnings-URL** textrutan klistra in den **URL: en för utloggning** som du har kopierat från Azure-portalen.
    
    h. Klicka på **hantera finger utskrifter**, och sedan överför fingeravtryck på nedladdade certifikatet.

11. Klicka på **användarinställningar**, och utför följande steg:
   
     ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. I den **förnamn identifierare Format** textrutan typ som det värde som talar om för oss var i SAML-försäkran användare firstname finns – till exempel: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. I den **senaste Format för namn på identifierare** textrutan typ som det värde som talar om för oss var i SAML-försäkran användare lastname finns – till exempel: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/learnupon-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/learnupon-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/learnupon-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-learnupon-test-user"></a>Skapa en LearnUpon testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i LearnUpon. LearnUpon stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inga uppgift åt dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt LearnUpon om det inte finns ännu. [Konfigurera Azure AD enkel inloggning på](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta [LearnUpon supportteamet](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LearnUpon.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon LearnUpon, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **LearnUpon**.

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen LearnUpon i åtkomstpanelen du bör få automatiskt loggat in på ditt LearnUpon program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

