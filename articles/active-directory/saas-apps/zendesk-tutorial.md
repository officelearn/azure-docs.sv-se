---
title: 'Självstudier: Azure Active Directory-integration med Zendesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268182"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Självstudier: Azure Active Directory-integration med Zendesk

I den här självstudien får du lära dig hur du integrerar Zendesk med Azure Active Directory (AD Azure).

Integrera Zendesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zendesk.
- Du kan aktivera användarna att automatiskt få loggat in på Zendesk (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zendesk, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett Zendesk enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Zendesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zendesk-from-the-gallery"></a>Att lägga till Zendesk från galleriet

För att konfigurera integrering av Zendesk i Azure AD, som du behöver lägga till Zendesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zendesk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. I sökrutan skriver **Zendesk**väljer **Zendesk** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zendesk baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Zendesk är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Zendesk upprättas.

I Zendesk, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zendesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Zendesk](#create-a-zendesk-test-user)**  – du har en motsvarighet för Britta Simon i Zendesk som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zendesk-program.

**Om du vill konfigurera Azure AD enkel inloggning med Zendesk, utför du följande steg:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Zendesk** application integration markerar **enkel inloggning**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    a. I den **inloggnings-URL** text skriver en URL med hjälp av följande mönster: `https://<subdomain>.zendesk.com`.

    b. I den **identifierare** text skriver en URL med hjälp av följande mönster: `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Zendesk klienten supportteamet](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) att hämta dessa värden.

6. Zendesk förväntar sig SAML-intyg i ett visst format. Det finns inga obligatoriska SAML-attribut, men du kan också lägga till ett attribut från **användarattribut** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut** dialogrutan.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. I den **användaranspråk** avsnittet på den **användarattribut** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    a. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Lämna den **Namespace** tom.

    d. Välj källa som **attributet**.
    
    e. Från den **källattribut** anger attributvärdet som visas för den raden.
    
    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    > [!NOTE]
    > Du kan använda Tilläggsattribut för att lägga till attribut som inte ingår i Azure AD som standard. Klicka på [användarattribut som kan anges i SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) för att hämta den fullständiga listan med attribut SAML som **Zendesk** accepterar.

8. I SAML-signeringscertifikat, i den **SAML-signeringscertifikat** avsnittet, kopiera den **tumavtryck**, och spara den på din dator.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Välj lämpligt alternativ för **signering alternativet** om det behövs.

    b. Välj lämpligt alternativ för **signering algoritmen** om det behövs.

    c. Klicka på **Spara**

9. På den **konfigurera Zendesk** klickar du på **visa stegvisa instruktioner** att öppna **konfigurera inloggning** fönster. Kopiera den nedan URL: er, från den **Snabbreferens avsnittet.**

    Observera att URL: en kan stå följande:

    a. URL för SAML enkel inloggning

    b. Enhets-id

    c. Utloggnings-URL

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Det finns två sätt som kan du konfigurera Zendesk - automatiskt och manuellt.
  
11. Om du vill automatisera konfigurationen i Zendesk, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![image](./media/zendesk-tutorial/install_extension.png)

12. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Zendesk** omdirigerar dig till Zendesk-programmet. Ange administratörsautentiseringsuppgifter för att logga in på Zendesk därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 13.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Om du vill konfigurera Zendesk manuellt, öppna ett nytt webbläsarfönster och logga på din Zendesk företagets webbplats som administratör och utför följande steg:

    * Klicka på **Admin**.

    * I det vänstra navigeringsfönstret klickar du på **inställningar**, och klicka sedan på **Security**.

    * På den **Security** utför följande steg:

      ![Security](././media/zendesk-tutorial/ic773089.png "säkerhet")

      ![Enkel inloggning](././media/zendesk-tutorial/ic773090.png "enkel inloggning")

      a. Klicka på den **Admin & agenter** fliken.

      b. Välj **enkel inloggning (SSO) och SAML**, och välj sedan **SAML**.

      c. I **URL för SAML SSO** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

      d. I **Remote URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

      e. I **certifikat fingeravtryck** textrutan klistra in den **tumavtryck** värdet för certifikat som du har kopierat från Azure-portalen.

      f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="create-a-zendesk-test-user"></a>Skapa en testanvändare i Zendesk

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Zendesk. Zendesk stöder automatisk användaretablering, vilket är som standard aktiverat. Du hittar mer information om [här](Zendesk-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du vill skapa användare manuellt gör du följande:**

> [!NOTE]
> **Slutanvändaren** konton tillhandahålls automatiskt när du loggar in. **Agenten** och **Admin** kontona måste tillhandahållas manuellt i **Zendesk** innan du loggar in.

1. Logga in på din **Zendesk** klient.

2. Välj den **Kundlista** fliken.

3. Välj den **användaren** och på **Lägg till**.

    ![Lägg till användare](././media/zendesk-tutorial/ic773632.png "Lägg till användare")
4. Skriv den **namn** och **e-post** på ett befintligt Azure AD-konto du vill etablera och klicka sedan på **spara**.

    ![Ny användare](././media/zendesk-tutorial/ic773633.png "ny användare")

> [!NOTE]
> Du kan använda Zendesk användaren-konto skapas verktyg från någon annan eller API: er fås från Zendesk att etablera AAD-användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Zendesk.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. I listan med program väljer **Zendesk**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zendesk i åtkomstpanelen du bör få automatiskt loggat in på ditt Zendesk-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](zendesk-provisioning-tutorial.md)
