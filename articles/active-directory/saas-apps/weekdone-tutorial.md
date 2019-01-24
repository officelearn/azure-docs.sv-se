---
title: 'Självstudier: Azure Active Directory-integrering med Weekdone | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Weekdone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 34921f9a-5637-4420-ab4c-9beb34421909
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: jeedes
ms.openlocfilehash: 869deb1b68d30041b6780151a5712099982bd9b7
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807957"
---
# <a name="tutorial-azure-active-directory-integration-with-weekdone"></a>Självstudier: Azure Active Directory-integrering med Weekdone

I den här självstudien får du lära dig hur du integrerar Weekdone med Azure Active Directory (AD Azure).

Integrera Weekdone med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Weekdone
- Du kan aktivera användarna att automatiskt få loggat in på Weekdone (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Weekdone, behöver du följande objekt:

- En Azure AD-prenumeration
- En Weekdone enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Weekdone från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-weekdone-from-the-gallery"></a>Att lägga till Weekdone från galleriet
För att konfigurera integrering av Weekdone i Azure AD, som du behöver lägga till Weekdone från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Weekdone från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **Weekdone**.

    ![Skapa en Azure AD-användare för testning](./media/weekdone-tutorial/tutorial_weekdone_search.png)

5. I resultatpanelen väljer **Weekdone**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/weekdone-tutorial/tutorial_weekdone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Weekdone baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Weekdone är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Weekdone upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Weekdone, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Weekdone](#creating-a-weekdone-test-user)**  – du har en motsvarighet för Britta Simon i Weekdone som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Weekdone program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Weekdone:**

1. I Azure-portalen på den **Weekdone** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_weekdone_samlbase.png)

3. På den **Weekdone domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_weekdone_url1.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://weekdone.com/a/<tenant>/metadata`

    > [!NOTE]
    > Metadatafilen från weekdone kan hämtas med hjälp av samma URL.

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://weekdone.com/a/<tenantname>`

4. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_weekdone_url2.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://weekdone.com/a/<tenantname>`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Weekdone klienten supportteamet](mailto:hello@weekdone.com) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_weekdone_certificate.png) 

6. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_general_400.png)
    
7. På den **Weekdone Configuration** klickar du på **konfigurera Weekdone** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_weekdone_configure.png) 

8. Att konfigurera enkel inloggning på **Weekdone** sida, som du behöver skicka de hämtade **XML-Metadata, URL: en för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** till [Weekdone support-teamet ](mailto:hello@weekdone.com).

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/weekdone-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/weekdone-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/weekdone-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/weekdone-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-weekdone-test-user"></a>Skapa en Weekdone testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Weekdone. Weekdone stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Weekdone om det inte finns ännu.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [Weekdone klienten supportteamet](mailto:hello@weekdone.com).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Weekdone.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Weekdone, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Weekdone**.

    ![Konfigurera enkel inloggning](./media/weekdone-tutorial/tutorial_weekdone_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Weekdone i åtkomstpanelen du bör få automatiskt loggat in på ditt Weekdone program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/weekdone-tutorial/tutorial_general_01.png
[2]: ./media/weekdone-tutorial/tutorial_general_02.png
[3]: ./media/weekdone-tutorial/tutorial_general_03.png
[4]: ./media/weekdone-tutorial/tutorial_general_04.png

[100]: ./media/weekdone-tutorial/tutorial_general_100.png

[200]: ./media/weekdone-tutorial/tutorial_general_200.png
[201]: ./media/weekdone-tutorial/tutorial_general_201.png
[202]: ./media/weekdone-tutorial/tutorial_general_202.png
[203]: ./media/weekdone-tutorial/tutorial_general_203.png
