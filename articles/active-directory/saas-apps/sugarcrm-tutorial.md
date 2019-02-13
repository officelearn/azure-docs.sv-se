---
title: 'Självstudier: Azure Active Directory-integrering med socker CRM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och socker CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a00c9cb0ce1bd8a6f36070e81df1185ef23a307
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201779"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Självstudier: Azure Active Directory-integrering med socker CRM

I den här självstudien får du lära dig hur du integrerar socker CRM med Azure Active Directory (AD Azure).

Integrera socker CRM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till socker CRM
- Du kan aktivera användarna att automatiskt få loggat in på socker CRM (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med socker CRM, behöver du följande objekt:

- En Azure AD-prenumeration
- En socker CRM enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till socker CRM från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sugar-crm-from-the-gallery"></a>Att lägga till socker CRM från galleriet
Om du vill konfigurera integreringen av socker CRM till Azure AD, som du behöver lägga till socker CRM från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till socker CRM från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **socker CRM**.

    ![Skapa en Azure AD-användare för testning](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

1. I resultatpanelen väljer **socker CRM**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med socker CRM baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till användaren i socker CRM motsvarigheten till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i socker CRM upprättas.

I socker CRM, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med socker CRM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare socker CRM](#creating-a-sugar-crm-test-user)**  – du har en motsvarighet för Britta Simon i socker CRM som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt socker CRM-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med socker CRM:**

1. I Azure-portalen på den **socker CRM** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

1. På den **socker CRM domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [socker CRM-klienten supportteamet](https://support.sugarcrm.com/) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_general_400.png)

1. På den **socker CRM Configuration** klickar du på **konfigurera socker CRM** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen socker CRM-företag som administratör.

1. Gå till **Admin**.
   
    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. I den **Administration** klickar du på **lösenordshantering**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

1. Välj **aktivera SAML-autentisering**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

1. I den **SAML-autentisering** avsnittet, utför följande steg:
   
    ![SAML-autentisering](./media/sugarcrm-tutorial/ic795891.png "SAML-autentisering")  
 
    a. I den **inloggnings-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
  
    b. I den **SLO URL** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.
  
    c. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet i **X.509-certifikat** textrutan.
  
    d. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/sugarcrm-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sugarcrm-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/sugarcrm-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Skapa en testanvändare socker CRM

För att aktivera Azure AD-användare att logga in på socker CRM, måste de etableras till socker CRM.

När det gäller socker CRM är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **socker CRM** företagets plats som administratör.

1. Gå till **Admin**.
   
    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. I den **Administration** klickar du på **Användarhantering**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

1. Gå till **användare \> Skapa ny användare**.
   
    ![Skapa ny användare](./media/sugarcrm-tutorial/ic795894.png "Skapa ny användare")

1. På den **användarprofil** fliken, utför följande steg:
   
    ![Ny användare](./media/sugarcrm-tutorial/ic795895.png "Ny användare")

    a. Skriv den **användarnamn**, **efternamn**, och **e-postadress** av en giltig Azure Active Directory-användare i den relaterade textrutor.
  
1. Som **Status**väljer **Active**.

1. Utför följande steg på fliken lösenord:
   
    ![Ny användare](./media/sugarcrm-tutorial/ic795896.png "Ny användare")

    a. Ange lösenordet i relaterade textrutan.

    b. Klicka på **Spara**.

>[!NOTE]
>Du kan använda något annat socker CRM användaren-konto skapas verktyg eller API: er som tillhandahålls av socker CRM att etablera AAD-användarkonton. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till socker CRM.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon socker CRM, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **socker CRM**.

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen socker CRM i åtkomstpanelen du bör få automatiskt loggat in på ditt socker CRM-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

