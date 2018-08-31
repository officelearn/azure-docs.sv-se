---
title: 'Självstudier: Azure Active Directory-integrering med Comeet rekrytering programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Comeet rekrytering programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 75f51dc9-9525-4ec6-80bf-28374f0c8adf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: jeedes
ms.openlocfilehash: 137ba7a7e82ff3e57d862868859e8049838701a3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307839"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Självstudier: Azure Active Directory-integrering med Comeet rekrytering programvara

I den här självstudien får du lära dig hur du integrerar Comeet rekrytering programvara med Azure Active Directory (AD Azure).

Integrera Comeet rekrytering programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Comeet rekrytering programvara.
- Du kan aktivera användarna att automatiskt få loggat in på Comeet rekrytering programvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Comeet rekrytering programvara, behöver du följande objekt:

- En Azure AD-prenumeration
- En Comeet rekrytering programvara enkel inloggning aktiverat prenumeration

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till Comeet rekrytering programvara från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>Lägga till Comeet rekrytering programvara från galleriet

För att konfigurera integrering av Comeet rekrytering programvara i Azure AD, som du behöver lägga till Comeet rekrytering programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Comeet rekrytering programvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Comeet rekrytering programvara**väljer **Comeet rekrytering programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Comeet rekrytering programvara i resultatlistan](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Comeet rekrytering programvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Comeet rekrytering programvara till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Comeet rekrytering programvara upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Comeet rekrytering programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Comeet rekrytering programvara](#create-a-comeet-recruiting-software-test-user)**  – du har en motsvarighet för Britta Simon Comeet rekrytering programvara som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i programmet Comeet rekrytering.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Comeet rekrytering programvara:**

1. I Azure-portalen på den **Comeet rekrytering programvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_samlbase.png)

3. På den **Comeet rekrytering programvara domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Comeet domän och URL: er med enkel inloggning för information](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url1.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svars-URL. Du får dessa värden från portalen Comeet rekrytering programvara enligt de [supportsidan](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Comeet rekrytering programvara domän och URL: er enkel inloggning för information](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url2.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://app.comeet.co`

5. Comeet rekrytering programvara förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men **Comeet rekrytering programvara** förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller Använd rätt attribut-värde baserat på konfigurationen för din organisation.

    ![Konfigurera enkel inloggning](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_attribute.png)

6. Klicka på **visa och redigera alla andra användarattribut** kryssrutan i den **användarattribut** avsnitt för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde |
    | ---------------| --------------- |
    | comeet_id | User.userPrincipalName |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver den **attributnamnet** visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_certificate.png)

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/comeetrecruitingsoftware-tutorial/tutorial_general_400.png)

9. Att konfigurera enkel inloggning på **Comeet rekrytering programvara** sida, klistra in innehållet i den hämtade XML-Metadata i Comeet rekrytering programvara, enligt den [supportsidan](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/comeetrecruitingsoftware-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/comeetrecruitingsoftware-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/comeetrecruitingsoftware-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/comeetrecruitingsoftware-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-comeet-recruiting-software-test-user"></a>Skapa en testanvändare Comeet rekrytering programvara

I det här avsnittet skapar du en användare som kallas Britta Simon Comeet rekrytering programvara. Arbeta med [Comeet rekrytering programvara supportteamet](mailto:support@comeet.co) att lägga till användare i Comeet rekrytering programvara-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Comeet rekrytering programvara.

![Tilldela rollen][200]

**Om du vill tilldela Britta Simon Comeet rekrytering programvara, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Comeet rekrytering programvara**.

    ![Länken Comeet rekrytering programvara i listan med program](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Comeet rekrytering programvara i åtkomstpanelen du bör få automatiskt loggat in på ditt Comeet rekrytering programvara.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_01.png
[2]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_02.png
[3]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_03.png
[4]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_04.png

[100]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_100.png

[200]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_200.png
[201]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_201.png
[202]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_202.png
[203]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_203.png