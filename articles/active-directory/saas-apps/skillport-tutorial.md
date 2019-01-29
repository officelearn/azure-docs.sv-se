---
title: 'Självstudier: Azure Active Directory-integrering med Skillport | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Skillport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4df349b2-a73f-4b88-a077-ec0fbfc26527
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: d6b80855fa301da5d2e5be37b1b316e5a0af81d1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171974"
---
# <a name="tutorial-azure-active-directory-integration-with-skillport"></a>Självstudier: Azure Active Directory-integrering med Skillport

I den här självstudien får du lära dig hur du integrerar Skillport med Azure Active Directory (AD Azure).

Integrera Skillport med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Skillport
- Du kan aktivera användarna att automatiskt få loggat in på Skillport (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Skillport, behöver du följande objekt:

- En Azure AD-prenumeration
- En Skillport enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Skillport från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-skillport-from-the-gallery"></a>Att lägga till Skillport från galleriet
För att konfigurera integrering av Skillport i Azure AD, som du behöver lägga till Skillport från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Skillport från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Skillport**.

    ![Skapa en Azure AD-användare för testning](./media/skillport-tutorial/tutorial_skillport_search.png)

1. I resultatpanelen väljer **Skillport**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/skillport-tutorial/tutorial_skillport_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Skillport baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Skillport är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Skillport upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Skillport.

Om du vill konfigurera och testa Azure AD enkel inloggning med Skillport, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Skillport](#creating-a-skillport-test-user)**  – du har en motsvarighet för Britta Simon i Skillport som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Skillport program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Skillport:**

1. I Azure-portalen på den **Skillport** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/skillport-tutorial/tutorial_skillport_samlbase.png)

1. På den **Skillport domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/skillport-tutorial/tutorial_skillport_url.png)

    a. I rutan **Inloggnings-URL** anger du URL:en:
      
      EU: S Datacenter: `https://adfs.skillport.eu`
   
      Datacenter i USA: `https://sso.skillport.com`

    b. I textrutan **Identifierare** anger du URL:en:
      
      EU: S Datacenter: `http://adfs.skillport.eu/adfs/services/trust`
   
      Datacenter i USA: `https://sso.skillport.com`
   
    c. I textrutan **Svars-URL** anger du URL:en:
    
      EU: S Datacenter: ` https://adfs.skillport.eu/adfs/ls/`
    
      Datacenter i USA: `https://sso.skillport.com/sp/ACS.saml2`
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/skillport-tutorial/tutorial_skillport_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/skillport-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Skillport** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Skillport supportteamet](https://www.skillsoft.com/contact.asp). De kan konfigurera den för att ha SAML SSO-anslutning som är korrekt inställda på båda sidorna.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/skillport-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/skillport-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/skillport-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/skillport-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-skillport-test-user"></a>Skapa en Skillport testanvändare

Du vill skapa Skillport testanvändare, måste du kontakta [Skillport supportteamet](https://www.skillsoft.com/contact.asp) eftersom de har flera affärsscenarier enligt behovet av slutanvändaren. Det konfigureras efter diskussioner med användare.  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Skillport.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Skillport, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Skillport**.

    ![Konfigurera enkel inloggning](./media/skillport-tutorial/tutorial_skillport_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Skillport i åtkomstpanelen du bör få automatiskt loggat in på ditt Skillport program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillport-tutorial/tutorial_general_01.png
[2]: ./media/skillport-tutorial/tutorial_general_02.png
[3]: ./media/skillport-tutorial/tutorial_general_03.png
[4]: ./media/skillport-tutorial/tutorial_general_04.png

[100]: ./media/skillport-tutorial/tutorial_general_100.png

[200]: ./media/skillport-tutorial/tutorial_general_200.png
[201]: ./media/skillport-tutorial/tutorial_general_201.png
[202]: ./media/skillport-tutorial/tutorial_general_202.png
[203]: ./media/skillport-tutorial/tutorial_general_203.png

