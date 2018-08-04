---
title: 'Självstudier: Azure Active Directory-integration med ProMaster (genom Inlogik) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ProMaster (genom Inlogik).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeedes
ms.openlocfilehash: 13bb128836590fb43e0c6a2f7131f83a99a23eaf
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506684"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Självstudier: Azure Active Directory-integration med ProMaster (genom Inlogik)

I den här självstudien får du lära dig hur du integrerar ProMaster (genom Inlogik) med Azure Active Directory (AD Azure).

Integrera ProMaster (genom Inlogik) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ProMaster (genom Inlogik).
- Du kan aktivera användarna att automatiskt få loggat in på ProMaster (genom Inlogik) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ProMaster (genom Inlogik), behöver du följande objekt:

- En Azure AD-prenumeration
- En ProMaster (genom Inlogik) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ProMaster (genom Inlogik) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Att lägga till ProMaster (genom Inlogik) från galleriet

Om du vill konfigurera integreringen av ProMaster (genom Inlogik) till Azure AD, måste du lägga till ProMaster (genom Inlogik) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ProMaster (genom Inlogik) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ProMaster (genom Inlogik)** väljer **ProMaster (genom Inlogik)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ProMaster (genom Inlogik) i resultatlistan](./media/promaster-tutorial/tutorial_promaster_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ProMaster (genom Inlogik) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i ProMaster (genom Inlogik) till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ProMaster (genom Inlogik) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ProMaster (genom Inlogik), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ProMaster (genom Inlogik)](#create-a-promaster-by-inlogik-test-user)**  – du har en motsvarighet för Britta Simon i ProMaster (genom Inlogik) som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för ProMaster (genom Inlogik).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ProMaster (genom Inlogik):**

1. I Azure-portalen på den **ProMaster (genom Inlogik)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/promaster-tutorial/tutorial_promaster_samlbase.png)

3. På den **ProMaster (genom Inlogik)-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![ProMaster (genom Inlogik)-domän och URL: er med enkel inloggning för information](./media/promaster-tutorial/tutorial_promaster_url1.png)

    a. I den **identifierare** textrutan använder något av följande URL-mönster:
    | |
    | - |-|
    |  `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. I den **svars-URL** textrutan använder något av följande URL-mönster:
    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![ProMaster (genom Inlogik)-domän och URL: er med enkel inloggning för information](./media/promaster-tutorial/tutorial_promaster_url2.png)

    I den **inloggnings-URL** textrutan använder något av följande URL-mönster:
    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs `|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [ProMaster (genom Inlogik) klient-supportteamet](mailto:michael.boldiston@inlogik.com) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på co-knappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/promaster-tutorial/tutorial_promaster_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/promaster-tutorial/tutorial_general_400.png)

7. Att konfigurera enkel inloggning på **ProMaster (genom Inlogik)** sida, som du behöver skicka den **Appfederationsmetadata** till [ProMaster (genom Inlogik) supportteamet](mailto:michael.boldiston@inlogik.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/promaster-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/promaster-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/promaster-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/promaster-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-promaster-by-inlogik-test-user"></a>Skapa en testanvändare ProMaster (genom Inlogik)

I det här avsnittet skapar du en användare som kallas Britta Simon i ProMaster (genom Inlogik). Arbeta med [ProMaster (genom Inlogik) supportteamet](mailto:michael.boldiston@inlogik.com) att lägga till användare i ProMaster (genom Inlogik)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ProMaster (genom Inlogik).

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon ProMaster (genom Inlogik), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **ProMaster (genom Inlogik)**.

    ![Länken ProMaster (genom Inlogik) i listan med program](./media/promaster-tutorial/tutorial_promaster_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ProMaster (genom Inlogik) i åtkomstpanelen du bör få automatiskt loggat in på programmets ProMaster (genom Inlogik).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promaster-tutorial/tutorial_general_01.png
[2]: ./media/promaster-tutorial/tutorial_general_02.png
[3]: ./media/promaster-tutorial/tutorial_general_03.png
[4]: ./media/promaster-tutorial/tutorial_general_04.png

[100]: ./media/promaster-tutorial/tutorial_general_100.png

[200]: ./media/promaster-tutorial/tutorial_general_200.png
[201]: ./media/promaster-tutorial/tutorial_general_201.png
[202]: ./media/promaster-tutorial/tutorial_general_202.png
[203]: ./media/promaster-tutorial/tutorial_general_203.png

