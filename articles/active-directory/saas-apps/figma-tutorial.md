---
title: 'Självstudier: Azure Active Directory-integration med Figma | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Figma.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8569cae1-87dd-4c40-9bbb-527ac80d6a96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: jeedes
ms.openlocfilehash: b57fdb3f039a9395133854f8b4d4f2095e3a4f9b
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095253"
---
# <a name="tutorial-azure-active-directory-integration-with-figma"></a>Självstudier: Azure Active Directory-integration med Figma

I den här självstudien får du lära dig hur du integrerar Figma med Azure Active Directory (AD Azure).

Integrera Figma med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Figma.
- Du kan aktivera användarna att automatiskt få loggat in på Figma (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Figma, behöver du följande objekt:

- En Azure AD-prenumeration
- En Figma [enkel inloggning aktiverat prenumeration](https://www.figma.com/pricing/)

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö. Nya kunder och aktiva Figma Team för Professional-prenumeranter kan kontakta Figma till [uppgradera sina prenumerationer](https://www.figma.com/pricing/) Figma organisation nivån.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Figma från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-figma-from-the-gallery"></a>Att lägga till Figma från galleriet

För att konfigurera integrering av Figma i Azure AD, som du behöver lägga till Figma från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Figma från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Figma**. Välj **Figma** i resultatrutan och klicka sedan på den **Lägg till** för att lägga till programmet.

    ![Figma i resultatlistan](./media/figma-tutorial/tutorial_figma_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Figma baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD länkas till Figma.  Om du vill konfigurera och testa Azure AD enkel inloggning med Figma, gör du följande:

1. [**Kontakta supportteamet för Figma** ](mailto:support@figma.com?subject=SAML+Config) att initiera en SAML-konfiguration för din organisation och få en ORG_SAML_CONFIG_ID.
2. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
3. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en testanvändare Figma](#creating-a-figma-test-user)**  – du har en motsvarighet för Britta Simon i Figma som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Figma program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Figma:**

1. I Azure-portalen på den **Figma** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/figma-tutorial/tutorial_general_301.png)

3. Om du vill ändra till **SAML** -läge från ett annat ett annat läge, klickar du på **ändra enkel inloggning läge** på skärmen.

    ![Konfigurera enkel inloggning](./media/figma-tutorial/tutorial_general_300.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](./media/figma-tutorial/tutorial_general_302.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Figma domän och URL: er med enkel inloggning för information](./media/figma-tutorial/tutorial_figma_url1.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/consume`

6. Klicka på **ange ytterligare webbadresser** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Figma domän och URL: er med enkel inloggning för information](./media/figma-tutorial/tutorial_figma_url2.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://www.figma.com/saml/<ORG_SAML_CONFIG_ID>/start`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [Figma supportteamet](mailto:support@figma.com?subject=SAML+Config) att hämta dessa värden.

7. Figma program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![Attributet avsnittet](./media/figma-tutorial/edit_attribute.png)

8. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Namn | Attribut för datakälla|
    | ---------------| --------- |
    | `externalId` | `user.mailnickname` |
    | `displayName` | `user.displayname` |
    | `title` | `user.jobtitle` |
    | `emailaddress` | `user.mail` |
    | `familyName` | `user.surname` |
    | `givenName` | `givenName` |
    | `userName` | `user.userprincipalname` |

    a. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![Nytt attribut](./media/figma-tutorial/new_save_attribute.png)

    ![Lägg till attribut](./media/figma-tutorial/new_attribute_details.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Lämna den **Namespace** tom.

    d. Välj källa som **attributet**.

    e. Från den **källattribut** anger attributvärdet som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

9. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och spara den på din dator.

    ![Länk för hämtning av certifikat](./media/figma-tutorial/tutorial_figma_certificate.png)

10. Om du vill konfigurera enkel inloggning på Figma sida, fyller du i det här formuläret: [ https://goo.gl/forms/XkRB1z5ed4eVUzXn2 ](https://goo.gl/forms/XkRB1z5ed4eVUzXn2). Det ska ta emot din **Appfederationsmetadata** från steg 9.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](./media/figma-tutorial/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](./media/figma-tutorial/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-figma-test-user"></a>Skapa en Figma testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Figma. Figma stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Figma om det inte finns ännu.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HubSpot SAML.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **HubSpot SAML**.

    ![Konfigurera enkel inloggning](./media/figma-tutorial/tutorial_figma_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Figma i åtkomstpanelen du bör få automatiskt loggat in på ditt Figma program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/figma-tutorial/tutorial_general_01.png
[2]: ./media/figma-tutorial/tutorial_general_02.png
[3]: ./media/figma-tutorial/tutorial_general_03.png
[4]: ./media/figma-tutorial/tutorial_general_04.png

[100]: ./media/figma-tutorial/tutorial_general_100.png

[200]: ./media/figma-tutorial/tutorial_general_200.png
[201]: ./media/figma-tutorial/tutorial_general_201.png
[202]: ./media/figma-tutorial/tutorial_general_202.png
[203]: ./media/figma-tutorial/tutorial_general_203.png