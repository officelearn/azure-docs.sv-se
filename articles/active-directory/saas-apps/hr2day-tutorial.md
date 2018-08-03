---
title: 'Självstudier: Azure Active Directory-integration med HR2day av Merces | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HR2day av Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 984e2e9999a2aba7a595034f1fec8bafb976f310
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441623"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Självstudier: Azure Active Directory-integration med HR2day av Merces

I den här självstudien får du lära dig hur du integrerar HR2day av Merces med Azure Active Directory (AD Azure).

Integrera HR2day av Merces med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HR2day av Merces.
- Du kan aktivera användarna att få loggas in automatiskt HR2day av Merces med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HR2day av Merces, behöver du följande objekt:

- En Azure AD-prenumeration.
- En HR2day av Merces enkel inloggning aktiverat prenumeration.

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Hämta en [en månads kostnadsfri utvärderingsversion av Azure AD](https://azure.microsoft.com/pricing/free-trial/) om du inte redan har den.  

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs här består av två viktigaste byggstenarna:

1. Lägger till HR2day av Merces från galleriet.
1. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Lägg till HR2day av Merces från galleriet
Lägg till HR2day av Merces från galleriet i din lista över hanterade SaaS-appar för att konfigurera integrering av HR2day av Merces i Azure AD.

**Om du vill lägga till HR2day av Merces från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret, väljer den **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **HR2day av Merces**.

    ![Skapa en Azure AD-användare för testning](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

1. I resultatpanelen väljer **HR2day av Merces**, och välj sedan den **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HR2day av Merces baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilken användaren motsvarighet i HR2day av Merces är att en användare i Azure AD. Med andra ord måste du upprätta en länk mellan en Azure AD-användare och relaterade användaren i HR2day av Merces.

I HR2day av Merces, tilldela den **användarnamn** i Azure AD för att **användarnamn** att upprätta en relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med HR2day av Merces, måste du utföra följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on): ge användarna använda den här funktionen.
1. [Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user): testa Azure AD enkel inloggning med Britta Simon.
1. [Skapa en HR2day av Merces testanvändare](#creating-an-hr2day-by-merces-test-user): skapa en motsvarighet för Britta Simon i HR2day av Merces som är länkad till en Azure AD-representation av användaren.
1. [Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user): aktivera Britta Simon att använda Azure AD enkel inloggning.
1. [Testa enkel inloggning](#testing-single-sign-on): Kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din HR2day av Merces program.

**Om du vill konfigurera Azure AD enkel inloggning med HR2day av Merces, gör du följande:**

1. I Azure-portalen på den **HR2day av Merces** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. Att aktivera enkel inloggning, i den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning**.
 
    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

1. I den **HR2day av Merces domän och URL: er** avsnittet, gör följande:

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. I den **inloggnings-URL** skriver du en URL med hjälp av följande mönster: `https://<tenantname>.force.com/<instancename>`.

    b. I den **identifierare** skriver du en URL med hjälp av följande mönster: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Kontakta den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl) att hämta dessa värden. 
 


1. På den **SAML-signeringscertifikat** väljer **Certificate(Base64)**, och sedan spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

1. Det här avsnittet beskrivs hur du aktiverar användare att autentisera till HR2day av Merces med ett konto i Azure AD. De kan göra detta med hjälp av federation som baseras på SAML-protokoll.

    Din HR2day av Merces program förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar SAML-token. Följande skärmbild visar ett exempel på detta. 

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Innan du kan konfigurera SAML-kontroll, måste du kontakta den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl) och begära värdet för attributet för unik identifierare för din klient. Du behöver det här värdet för att slutföra stegen i nästa avsnitt. 

1. I den **enkel inloggning** i dialogrutan den **användarattribut** avsnittet, konfigurera attributet som SAML-token som visas i följande bild. Sedan vidta följande steg.
    
      | Attributnamn    |   Attributvärde |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Öppna den **lägga till attributet** dialogrutan **Lägg till attribut**.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. I den **namn** skriver **ATTR_LOGINCLAIM**.

    c. Från den **värdet** väljer **Join()**.

    d. Från den **sträng1** väljer **user.mail**.

    e. För **sträng2**, ange den unika identifieraren som tillhandahålls av HR2day-teamet.

    f. I den **avgränsare** skriver **\@**.
    
    g. Välj **OK**.

1. Välj knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_general_400.png)

1. I den **HR2day av Merces konfigurationen** väljer **konfigurera HR2day av Merces** att öppna den **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens** avsnittet.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

1. Om du vill konfigurera enkel inloggning för ditt program, kontakta den [HR2day av Merces klienten supportteamet](mailTo:servicedesk@merces.nl). Koppla de hämtade **Certificate(Base64)** filen till din e-post. Tillhandahåller även den **URL: en för utloggning**, **SAML entitets-ID**, och **SAML enkel inloggning för tjänst-URL** så att de kan konfigureras för SSO-integrering.

    > [!NOTE]
    >Nämner till Merces-teamet att den här integreringen behöver entitets-ID anges med mönstret **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken. Komma åt den inbäddade dokumentationen genom den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen embedded-dokumentation i den [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret, väljer den **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/hr2day-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/hr2day-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/hr2day-tutorial/create_aaduser_03.png) 

1. I den **användaren** dialogrutan rutan, gör följande:
 
    ![Skapa en Azure AD-användare för testning](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord**, och sedan skriva ned lösenordet.

    d. Välj **Skapa**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Skapa en HR2day av Merces testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i HR2day av Merces. Om du vill lägga till användarna i HR2day-konto, arbeta med den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta den [HR2day av Merces klienten supportteamet](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst HR2day av Merces.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon HR2day av Merces, gör du följande:**

1. Öppna vyn program i Azure-portalen, gå till vyn directory och gå sedan till **företagsprogram**. Välj sedan **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **HR2day av Merces**.

    ![Konfigurera enkel inloggning](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

1. I menyn till vänster väljer **användare och grupper**.

    ![Tilldela användare][202] 

1. Välj den **Lägg till** knappen. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Tilldela användare][203]

1. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**.

1. Klicka på den **Välj** knappen.

1. I den **Lägg till tilldelning** dialogrutan **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  

När du väljer HR2day av Merces panel i åtkomstpanelen, får du loggas in automatiskt till din HR2day av Merces program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

