---
title: 'Självstudier: Azure Active Directory-integration med Slack | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b742f3eb9124093bcf0c3c912bbae0367cdcce56
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053847"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Självstudier: Azure Active Directory-integration med Slack

I den här självstudien får du lära dig hur du integrerar Slack med Azure Active Directory (AD Azure).

Integrera Slack med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Slack
- Du kan aktivera användarna att automatiskt få loggat in på Slack (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Slack, behöver du följande objekt:

- En Azure AD-prenumeration
- En Slack enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Slack från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-slack-from-the-gallery"></a>Att lägga till Slack från galleriet
Om du vill konfigurera integreringen av Slack i Azure AD, som du behöver lägga till Slack från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Slack från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Slack**.

    ![Skapa en Azure AD-användare för testning](./media/slack-tutorial/tutorial_slack_search.png)

1. I resultatpanelen väljer **Slack**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Slack baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Slack är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Slack upprättas.

I Slack, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Slack, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Slack testanvändare](#creating-a-slack-test-user)**  – du har en motsvarighet för Britta Simon i Slack som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Slack-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Slack:**

1. I Azure-portalen på den **Slack** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. På den **Slack domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.slack.com`

    b. I den **identifierare** textrutan uppdatera värdet med loggar på URL: en. Det här är din arbetsyta-domän. Exempel: `https://contoso.slack.com`

1. Slack program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Om du har användare som har tilldelats **e-postadress** finns inte på en Office 365-licens, den **User.Email** anspråk visas inte i SAML-Token. I dessa fall kan vi föreslår att du använder **user.userprincipalname** som den **User.Email** attributet värde som ska mappas som **Unik identifierare** i stället.

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan **user.mail** som **användaridentifierare** och för varje rad som visas i i tabellen nedan, utför följande steg:
    
    | Attributnamn | Attributvärde |
    | --- | --- |
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | User.Email | User.Mail |  
    | User.Username | User.userPrincipalName |

    a. Klicka på **attributet** att öppna **redigera attributet** dialogrutan rutan och utför följande steg:

    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    b. Från den **värdet** väljer attribut-värde som visas för den raden.

    c. Lämna den **Namespace** tom.

    d. Klicka på **OK**

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_general_400.png)

1. På den **Slack Configuration** klickar du på **konfigurera Slack** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_configure.png)

1. I ett annat webbläsarfönster logga du in på din Slack företagets webbplats som administratör.

1. Gå till **Microsoft Azure AD** och gå till **Teaminställningar**.

     ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial_slack_001.png)

1. I den **Teaminställningar** klickar du på den **autentisering** fliken och klicka sedan på **Ändringsinställningar**.

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial_slack_002.png)

1. På den **SAML autentiseringsinställningar** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial_slack_003.png)

    a.  I den **SAML 2.0 slutpunkt (HTTP)** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    b.  I den **Providerutgivare** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    c.  Öppna din hämtade certifikatfil i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **offentligt certifikat** textrutan.

    d. Konfigurera de ovanstående tre inställningarna efter behov för din Slack-teamet. Mer information om inställningarna hittar du den **Slack's SSO-konfigurationsguide** här. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klicka på **spara konfigurationen**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/slack-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/slack-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/slack-tutorial/create_aaduser_03.png)

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/slack-tutorial/create_aaduser_04.png)

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-slack-test-user"></a>Skapa en Slack testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Slack. Slack stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Slack om det inte finns ännu. Slack stöder även automatisk användaretablering, du kan hitta mer information om [här](slack-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta [Slack supportteamet](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Slack.

![Tilldela användare][200]

**Om du vill tilldela Britta Simon Slack, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Slack**.

    ![Konfigurera enkel inloggning](./media/slack-tutorial/tutorial_slack_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Slack i åtkomstpanelen, du bör få automatiskt loggat in på din Slack-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
