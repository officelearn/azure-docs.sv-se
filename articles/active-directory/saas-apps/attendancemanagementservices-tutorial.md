---
title: 'Självstudier: Azure Active Directory-integrering med närvaro hanteringstjänster | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och närvaro hanteringstjänster.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: 331a8d07004a8915d27b28ca0fba940b1aa3ab72
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164817"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Självstudier: Azure Active Directory-integrering med närvaro hanteringstjänster

I den här självstudien får du lära dig hur du integrerar närvaro Management Services med Azure Active Directory (AD Azure).

Integrera närvaro Management Services med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till närvaro hanteringstjänster.
- Du kan aktivera användarna att automatiskt få loggat in på närvaro Management Services (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med närvaro Management Services, behöver du följande objekt:

- En Azure AD-prenumeration
- En närvaro hanteringstjänster enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till närvaro hanteringstjänster från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-attendance-management-services-from-the-gallery"></a>Att lägga till närvaro hanteringstjänster från galleriet
För att konfigurera integrering av närvaro hanteringstjänster i Azure AD, som du behöver lägga till närvaro hanteringstjänster från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till närvaro hanteringstjänster från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **närvaro hanteringstjänster**väljer **närvaro hanteringstjänster** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Närvaro hanteringstjänster i resultatlistan](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med närvaro hanteringstjänster baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i närvaro Management Services är en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i närvaro Management Services upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med närvaro Management Services, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare närvaro hanteringstjänster](#create-an-attendance-management-service-test-user)**  – du har en motsvarighet för Britta Simon i närvaro hanteringstjänster som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din närvaro Management Services-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med närvaro Management Services:**

1. I Azure-portalen på den **närvaro hanteringstjänster** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

1. På den **närvaro Management Services-domän och URL: er** avsnittet, utför följande steg:

    ![Närvaro Management Services-domän och URL: er med enkel inloggning för information](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://id.obc.jp/<tenant information >/`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [närvaro Management Services Client supportteamet](https://www.obcnet.jp/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

1. På den **närvaro Management Services-konfiguration** klickar du på **konfigurera närvaro hanteringstjänster** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Närvaro Management Services-konfiguration](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

1. I ett annat webbläsarfönster inloggning till webbplatsen för företagets närvaro hanteringstjänster som administratör.

1. Klicka på **SAML-autentisering** under den **Security hanteringsavsnittet**.

    ![Närvaro Management Services-konfiguration](./media/attendancemanagementservices-tutorial/user1.png)

1. Utför följande steg:

    ![Närvaro Management Services-konfiguration](./media/attendancemanagementservices-tutorial/user2.png)

    a. Välj **Använd SAML-autentisering**.

    b. I den **identifierare** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen. 

    c. I den **autentisering slutpunkts-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    d. Klicka på **Välj en fil** att ladda upp det certifikat som du har hämtat från Azure AD.

    e. Välj **inaktivera lösenordsautentisering**.

    f. Klicka på **registrering**

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen! När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Skapa en testanvändare närvaro hanteringstjänster

Om du vill aktivera Azure AD-användare att logga in på närvaro Management Services, måste de etableras i närvaro hanteringstjänster. När det gäller närvaro Management Services är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen för företagets närvaro hanteringstjänster som administratör.

1. Klicka på **Användarhantering** under den **Security hanteringsavsnittet**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user5.png)

1. Klicka på **ny regler inloggning**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user3.png)

1. I den **OBCiD information** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user4.png)

    a. I den **OBCiD** textrutan typ e-postmeddelandet av användare som **BrittaSimon@contoso.com**.

    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    c. Klicka på **registrering**


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till närvaro hanteringstjänster.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon närvaro Management Services, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **närvaro hanteringstjänster**.

    ![Länken närvaro hanteringstjänster i listan med program](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen närvaro hanteringstjänster i åtkomstpanelen du bör få automatiskt loggat in på ditt närvaro Management Services-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png

