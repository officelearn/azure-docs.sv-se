---
title: 'Självstudier: Azure Active Directory-integrering med ArcGIS Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ea2b32b43fedacba7b8a60db29762c32fda65aa5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306350"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Självstudier: Azure Active Directory-integrering med ArcGIS Enterprise

Lär dig hur du integrerar ArcGIS Enterprise med Azure Active Directory (AD Azure) i den här självstudien.

Integrera ArcGIS Enterprise med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ArcGIS Enterprise.
- Du kan aktivera användarna att automatiskt få loggat in på ArcGIS Enterprise (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ArcGIS Enterprise, behöver du följande objekt:

- En Azure AD-prenumeration
- En ArcGIS-Enterprise enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ArcGIS Enterprise från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Att lägga till ArcGIS Enterprise från galleriet

Om du vill konfigurera integreringen av ArcGIS Enterprise till Azure AD, som du behöver lägga till ArcGIS Enterprise från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ArcGIS Enterprise från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ArcGIS Enterprise**väljer **ArcGIS Enterprise** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ArcGIS Enterprise i resultatlistan](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ArcGIS Enterprise baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i ArcGIS Enterprise till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ArcGIS företag upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ArcGIS Enterprise, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ArcGIS Enterprise](#create-an-arcgis-enterprise-test-user)**  – du har en motsvarighet för Britta Simon i ArcGIS-företag som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ArcGIS Enterprise-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ArcGIS Enterprise:**

1. I Azure-portalen på den **ArcGIS Enterprise** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. På den **ArcGIS Enterprise domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![ArcGIS Enterprise domän och URL: er med enkel inloggning för information](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `<EXTERNAL_DNS_NAME>.portal`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![ArcGIS Enterprise domän och URL: er med enkel inloggning för information](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [ArcGIS Enterprise Client supportteamet](mailto:support@esri.com) att hämta dessa värden. Du får ID-värde från **ange identitetsprovider** som beskrivs senare i den här självstudien.

5. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. I ett annat webbläsarfönster logga du in på ditt företag ArcGIS Företagsplats som administratör.

8. Välj **organisation > Redigera inställningar**.

    ![ArcGIS företagskonfiguration](./media/arcgisenterprise-tutorial/configure1.png)

9. Välj **Security** fliken.

    ![ArcGIS företagskonfiguration](./media/arcgisenterprise-tutorial/configure2.png)

10. Rulla ned till den **Enterprise inloggningar via SAML** och väljer **ange ENTERPRISE inloggningen**.

    ![ArcGIS företagskonfiguration](./media/arcgisenterprise-tutorial/configure3.png)

11. På den **ange identitetsprovider** avsnittet, utför följande steg:

    ![ArcGIS företagskonfiguration](./media/arcgisenterprise-tutorial/configure4.png)

    a. Ange ett namn som liknar **Azure Active Directory-Test** i den **namn** textrutan.

    b. I den **URL** textrutan klistra in den **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.

    c. Klicka på **visa avancerade inställningar** och kopiera den **entitets-ID** värde och klistra in den i den **identifierare** -textrutan i den **ArcGIS Enterprise domän och URL: er** avsnitt i Azure-portalen.
    
    ![ArcGIS företagskonfiguration](./media/arcgisenterprise-tutorial/configure5.png)

    d. Klicka på **uppdatering IDENTITETSPROVIDER**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-an-arcgis-enterprise-test-user"></a>Skapa en testanvändare ArcGIS Enterprise

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i ArcGIS-företag. ArcGIS Enterprise stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt ArcGIS Enterprise om det inte finns ännu.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [ArcGIS Enterprise support-teamet](mailto:support@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ArcGIS Enterprise.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon ArcGIS Enterprise, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **ArcGIS Enterprise**.

    ![Länken ArcGIS Enterprise i listan med program](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ArcGIS Enterprise i åtkomstpanelen du bör få automatiskt loggat in på dina ArcGIS företagsprogram.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png