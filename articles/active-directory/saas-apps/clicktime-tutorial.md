---
title: 'Självstudier: Azure Active Directory-integrering med ClickTime | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ClickTime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: 9cce73712bd3122916d18c3ed7f7744e30d5fa1a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176530"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Självstudier: Azure Active Directory-integrering med ClickTime

I den här självstudien får du lära dig hur du integrerar ClickTime med Azure Active Directory (AD Azure).

Integrera ClickTime med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ClickTime
- Du kan aktivera användarna att automatiskt få loggat in på ClickTime (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ClickTime, behöver du följande objekt:

- En Azure AD-prenumeration
- En ClickTime enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ClickTime från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-clicktime-from-the-gallery"></a>Att lägga till ClickTime från galleriet
För att konfigurera integrering av ClickTime i Azure AD, som du behöver lägga till ClickTime från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ClickTime från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **ClickTime**väljer **ClickTime** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![ClickTime i resultatlistan](./media/clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ClickTime baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ClickTime är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ClickTime upprättas.

I ClickTime, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ClickTime, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare ClickTime](#create-a-clicktime-test-user)**  – du har en motsvarighet för Britta Simon i ClickTime som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ClickTime program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ClickTime:**

1. I Azure-portalen på den **ClickTime** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/clicktime-tutorial/tutorial_clicktime_samlbase.png)

1. På den **ClickTime domän och URL: er** avsnittet, utför följande steg:

    ![ClickTime domän och URL: er med enkel inloggning för information](./media/clicktime-tutorial/tutorial_clicktime_url.png)

    a. I den **identifierare** textrutan anger du ett URL: en som: `https://app.clicktime.com/sp/`
    
    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/clicktime-tutorial/tutorial_clicktime_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/clicktime-tutorial/tutorial_general_400.png)

1. På den **ClickTime Configuration** klickar du på **konfigurera ClickTime** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![ClickTime konfiguration](./media/clicktime-tutorial/tutorial_clicktime_configure.png) 

1. Logga in på webbplatsen ClickTime företag som en administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp, klickar du på **inställningar**, och klicka sedan på **säkerhetsinställningar**.

1. I den **inställningar för enkel inloggning** konfiguration och utför följande steg:
   
    ![Säkerhetsinställningar](./media/clicktime-tutorial/tic777280.png "säkerhetsinställningar")
   
    a.  Välj **Tillåt** logga in med enkel inloggning (SSO) med **Azure AD**.
   
    b. I den **identitet Leverantörsslutpunkt** textrutan klistra in **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    c.  Öppna den **Base64-kodade certifikat** ned från Azure-portalen i **anteckningar**, kopiera innehållet och klistra in den i den **X.509-certifikat** textrutan.
   
    d.  Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/clicktime-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/clicktime-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.
 
    ![Knappen Lägg till](./media/clicktime-tutorial/create_aaduser_03.png) 

1. I den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/clicktime-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-clicktime-test-user"></a>Skapa en ClickTime testanvändare

För att aktivera Azure AD-användare att logga in på ClickTime, måste de etableras i ClickTime.  
När det gäller ClickTime är etablering en manuell aktivitet.

> [!NOTE]
> Du kan använda alla andra ClickTime användare konto verktyg för att skapa eller API: er som tillhandahålls av ClickTime att etablera användarkonton i Azure AD.

**Utför följande steg för att etablera ett användarkonto:**
1. Logga in på din **ClickTime** klient.
1. I verktygsfältet högst upp, klickar du på **företagets**, och klicka sedan på **personer**.
   
    ![Personer](./media/clicktime-tutorial/tic777282.png "Personer")
1. Klicka på **Person till**.
   
    ![Lägg till Person](./media/clicktime-tutorial/tic777283.png "Person till")
1. I avsnittet ny Person att utföra följande steg:
   
    ![Personer](./media/clicktime-tutorial/tic777284.png "Personer")
   
    a.  I den **fullständigt namn** textrutan typ som är fullständigt namn för användaren som **Britta Simon**. 
  
    b.  I den **e-postadress** textrutan typ e-postmeddelandet av användare som **brittasimon@contoso.com**.
       
    > [!NOTE]
    > Om du vill kan ange du ytterligare egenskaper för personobjektet.
   
    c.  Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ClickTime.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon ClickTime, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ClickTime**.

    ![ClickTimne länk i listan med program](./media/clicktime-tutorial/tutorial_clicktime_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ClickTime i åtkomstpanelen du bör få automatiskt loggat in på ditt ClickTime program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clicktime-tutorial/tutorial_general_01.png
[2]: ./media/clicktime-tutorial/tutorial_general_02.png
[3]: ./media/clicktime-tutorial/tutorial_general_03.png
[4]: ./media/clicktime-tutorial/tutorial_general_04.png

[100]: ./media/clicktime-tutorial/tutorial_general_100.png

[200]: ./media/clicktime-tutorial/tutorial_general_200.png
[201]: ./media/clicktime-tutorial/tutorial_general_201.png
[202]: ./media/clicktime-tutorial/tutorial_general_202.png
[203]: ./media/clicktime-tutorial/tutorial_general_203.png

