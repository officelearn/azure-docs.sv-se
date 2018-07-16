---
title: 'Självstudier: Azure Active Directory-integration med IQNavigator VMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: 9b264a7ba1479e485ff528ee242c78c4b39010dc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052524"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Självstudier: Azure Active Directory-integration med IQNavigator VMS

I den här självstudien får du lära dig hur du integrerar IQNavigator VMS med Azure Active Directory (AD Azure).

Integrera IQNavigator VMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till IQNavigator VMS
- Du kan aktivera användarna att automatiskt få loggat in på IQNavigator VMS (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med IQNavigator VMS, behöver du följande objekt:

- En Azure AD-prenumeration
- En IQNavigator VMS enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här [– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till IQNavigator VMS från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Att lägga till IQNavigator VMS från galleriet
För att konfigurera integrering av IQNavigator VMS i Azure AD, som du behöver lägga till IQNavigator VMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till IQNavigator VMS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **IQNavigator VMS**.

    ![Skapa en Azure AD-användare för testning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

5. I resultatpanelen väljer **IQNavigator VMS**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med IQNavigator VMS utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i IQNavigator VMS är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i IQNavigator VMS upprättas.

I IQNavigator VMS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med IQNavigator VMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare IQNavigator VMS](#creating-a-iqnavigator-vms-test-user)**  – du har en motsvarighet för Britta Simon i IQNavigator VMS som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt IQNavigator VMS-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med IQNavigator VMS:**

1. I Azure-portalen på den **IQNavigator VMS** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

3. På den **IQNavigator VMS domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. I den **identifierare** textrutan anger du URL:`iqn.com`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

4. Kontrollera **visa avancerade URL-inställningar**, utföra följande steg:

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    I den **vidarebefordrar tillstånd** textrutan anger du ett URL med hjälp av följande mönster:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med bibliotekets aktuella tillstånd för svars-URL och Relay. Kontakta [IQNavigator VMS klienten supportteamet](https://www.beeline.com/iqn-product-support/) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.
    
    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

6. IQNavigator program förväntar sig det unikt värdet för användaridentifierare i namnidentifierare anspråket. Kunden kan mappa rätt värde för namnidentifierare anspråket. I det här fallet har vi mappat användaren. UserPrincipalName för demo ändamål. Men enligt Organisationsinställningarna för din ska du mappa det korrekta värdet för den.

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

8. På den **IQNavigator VMS Configuration** klickar du på **konfigurera IQNavigator VMS** att öppna **konfigurera inloggning** fönstret. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

9. Att konfigurera enkel inloggning på **IQNavigator VMS** sida, som du behöver skicka den **Appfederationsmetadata**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL**till [IQNavigator VMS supportteamet](https://www.beeline.com/iqn-product-support/). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-iqnavigator-vms-test-user"></a>Skapa en IQNavigator VMS testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i IQNavigator VMS. Arbeta med [IQNavigator VMS supportteamet](https://www.beeline.com/iqn-product-support/) att lägga till användare i IQNavigator VMS-konto.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till IQNavigator VMS.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon IQNavigator VMS, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **IQNavigator VMS**.

    ![Konfigurera enkel inloggning](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen IQNavigator VMS i åtkomstpanelen du bör få automatiskt loggat in på ditt IQNavigator VMS-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

