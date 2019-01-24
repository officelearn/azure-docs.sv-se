---
title: 'Självstudier: Azure Active Directory-integrering med Pega system | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pega system.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: f21610c0004df802d56df8cf47c3c7f082e391cd
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823274"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudier: Azure Active Directory-integrering med Pega system

I den här självstudien får du lära dig hur du integrerar Pega system med Azure Active Directory (AD Azure).

Integrera Pega system med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pega system.
- Du kan aktivera användarna att automatiskt få loggat in på Pega system (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Pega system behöver du följande objekt:

- En Azure AD-prenumeration
- En Pega system enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Pega system från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pega-systems-from-the-gallery"></a>Att lägga till Pega system från galleriet
Om du vill konfigurera integreringen av Pega system till Azure AD, som du behöver lägga till Pega system från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Pega system från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Pega system**väljer **Pega system** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Pega system i listan med resultat](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Pega system baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Pega system är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Pega system upprättas.

I Pega system, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pega system, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Pega system](#create-a-pega-systems-test-user)**  – du har en motsvarighet för Britta Simon i Pega system som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Pega system.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Pega system:**

1. I Azure-portalen på den **Pega system** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

1. På den **Pega system domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Pega system domän och URL: er med enkel inloggning för information](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Pega system domän och URL: er med enkel inloggning för information](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    I den **Vidarebefordransstatus** textrutan anger du ett URL med hjälp av följande mönster: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska identifierare, svars-URL och URL: en för Relay-tillstånd. Du hittar du värdet för identifieraren och svars-URL från Pega program som beskrivs senare i den här självstudien. Vidarebefordransstatus, kontakta [Pega system klienten supportteamet](https://www.pega.com/contact-us) att hämta värdet. 

1. Pega system programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. De här anspråken är kundspecifik och beror på dina behov. Följande valfria anspråk är exempel som som du kan konfigurera för ditt program. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. 

    ![Konfigurera enkel inloggning](./media/pegasystems-tutorial/tutorial_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | CN  | *********** |
    | e-post | *********** |
    | accessgroup | *********** |
    | organisation | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefon | *********** |

    > [!NOTE]
    > Det här är kund specifika värden. Ange din lämpliga värden.

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på **Pega system** sida, öppna den **Pega Portal** med administratörskonto i ett annat webbläsarfönster.

1. Välj **skapa** -> **SysAdmin** -> **autentiseringstjänst**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
1. Utför följande åtgärder på **skapa Aauthentication Service** skärmen:

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Välj **SAML 2.0** från typen

    b. I den **namn** textrutan Ange alla namn t.ex Azure AD SSO

    c. I den **kort beskrivning** textrutan Ange någon beskrivning  

    d. Klicka på **skapa och öppna** 
    
1. I **identitetsprovider (IdP) information** klickar du på **Import IdP metadata** och bläddra i metadatafilen som du har hämtat från Azure-portalen. Klicka på **skicka** att läsa in metadata.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
1. Det här fylls IdP-data som visas nedan.

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
1. Utför följande åtgärder på **Service Provider (SP) inställningar** avsnittet:

    ![Konfigurera enkel inloggning – knappen Spara](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopiera den **enhetens identifiering** och klistra in i Azure-portalen **identifierare** textrutan.

    b.  Kopiera den **Assertion konsument Service (ACS) plats** och klistra in i Azure-portalen **svars-URL** textrutan.

    c. Välj **inaktivera Begär signering**.

1. Klicka på **Spara**
    
> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/pegasystems-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/pegasystems-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/pegasystems-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-pega-systems-test-user"></a>Skapa en testanvändare Pega system

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pega system. Kontakta [Pega system klienten supportteamet](https://www.pega.com/contact-us) att skapa användare i Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Pega system.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Pega system, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Pega system**.

    ![Länken Pega system i listan med program](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Pega system i åtkomstpanelen du bör få automatiskt loggat in på programmets Pega system.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

