---
title: 'Självstudier: Azure Active Directory-integrering med Klue | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Klue.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 08341008-980b-4111-adb2-97bbabbf1e47
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 9312006af84e74aa039d762abdfc6edba79a47fb
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824804"
---
# <a name="tutorial-azure-active-directory-integration-with-klue"></a>Självstudier: Azure Active Directory-integrering med Klue

I den här självstudien får du lära dig hur du integrerar Klue med Azure Active Directory (AD Azure).

Integrera Klue med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Klue
- Du kan aktivera användarna att automatiskt få loggat in på Klue (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Klue, behöver du följande objekt:

- En Azure AD-prenumeration
- En Klue enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Klue från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-klue-from-the-gallery"></a>Att lägga till Klue från galleriet

För att konfigurera integrering av Klue i Azure AD, som du behöver lägga till Klue från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Klue från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

4. I sökrutan skriver **Klue**.

    ![Skapa en Azure AD-användare för testning](./media/klue-tutorial/tutorial_klue_search.png)

5. I resultatpanelen väljer **Klue**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/klue-tutorial/tutorial_klue_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Klue baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Klue är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Klue upprättas.

I Klue, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Klue, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Klue](#creating-a-klue-test-user)**  – du har en motsvarighet för Britta Simon i Klue som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Klue program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Klue:**

1. I Azure-portalen på den **Klue** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_klue_samlbase.png)

3. På den **Klue domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_klue_url1.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `urn:klue:<Customer ID>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://app.klue.com/account/auth/saml/<Customer UUID>/callback`

4. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_klue_url2.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://app.klue.com/account/auth/saml/<Customer UUID>/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska svars-URL, identifierare och inloggnings-URL. Kontakta [Klue klienten supportteamet](mailto:support@klue.com) att hämta dessa värden.

5. Klue programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program.

    ![Konfigurera enkel inloggning](./media/klue-tutorial/attribute.png)

6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:

    | Attributnamn      | Attributvärde      |
    | ------------------- | -------------------- |
    | first_name          | user.givenname |
    | last_name           | user.surname |
    | e-post               | user.userprincipalname|

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Klicka på **OK**.

    > [!NOTE]
    > Lämna den **Namespace** värdet tomt.

7. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_klue_certificate.png) 

8. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_general_400.png)

9. På den **Klue Configuration** klickar du på **konfigurera Klue** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_klue_configure.png) 

10. Att konfigurera enkel inloggning på **Klue** sida, som du behöver skicka de hämtade **Certificate(Base64), SAML enkel inloggning för tjänstens URL och SAML entitets-ID** till [Klue supportteamet](mailto:support@klue.com).

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/klue-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/klue-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/klue-tutorial/create_aaduser_03.png)

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/klue-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-klue-test-user"></a>Skapa en Klue testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Klue. Klue stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Klue om det inte finns ännu.

> [!Note]
> Om du vill skapa en användare manuellt, kontakta [Klue supportteamet](mailto:support@klue.com).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Klue.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Klue, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Klue**.

    ![Konfigurera enkel inloggning](./media/klue-tutorial/tutorial_klue_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Klue i åtkomstpanelen du bör få automatiskt loggat in på ditt Klue program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/klue-tutorial/tutorial_general_01.png
[2]: ./media/klue-tutorial/tutorial_general_02.png
[3]: ./media/klue-tutorial/tutorial_general_03.png
[4]: ./media/klue-tutorial/tutorial_general_04.png

[100]: ./media/klue-tutorial/tutorial_general_100.png

[200]: ./media/klue-tutorial/tutorial_general_200.png
[201]: ./media/klue-tutorial/tutorial_general_201.png
[202]: ./media/klue-tutorial/tutorial_general_202.png
[203]: ./media/klue-tutorial/tutorial_general_203.png