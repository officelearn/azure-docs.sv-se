---
title: 'Självstudier: Azure Active Directory-integrering med Expensify | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: 1f69ad3045693e1ca826b6e04443f97ac30bc377
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156946"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Självstudier: Azure Active Directory-integrering med Expensify

I den här självstudien får du lära dig hur du integrerar Expensify med Azure Active Directory (AD Azure).

Integrera Expensify med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Expensify.
- Du kan aktivera användarna att automatiskt få loggat in på Expensify (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Expensify, behöver du följande objekt:

- En Azure AD-prenumeration
- En Expensify enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Expensify från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-expensify-from-the-gallery"></a>Att lägga till Expensify från galleriet

För att konfigurera integrering av Expensify i Azure AD, som du behöver lägga till Expensify från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Expensify från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/expensify-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/expensify-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/expensify-tutorial/a_new_app.png)

4. I sökrutan skriver **Expensify**väljer **Expensify** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Expensify baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Expensify är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Expensify upprättas.

I Expensify, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Expensify, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Expensify](#create-an-expensify-test-user)**  – du har en motsvarighet för Britta Simon i Expensify som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Expensify program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Expensify:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Expensify** application integration markerar **enkel inloggning**.

    ![image](./media/expensify-tutorial/b1_b2_select_sso.png)

2. Klicka på **ändra enkel inloggningsläge** på skärmen för att välja den **SAML** läge.

      ![image](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I den **inloggnings-URL** anger en URL som: `https://www.expensify.com/authentication/saml/login`

    b. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.<companyname>.expensify.com`

    ![image](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Ersätt den <companyname> delen av den URL-identifierare med din företagsdomän. Se exempel på `https://contoso.expensify.com` ovan. I Expensify, det här är namnet på din domän som det visas **Inställningar > domän kontroll**.

    ![Expensify domäninformation](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned rätt certifikat som uppfyller dina krav och spara den på din dator.

    ![image](./media/expensify-tutorial/certificatebase64.png)

7. Om du vill aktivera enkel inloggning i Expensify, måste du först aktivera **Domain Control** i programmet. Du kan aktivera domän kontroll i programmet med hjälp av stegen [här](https://help.expensify.com/domain-control). För ytterligare support, arbeta med [Expensify klienten supportteamet](mailto:help@expensify.com). När du har domän kontroll aktiverad kan du följa dessa steg:
   
    ![Konfigurera enkel inloggning](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Logga in på ditt Expensify program.
    
    b. I den vänstra rutan klickar du på **inställningar** och gå till **SAML**.
    
    c. Visa/Dölj de **SAML-inloggningen** beroende på **aktiverad**.
    
    d. Öppna den hämtade Federationsmetadata från Azure AD i anteckningar, kopiera innehållet och klistra in den i den **identitet providern Metadata** textrutan.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/expensify-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/expensify-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/expensify-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-an-expensify-test-user"></a>Skapa en Expensify testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Expensify. Arbeta med [Expensify klienten supportteamet](mailto:help@expensify.com) att lägga till användare i Expensify-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Expensify.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/expensify-tutorial/d_all_applications.png)

2. I listan med program väljer **Expensify**.

    ![image](./media/expensify-tutorial/d_all_proapplications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/expensify-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/expensify-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Expensify i åtkomstpanelen du bör få automatiskt loggat in på ditt Expensify program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)




