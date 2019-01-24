---
title: 'Självstudier: Azure Active Directory-integrering med Optimizely | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 72e0f19a665b1e8cc91939ae24cc71341b5f1674
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819024"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Självstudier: Azure Active Directory-integrering med Optimizely

I den här självstudien får du lära dig hur du integrerar Optimizely med Azure Active Directory (AD Azure).

Integrera Optimizely med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Optimizely
- Du kan aktivera användarna att automatiskt få loggat in på Optimizely (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Optimizely, behöver du följande objekt:

- En Azure AD-prenumeration
- En Optimizely enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Optimizely från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-optimizely-from-the-gallery"></a>Att lägga till Optimizely från galleriet

För att konfigurera integrering av Optimizely i Azure AD, som du behöver lägga till Optimizely från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Optimizely från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **Optimizely**.

    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/tutorial_optimizely_search.png)

5. I resultatpanelen väljer **Optimizely**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Optimizely baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Optimizely är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Optimizely upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Optimizely.

Om du vill konfigurera och testa Azure AD enkel inloggning med Optimizely, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Optimizely](#creating-an-optimizely-test-user)**  – du har en motsvarighet för Britta Simon i Optimizely som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Optimizely program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Optimizely:**

1. I Azure-portalen på den **Optimizely** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. På den **Optimizely domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://app.optimizely.net/<instance name>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:  `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > De här värdena är inte verkliga. Du ska uppdatera värdet med faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien.

4. Optimizely program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_attribute.png)
    
5. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde |
    | ---------------| --------------- |
    | e-post | user.mail |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver den **attributnamnet** visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Klicka på **OK**.

6. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_certificate.png)

7. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_general_400.png)

8. På den **Optimizely Configuration** klickar du på **konfigurera Optimizely** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_configure.png)

9. Att konfigurera enkel inloggning på **Optimizely** sida, kontakta kontoansvarig Optimizely och ange de hämtade **certifikat (Base64)**, och **SAML enkel inloggning för tjänst-URL**.

10. Som svar på din e-post ger Optimizely dig logga på URL: en (SP-initierad SSO) och värden för identifierare (Service Provider entitets-ID).

    a. Kopiera den **SP-initierad SSO-URL** angivna genom Optimizely och klistra in i den **inloggning på URL: en** -textrutan i **Optimizely domän och URL: er** avsnittet på Azure-portalen.

    b. Kopiera den **entitets-ID för Service Provider** angivna genom Optimizely och klistra in i den **identifierare** -textrutan i **Optimizely domän och URL: er** avsnittet på Azure-portalen.

11. I ett annat webbläsarfönster inloggning till programmets Optimizely.

12. Klicka på du kontonamn i övre högra hörnet och sedan **kontoinställningar**.

    ![Azure AD enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_09.png)

13. Markera kryssrutan på fliken konto **aktivera SSO** under enkel inloggning i den **översikt** avsnittet.
  
    ![Azure AD enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_10.png)

14. Klicka på **Spara**

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för Britta Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-an-optimizely-test-user"></a>Skapa en Optimizely testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Optimizely.

1. På startsidan, Välj **medarbetare** fliken.

2. Om du vill lägga till nya medarbetare i projektet, klickar du på **nya medarbetare**.
   
    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_10.png)

3. Fyll i e-postadressen och tilldela dem en roll. Klicka på **bjuda in**.

    ![Skapa en Azure AD-användare för testning](./media/optimizely-tutorial/create_aaduser_11.png)

4. De får en e-postinbjudan. Med den e-postadressen som de behöver logga in på Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Optimizely.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Optimizely, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Optimizely**.

    ![Konfigurera enkel inloggning](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Optimizely i åtkomstpanelen du bör få automatiskt loggat in på ditt Optimizely program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png