---
title: 'Självstudier: Azure Active Directory-integrering med The finansiering Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och The finansiering Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4663cc8a-976a-4c6c-b3b4-1e5df9b66744
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 98ddd26bce7a8e2741db4f8e064a1b5bf2b6bed5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865866"
---
# <a name="tutorial-azure-active-directory-integration-with-the-funding-portal"></a>Självstudier: Azure Active Directory-integrering med The finansiering Portal

I den här självstudien får du lära dig hur du integrerar The finansiering Portal med Azure Active Directory (AD Azure).

Integrera The finansiering Portal med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till The finansiering Portal
- Du kan aktivera användarna att automatiskt få loggat in på The finansiering Portal (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med The finansiering Portal, behöver du följande objekt:

- En Azure AD-prenumeration
- En The finansiering Portal enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till The finansiering portalen från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-the-funding-portal-from-the-gallery"></a>Att lägga till The finansiering portalen från galleriet
Om du vill konfigurera integreringen av The finansiering Portal till Azure AD, som du behöver lägga till The finansiering portalen från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till The finansiering portalen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **The finansiering Portal**.

    ![Skapa en Azure AD-användare för testning](./media/thefundingportal-tutorial/tutorial_thefundingportal_search.png)

5. I resultatpanelen väljer **The finansiering Portal**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/thefundingportal-tutorial/tutorial_thefundingportal_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med The finansiering Portal baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i The finansiering Portal är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i The finansiering Portal upprättas.

I det finansiering Portal tilldelar du värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med The finansiering portalen, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa The finansiering Portal testanvändare](#creating-the-funding-portal-test-user)**  – du har en motsvarighet för Britta Simon i The finansiering Portal som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt The finansiering Portal-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med The finansiering Portal:**

1. I Azure-portalen på den **The finansiering Portal** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_thefundingportal_samlbase.png)

3. På den **The finansiering Portal domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_thefundingportal_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.regenteducation.net/`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.regenteducation.net`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [finansiering Portal klienten supportteamet](mailto:info@regenteducation.com) att hämta dessa värden. 

4. Av finansiering portalprogram förväntar sig SAML-intyg som innehåller ett attribut med namnet ”externalId1”. Värdet för ”externalId1” ska vara en känd studentID. Konfigurera anspråket ”externalId1” för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** av programmet. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_thefundingportal_attribute.png)

5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:

    | Attributnamn | Attributvärde |
    | ------------------- | ---------------- |
    | externalId1 | User.extensionattribute1 |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **attributvärdet** väljer du de attribut som du vill använda för din implementering. Till exempel om du har lagrat StudentID värdet i ExtensionAttribute1, välj sedan user.extensionattribute1.
    
    d. Klicka på **OK**.
 
6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_thefundingportal_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_general_400.png)

8. Att konfigurera enkel inloggning på **The finansiering Portal** sida, som du behöver skicka de hämtade **XML-Metadata för** till [The finansiering Portal supportteamet](mailto:info@regenteducation.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/thefundingportal-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/thefundingportal-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/thefundingportal-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/thefundingportal-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-the-funding-portal-test-user"></a>Skapa The finansiering Portal testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon The finansiering-portalen. Arbeta med [The finansiering Portal supportteamet](mailto:info@regenteducation.com) att lägga till användaren och aktivera enkel inloggning.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till The finansiering Portal.

![Tilldela användare][200] 

**Om du vill tilldela den finansiering Portal Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **The finansiering Portal**.

    ![Konfigurera enkel inloggning](./media/thefundingportal-tutorial/tutorial_thefundingportal_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på det finansiering portalen panelen i åtkomstpanelen, du bör få automatiskt loggat in på ditt The finansiering Portal-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/thefundingportal-tutorial/tutorial_general_04.png

[100]: ./media/thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/thefundingportal-tutorial/tutorial_general_201.png
[202]: ./media/thefundingportal-tutorial/tutorial_general_202.png
[203]: ./media/thefundingportal-tutorial/tutorial_general_203.png

