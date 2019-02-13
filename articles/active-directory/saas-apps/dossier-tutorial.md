---
title: 'Självstudier: Azure Active Directory-integrering med dokumentation | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dokumentation.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a5fec92-9c01-4ced-99b2-a10e28fc028e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd69616fa544c1d13e14eb8eebb92adb6350d9e8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217538"
---
# <a name="tutorial-azure-active-directory-integration-with-dossier"></a>Självstudier: Azure Active Directory-integrering med dokumentation

I den här självstudien får du lära dig hur du integrerar dokumentation med Azure Active Directory (AD Azure).

Integrera dokumentation med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till dokumentationen.
- Du kan aktivera användarna att automatiskt få loggat in på dokumentation (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med dokumentation, behöver du följande objekt:

- En Azure AD-prenumeration
- En dokumentation enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till dokumentation från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-dossier-from-the-gallery"></a>Att lägga till dokumentation från galleriet

Om du vill konfigurera integreringen av dokumentationen till Azure AD, som du behöver lägga till dokumentation från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till dokumentation från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **dokumentation**väljer **dokumentation** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Dokumentation i resultatlistan](./media/dossier-tutorial/tutorial_dossier_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med dokumentation baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användaren i dokumentationen till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i dokumentationen upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med dokumentation, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare dokumentation](#create-a-dossier-test-user)**  – du har en motsvarighet för Britta Simon i dokumentationen som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för dokumentation.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med dokumentation:**

1. I Azure-portalen på den **dokumentation** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/dossier-tutorial/tutorial_dossier_samlbase.png)

3. På den **dokumentation domän och URL: er** avsnittet, utför följande steg:

    ![Dokumentation domän och URL: er med enkel inloggning för information](./media/dossier-tutorial/tutorial_dossier_url1.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    
    | | |
    |-|-|
    | `https://<SUBDOMAIN>.dossiersystems.com/azuresso/account/SignIn`|
    | `https://dossier.<CLIENTDOMAINNAME>/azuresso/account/SignIn`|
    | |

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `Dossier/<CLIENTNAME>`

    > [!NOTE]
    > För ID-värde som det ska vara i formatet `Dossier/<CLIENTNAME>` eller alla användare anpassade värde.

    c. I textrutan **Svars-URL** anger du en URL med följande mönster:
    | | |
    |-|-|
    |  `https://<SUBDOMAIN>.dossiersystems.com/azuresso`|
    | `https://dossier.<CLIENTDOMAINNAME>/azuresso`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [dokumentation klienten supportteamet](mailto:support@intellimedia.ca) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/dossier-tutorial/tutorial_dossier_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/dossier-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på **dokumentation** sida, som du behöver skicka den **Appfederationsmetadata** till [dokumentation supportteamet](mailto:support@intellimedia.ca). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/dossier-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/dossier-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/dossier-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/dossier-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-dossier-test-user"></a>Skapa en testanvändare för dokumentation

I det här avsnittet skapar du en användare som kallas Britta Simon i dokumentationen. Arbeta med [dokumentation supportteamet](mailto:support@intellimedia.ca) att lägga till användare i dokumentationen-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till dokumentationen.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon dokumentation, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **dokumentation**.

    ![Länken dokumentation i listan med program](./media/dossier-tutorial/tutorial_dossier_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen dokumentation i åtkomstpanelen du bör få automatiskt loggat in på programmets dokumentation.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dossier-tutorial/tutorial_general_01.png
[2]: ./media/dossier-tutorial/tutorial_general_02.png
[3]: ./media/dossier-tutorial/tutorial_general_03.png
[4]: ./media/dossier-tutorial/tutorial_general_04.png

[100]: ./media/dossier-tutorial/tutorial_general_100.png

[200]: ./media/dossier-tutorial/tutorial_general_200.png
[201]: ./media/dossier-tutorial/tutorial_general_201.png
[202]: ./media/dossier-tutorial/tutorial_general_202.png
[203]: ./media/dossier-tutorial/tutorial_general_203.png

