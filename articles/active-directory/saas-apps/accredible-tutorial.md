---
title: 'Självstudier: Azure Active Directory-integrering med Accredible | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Accredible.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7284dfb6-df62-41f1-a4a4-1b8322b7ef44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: jeedes
ms.openlocfilehash: c5eaa0944210e701c306180709bf97abdbb3b9e3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151285"
---
# <a name="tutorial-azure-active-directory-integration-with-accredible"></a>Självstudier: Azure Active Directory-integrering med Accredible

I den här självstudien får du lära dig hur du integrerar Accredible med Azure Active Directory (AD Azure).

Integrera Accredible med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Accredible.
- Du kan aktivera användarna att automatiskt få loggat in på Accredible (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Accredible, behöver du följande objekt:

- En Azure AD-prenumeration
- En Accredible enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Accredible från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-accredible-from-the-gallery"></a>Att lägga till Accredible från galleriet
För att konfigurera integrering av Accredible i Azure AD, som du behöver lägga till Accredible från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Accredible från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Accredible**väljer **Accredible** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Accredible i resultatlistan](./media/accredible-tutorial/tutorial_accredible_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Accredible baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Accredible är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Accredible upprättas.

I Accredible, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Accredible, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Accredible](#create-an-accredible-test-user)**  – du har en motsvarighet för Britta Simon i Accredible som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Accredible program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Accredible:**

1. I Azure-portalen på den **Accredible** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/accredible-tutorial/tutorial_accredible_samlbase.png)

3. På den **Accredible domän och URL: er** avsnittet, utför följande steg:

    ![Accredible domän och URL: er med enkel inloggning för information](./media/accredible-tutorial/tutorial_accredible_url.png)

    a. I den **identifierare** textrutan anger du ett URL:
    | |
    |--|
    |  `https://api.accredible.com/sp/admin/accredible` |
    | `https://api.accredible.com/sp/user/accredible` |

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://api.accredible.com/v1/saml/admin/<Unique id>/consume`

    > [!NOTE] 
    > Värdet för svars-URL:en är inte verkligt. Använda ID-värde enligt rollen för användaren. Varje kund har en unik svars-URL beroende på deras ID. Kontakta [Accredible supportteamet](mailto:support@accredible.com) att hämta dessa värden.
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/accredible-tutorial/tutorial_accredible_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/accredible-tutorial/tutorial_general_400.png)

6. Att konfigurera enkel inloggning på **Accredible** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Accredible supportteamet](mailto:support@accredible.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/accredible-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/accredible-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/accredible-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/accredible-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-an-accredible-test-user"></a>Skapa en Accredible testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Accredible. Du behöver skicka användarens emailid till [Accredible supportteamet](mailto:support@accredible.com), och sedan de kontrollera e-postmeddelandet och skicka inbjudan e-postmeddelandet så att du kan lägga till användare i accredible-plattformen.
 
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Accredible.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Accredible, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Accredible**.

    ![Länken Accredible i listan med program](./media/accredible-tutorial/tutorial_accredible_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Accredible i åtkomstpanelen du bör få automatiskt loggat in på ditt Accredible program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/accredible-tutorial/tutorial_general_01.png
[2]: ./media/accredible-tutorial/tutorial_general_02.png
[3]: ./media/accredible-tutorial/tutorial_general_03.png
[4]: ./media/accredible-tutorial/tutorial_general_04.png

[100]: ./media/accredible-tutorial/tutorial_general_100.png

[200]: ./media/accredible-tutorial/tutorial_general_200.png
[201]: ./media/accredible-tutorial/tutorial_general_201.png
[202]: ./media/accredible-tutorial/tutorial_general_202.png
[203]: ./media/accredible-tutorial/tutorial_general_203.png

