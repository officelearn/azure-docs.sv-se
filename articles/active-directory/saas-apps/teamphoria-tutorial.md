---
title: 'Självstudier: Azure Active Directory-integrering med Teamphoria | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: d7a0523a1e436a086be90896da4d16fab55ce9ab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174456"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Självstudier: Azure Active Directory-integrering med Teamphoria

I den här självstudien får du lära dig hur du integrerar Teamphoria med Azure Active Directory (AD Azure).

Integrera Teamphoria med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Teamphoria
- Du kan aktivera användarna att automatiskt få loggat in på Teamphoria (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Teamphoria, behöver du följande objekt:

- En Azure AD-prenumeration
- En Teamphoria enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Teamphoria från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-teamphoria-from-the-gallery"></a>Att lägga till Teamphoria från galleriet
För att konfigurera integrering av Teamphoria i Azure AD, som du behöver lägga till Teamphoria från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Teamphoria från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Teamphoria**.

    ![Skapa en Azure AD-användare för testning](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

1. I resultatpanelen väljer **Teamphoria**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Teamphoria baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Teamphoria är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Teamphoria upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Teamphoria, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Teamphoria](#creating-a-teamphoria-test-user)**  – du har en motsvarighet för Britta Simon i Teamphoria som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Teamphoria program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Teamphoria:**

1. I Azure-portalen på den **Teamphoria** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

1. På den **Teamphoria domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    I den **inloggnings-URL** textrutan anger du URL med hjälp av följande mönster: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Du måste uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Teamphoria klienten supportteamet](https://www.teamphoria.com/) att hämta inloggnings URL.

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatet på datorn.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/tutorial_general_400.png)

1. På den **Teamphoria Configuration** klickar du på **konfigurera Teamphoria** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

1. Att konfigurera enkel inloggning på **Teamphoria** sida, logga in på ditt Teamphoria program som administratör.

1. Gå till **ADMINISTRATÖRSINSTÄLLNINGAR** alternativet i det vänstra verktygsfältet och på fliken Konfigurera klickar du på **enkel inloggning** att öppna fönstret SSO-konfiguration.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Klicka på **lägga till nya IDENTITETSPROVIDER** alternativet i det övre högra hörnet för att öppna formuläret för att lägga till inställningar för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Ange information i fälten som beskrivs nedan-

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **VISNINGSNAMN**: Ange visningsnamnet för plugin-programmet på sidan.

    b. **KNAPPNAMN**: Namnet på fliken som visas på inloggningssidan för att logga in via enkel inloggning.

    c. **CERTIFIKATET**: Öppna certifikatet hämtade tidigare från Azure-portalen i anteckningar, kopiera innehållet i samma och klistra in det här i rutan.

    d. **STARTPUNKTEN**: Klistra in den **SAML enkel inloggning för tjänst-URL** kopierade tidigare från Azure-portalen.

    e. Växla kan **på** och klicka på **spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/teamphoria-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/teamphoria-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/teamphoria-tutorial/create_aaduser_03.png)

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-teamphoria-test-user"></a>Skapa en Teamphoria testanvändare

För att aktivera Azure AD-användare att logga in på Teamphoria, måste de etableras i Teamphoria. När det gäller Teamphoria är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Teamphoria företag som administratör.

1. Klicka på **ADMIN** inställningar i det vänstra verktygsfältet och under den **hantera** fliken klickar du på på **användare** att öppna sidan för användare.

    ![Lägga till medarbetare](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klicka på den **manuell bjuda in** alternativet.

    ![Bjuda in](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. På den här sidan kan du utföra följande åtgärd.
    
    ![Bjuda in](./media/teamphoria-tutorial/manual_user_invite.png)

    a. I den **e-postadress** textrutan den **e-postadress** av BrittaSimon.

    b. I den **Förnamn** textrutan typ **Britta**.

    c. I den **efternamn** textrutan typ **Simon**.

    d. Klicka på **inbjudan 1 användare**. Användare måste acceptera inbjudan att skapas i systemet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Teamphoria.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon Teamphoria, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **Teamphoria**.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

Öppna panelen om du vill testa dina inställningar för enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
