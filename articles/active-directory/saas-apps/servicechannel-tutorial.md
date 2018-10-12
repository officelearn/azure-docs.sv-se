---
title: 'Självstudier: Azure Active Directory-integration med ServiceChannel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ServiceChannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.openlocfilehash: 22e72d454626941b932cef2750cba008da20a214
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114968"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Självstudier: Azure Active Directory-integration med ServiceChannel

I den här självstudien får du lära dig hur du integrerar ServiceChannel med Azure Active Directory (AD Azure).

Integrera ServiceChannel med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ServiceChannel
- Du kan aktivera användarna att automatiskt få loggat in på ServiceChannel (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ServiceChannel, behöver du följande objekt:

- En Azure AD-prenumeration
- En ServiceChannel enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ServiceChannel från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-servicechannel-from-the-gallery"></a>Att lägga till ServiceChannel från galleriet
För att konfigurera integrering av ServiceChannel i Azure AD, som du behöver lägga till ServiceChannel från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ServiceChannel från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **ServiceChannel**.

    ![Skapa en Azure AD-användare för testning](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

1. I resultatpanelen väljer **ServiceChannel**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ServiceChannel baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ServiceChannel är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ServiceChannel upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i ServiceChannel.

Om du vill konfigurera och testa Azure AD enkel inloggning med ServiceChannel, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare ServiceChannel](#creating-a-servicechannel-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt program för ServiceChannel.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ServiceChannel:**

1. I hanteringsportalen för Azure på den **ServiceChannel** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

1. På den **ServiceChannel domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. I den **identifierare** textrutan Ange värdet som: `http://adfs.<domain>.com/adfs/service/trust`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera dessa värden med de faktiska identifierare och svars-URL. Här föreslår vi att du för att använda det unika värdet av strängen i identifierare. Kontakta [ServiceChannel supportteamet](https://servicechannel.zendesk.com/hc/en-us) att hämta dessa värden.

1. Programmets ServiceChannel förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. **NameIdentifier (användaridentifierare)** är bara obligatoriskt anspråket och standardvärdet är **user.userprincipalname** men ServiceChannel förväntar sig detta mappas med **user.mail**. Om du planerar att aktivera etablering av Just-In-Time-användare, bör du till följande anspråk som visas nedan. **Rollen** anspråk måste mappas till **user.assignedroles** som innehåller rollen för användaren.  

    Du kan läsa ServiceChannel guide [här](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) för mer information om anspråk.
    
    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Se [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md) att lära dig hur du konfigurerar **rollen** i Azure AD.

1. I **användarattribut** klickar du på **visa och redigera alla andra användarattribut** och ange attribut.

    | Attributnamn | Attributvärde |
    | --- | --- |    
    | Roll| User.assignedroles |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**
    
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

1. Klicka på **Spara**.

    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial_general_400.png)

1. På den **ServiceChannel Configuration** klickar du på **konfigurera ServiceChannel** att öppna **konfigurera inloggning** fönster. Observera den **SAML Enitity ID** från den **Snabbreferens** avsnittet.

1. Att konfigurera enkel inloggning på **ServiceChannel** sida, som du behöver skicka de hämtade **certifikat (Base64)** och **SAML entitets-ID** till [ServiceChannel supportteam](https://servicechannel.zendesk.com/hc/en-us). De kommer har konfigurerat för att få SAML SSO-anslutningen korrekt inställda på båda sidorna.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/servicechannel-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/servicechannel-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/servicechannel-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/servicechannel-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 

### <a name="creating-a-servicechannel-test-user"></a>Skapa en ServiceChannel testanvändare

Programmet stöder bara i tid användaretablering och -autentiserade användare kommer att skapas i programmet automatiskt. För fullständig användaretablering, kontakta [ServiceChannel support-teamet](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst ServiceChannel.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon ServiceChannel, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ServiceChannel**.

    ![Konfigurera enkel inloggning](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ServiceChannel i åtkomstpanelen du bör få automatiskt loggat in på programmets ServiceChannel.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png