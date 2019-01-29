---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler privat administratör för användaråtkomst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privat åtkomst administratör.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 6eca534f933742e4797ca164637e7d536d194ba3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181511"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Självstudier: Azure Active Directory-integrering med Zscaler privat administratör för användaråtkomst

Lär dig hur du integrerar Zscaler privat åtkomst administratör med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Zscaler privat åtkomst administratör med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler privat åtkomst administratör.
- Du kan aktivera användarna att automatiskt få loggat in till Zscaler privat Access Administrator (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler privat åtkomst Administrator, behöver du följande objekt:

- En Azure AD-prenumeration
- En administratör för privat Zscaler enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till Zscaler privat åtkomst administratör från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Lägger till Zscaler privat åtkomst administratör från galleriet
För att konfigurera integrering av Zscaler privat åtkomst administratör i Azure AD, som du behöver lägga till Zscaler privat åtkomst administratören från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler privat åtkomst administratören från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Zscaler privat åtkomst administratören**väljer **Zscaler privat åtkomst administratören** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Zscaler privat administratör för användaråtkomst i resultatlistan](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler privat administratör för användaråtkomst baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Zscaler privat åtkomst administratör är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Zscaler privat åtkomst administratör upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst administratör, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Zscaler privat åtkomst administratören](#create-a-zscaler-private-access-administrator-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler privat åtkomst administratören som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med Zscaler privat åtkomst administratör.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler privat åtkomst administratören:**

1. I Azure-portalen på den **Zscaler privat åtkomst administratören** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

1. På den **Zscaler privat åtkomst administratören domän och URL: er** avsnittet om du vill konfigurera programmet i **IDP** initierade läge:

    ![Zscaler privat åtkomst administratören domän och URL: er med enkel inloggning för information](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kontrollera **visa avancerade URL-inställningar**

    d. I den **RelayState** textrutan, ange ett värde: `idpadminsso`

1.  Om du vill konfigurera programmet i **SP** initierad läge utför följande steg:

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Zscaler privat åtkomst administratören supportteamet](https://help.zscaler.com/zpa-submit-ticket) att hämta dessa värden.
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster, logga in till Zscaler privat åtkomst administratören som administratör.

1. Klicka på överst **Administration** och gå till **AUTENTISERING** avsnittet klickar du på **IdP-konfigurationen**.

    ![Zscaler privat åtkomst administratör admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. I övre högra hörnet, klickar du på **lägga till IdP-konfigurationen**. 

    ![Zscaler privat åtkomst administratören addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

1. På den **lägga till IdP-konfigurationen** sidan utför följande steg:
 
    ![Zscaler privat åtkomst administratören idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klicka på **Välj fil** att ladda upp den hämta filen Metadata från Azure AD i den **IdP Metadata filöverföring** fält.

    b. Det läser de **IdP metadata** från Azure AD och fylls informationen för fält som visas nedan.

    ![Zscaler privat åtkomst administratören idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Välj **enkel inloggning** som **administratör**.

    d. Välj din domän från **domäner** fält.
    
    e. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Skapa en testanvändare Zscaler privat åtkomst administratör

De måste etableras i Zscaler privat åtkomst administratören om du vill aktivera Azure AD-användare att logga in till Zscaler privat åtkomst administratör. När det gäller Zscaler privat åtkomst administratören etablering är en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Zscaler administratör för privata företag som administratör.

1. Klicka på överst **Administration** och gå till **AUTENTISERING** avsnittet klickar du på **IdP-konfigurationen**.

    ![Zscaler privat åtkomst administratör admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. Klicka på **administratörer** från vänster sida av menyn.

    ![Administratör för Zscaler privat administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

1. I övre högra hörnet, klickar du på **Lägg till administratör**:

    ![Administratör för Zscaler privat Lägg till administratör](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

1. I den **Lägg till administratör** utför följande steg:

    ![Administratör för Zscaler privat Användaradministration](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. I den **användarnamn** textrutan Ange e-postadress för användaren som **BrittaSimon@contoso.com**.

    b. I den **lösenord** textrutan skriver du lösenordet.

    c. I den **Bekräfta lösenord** textrutan skriver du lösenordet.

    d. Välj **rollen** som **Zscaler privat åtkomst administratören**.

    e. I textrutan **E-post** anger du e-postadressen för användaren, t.ex. **BrittaSimon@contoso.com**.

    f. I den **Phone** textrutan anger du telefonnumret.

    g. I den **tidszon** textrutan Välj tidszonen.

    h. Klicka på **Spara**.  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Zscaler privat åtkomst administratör.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Zscaler privat åtkomst Administrator, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Zscaler privat åtkomst administratören**.

    ![Länken Zscaler privat administratör för användaråtkomst i listan med program](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler privat åtkomst administratör i åtkomstpanelen du bör få automatiskt loggat in på programmets Zscaler privat åtkomst administratör.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

