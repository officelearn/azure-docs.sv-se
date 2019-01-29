---
title: 'Självstudier: Azure Active Directory-integrering med Fuse | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fuse.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5ef34f58-863a-4b37-875c-e8efa3e18bb3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: jeedes
ms.openlocfilehash: d801ba8e7e720a2ef4efd67c7e08a5a6439049b4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158544"
---
# <a name="tutorial-azure-active-directory-integration-with-fuse"></a>Självstudier: Azure Active Directory-integrering med Fuse

I den här självstudien får du lära dig hur du integrerar Fuse med Azure Active Directory (AD Azure).

Integrera Fuse med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Fuse.
- Du kan aktivera användarna att automatiskt få loggat in på Fuse (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Fuse, behöver du följande objekt:

- En Azure AD-prenumeration
- En Fuse enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Fuse från galleriet
2. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-fuse-from-the-gallery"></a>Lägg till Fuse från galleriet
För att konfigurera integrering av Fuse i Azure AD, som du behöver lägga till Fuse från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Fuse från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **sammanfoga**väljer **sammanfoga** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Sammanfoga i resultatlistan](./media/fuse-tutorial/tutorial_fuse_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Fuse baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Fuse är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Fuse upprättas.

I Fuse, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Fuse, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Fuse](#create-a-fuse-test-user)**  – du har en motsvarighet för Britta Simon i Fuse som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Fuse-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Fuse:**

1. I Azure-portalen på den **sammanfoga** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/fuse-tutorial/tutorial_fuse_samlbase.png)

3. På den **sammanfoga domän och URL: er** avsnittet, utför följande steg:

    ![Sammanfoga domän och URL: er enkel inloggning för information](./media/fuse-tutorial/tutorial_fuse_url.png)
    
    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenant name>.fusionuniversal.com/`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [sammanfoga klienten supportteamet](mailto:support@fusion-universal.com) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Raw)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/fuse-tutorial/tutorial_fuse_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/fuse-tutorial/tutorial_general_400.png)

6. På den **sammanfoga Configuration** klickar du på **konfigurera sammanfoga** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Sammanfoga konfiguration](./media/fuse-tutorial/tutorial_fuse_configure.png) 

7. För att få SSO konfigurerats för ditt program kan kontakta [Fuse-supportteamet](mailto:support@fusion-universal.com) och ge dem med följande:

    * Den hämtade **certifikatfil (Raw)**
    * Den **URL för SAML enkel inloggning**
    * Den **SAML entitets-ID**
    * Den **utloggnings-URL**

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/fuse-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/fuse-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/fuse-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/fuse-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-fuse-test-user"></a>Skapa en Fuse testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Fuse. Kontakta [Fuse-supportteamet](mailto:support@fusion-universal.com) att lägga till användare i Fuse-plattformen.


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Fuse.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Fuse, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **sammanfoga**.

    ![Länken Fuse i listan med program](./media/fuse-tutorial/tutorial_fuse_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Fuse i åtkomstpanelen du bör få automatiskt loggat in på ditt Fuse-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fuse-tutorial/tutorial_general_01.png
[2]: ./media/fuse-tutorial/tutorial_general_02.png
[3]: ./media/fuse-tutorial/tutorial_general_03.png
[4]: ./media/fuse-tutorial/tutorial_general_04.png

[100]: ./media/fuse-tutorial/tutorial_general_100.png

[200]: ./media/fuse-tutorial/tutorial_general_200.png
[201]: ./media/fuse-tutorial/tutorial_general_201.png
[202]: ./media/fuse-tutorial/tutorial_general_202.png
[203]: ./media/fuse-tutorial/tutorial_general_203.png

