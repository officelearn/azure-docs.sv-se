---
title: 'Självstudier: Azure Active Directory-integration med arbetsplats av Facebook | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbetsplats med Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 2e072a27087f90bddd3f7c416904758e40c2f6b9
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425198"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Självstudier: Azure Active Directory-integration med arbetsplats med Facebook

Lär dig hur du integrerar arbetsplats av Facebook med Azure Active Directory (AD Azure) i den här självstudien.

Integrera arbetsplats av Facebook med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till arbetsplats med Facebook
- Du kan aktivera användarna att automatiskt få loggat in på arbetsplatsen av Facebook (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med arbetsplats med Facebook, behöver du följande objekt:

- En Azure AD-prenumeration
- En arbetsplats av Facebook enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook har två produkter, arbetsplats Standard (kostnadsfri) och arbetsplats Premium (betalas). Alla arbetsplats Premium-klientorganisation kan konfigurera SCIM- och SSO-integrering med inga andra effekter kostnaden eller licenser som krävs. Enkel inloggning och SCIM är inte tillgängliga i arbetsplats standardinstanser.

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till arbetsplatsen av Facebook från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Att lägga till arbetsplatsen av Facebook från galleriet
Om du vill konfigurera integreringen av arbetsplats av Facebook till Azure AD, som du behöver lägga till arbetsplatsen av Facebook från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till arbetsplatsen av Facebook från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **arbetsplats av Facebook**.

    ![Skapa en Azure AD-användare för testning](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

1. I resultatpanelen väljer **arbetsplats av Facebook**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med arbetsplats av Facebook baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i arbetsplats av Facebook till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i arbetsplats av Facebook upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** på arbetsplats med Facebook.

Om du vill konfigurera och testa Azure AD enkel inloggning med arbetsplats med Facebook, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Konfigurera omautentisering frekvens](#configuring-reauthentication-frequency)**  – om du vill konfigurera arbetsplats att begära en SAML-kontroll.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en arbetsplats av Facebook testanvändare](#creating-a-workplace-by-facebook-test-user)**  – du har en motsvarighet för Britta Simon i arbetsplats av Facebook som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din arbetsplats med Facebook-programmet.

**Om du vill konfigurera Azure AD enkel inloggning med arbetsplats med Facebook, utför du följande steg:**

1. I Azure-portalen på den **arbetsplats av Facebook** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

1. På den **arbetsplats med Facebook-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<instancename>.facebook.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Visa autentiseringssidan på arbetsplatsen företagets instrumentpanelen för rätt värden för din arbetsplats-community. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

1. På den **arbetsplats av Facebook-konfigurationen** klickar du på **konfigurera arbetsplats av Facebook** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/workplacebyfacebook-tutorial/config.png) 

1. I ett annat webbläsarfönster, logga in på din arbetsplats av Facebook företagets plats som administratör.
  
   > [!NOTE] 
   > Som en del av SAML-autentiseringsprocessen utnyttjar arbetsplats frågesträngar för upp till 2,5 kB i storlek för att skicka parametrar till Azure AD.

1. I den **Administrationspanel**går du till den **Security** fliken **autentisering**.

1. Under **SAML-autentisering**väljer **SSO endast** från den nedrullningsbara listan.

1. Indatavärden kopieras från **arbetsplats av Facebook-konfigurationen** på Azure portal till motsvarande fält:

    *   I **URL för SAML** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.
    *   I **SAML utfärdar-URL-textrutan**, klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.
    *   I **SAML-utloggning omdirigera** (valfritt), klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.
    *   Öppna din **Base64-kodade certifikat** i anteckningar som hämtats från Azure-portalen, kopierar du innehållet i den till Urklipp och klistra in den till den **SAML-certifikatet** textrutan.

1. Du kan behöva ange publik URL, mottagaren URL: en, och URL: en för ACS (Konsumenttjänst för försäkran) visas under den **SAML-konfiguration** avsnittet.

1. Rulla till slutet av avsnittet och klicka på den **Test SSO** knappen. Detta resulterar i ett popup-fönster visas med Azure AD-inloggningssidan visas. Ange dina autentiseringsuppgifter i som vanligt för att autentisera. 

    **Felsökning:** kontrollera e-postadressen som returneras tillbaka från Azure AD är samma som arbetsplatskontot som du har loggat in med.

1. När testet har slutförts, bläddra längst ned på sidan och klicka på den **spara** knappen.

1. Alla användare som använder arbetsplats visas nu med Azure AD-inloggningssidan för autentisering.

1. **SAML utloggning omdirigera (valfritt)** - 

    Du kan välja att du kan också konfigurera en SAML utloggnings-Url, som kan användas för att den pekar på sidan för Azure AD logga ut. När den här inställningen är aktiverad och konfigurerad, kommer inte längre användaren omdirigeras till sidan Arbetsyta utloggning. I stället omdirigeras användaren till den url som har lagts till i SAML-utloggning omdirigerings-inställningen.

### <a name="configuring-reauthentication-frequency"></a>Konfigurera omautentisering frekvens

Du kan konfigurera arbetsplats för att fråga efter en SAML-kontroll varje dag, tre dagar, vecka, vecka, månad eller aldrig.

> [!NOTE] 
>Minimivärdet för SAML-kontrollen på mobilprogram anges till en vecka.

Du kan också tvinga en SAML för alla användare med hjälp av knappen Återställ: kräver SAML-autentisering för alla användare nu.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Skapa en arbetsplats av Facebook testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i arbetsplats med Facebook. Arbetsplats av Facebook stöder just-in-time-etablering, som är aktiverat som standard.

Det finns inga åtgärder åt dig i det här avsnittet. Om en användare inte finns i arbetsplats med Facebook, skapas en ny när du försöker få åtkomst till arbetsplats med Facebook.

>[!Note]
>Om du vill skapa en användare manuellt, kontakta [arbetsplats med Facebook-klientens support-teamet](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till arbetsplats med Facebook.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon arbetsplats med Facebook, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **arbetsplats av Facebook**.

    ![Konfigurera enkel inloggning](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Öppna panelen om du vill testa dina inställningar för enkel inloggning.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
