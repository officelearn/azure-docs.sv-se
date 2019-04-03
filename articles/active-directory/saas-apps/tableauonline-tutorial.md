---
title: 'Självstudier: Azure Active Directory-integrering med Tableau Online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5e3087c21908600be9cd369a15f3036e5acb2f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884711"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Självstudier: Azure Active Directory-integrering med Tableau Online

I den här självstudien får du lära dig hur du integrerar Tableau Online med Azure Active Directory (AD Azure).

Integrera Tableau Online med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tableau Online
- Du kan aktivera användarna att automatiskt få loggat in på Tableau Online (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tableau Online, behöver du följande objekt:

- En Azure AD-prenumeration
- En Tableau Online enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Tableau Online från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-tableau-online-from-the-gallery"></a>Att lägga till Tableau Online från galleriet
För att konfigurera integrering av Tableau Online i Azure AD, som du behöver lägga till Tableau Online från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Tableau Online från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Tableau Online**.

    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/tutorial_tableauonline_search.png)

1. I resultatpanelen väljer **Tableau Online**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tableau Online baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i Tableau Online är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Tableau Online upprättas.

I Online Tableau, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Online, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Tableau Online testanvändare](#creating-a-tableau-online-test-user)**  – du har en motsvarighet för Britta Simon i Tableau Online som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program som Tableau Online.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Online:**

1. I Azure-portalen på den **Tableau Online** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

1. På den **Tableau Online domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. I rutan **Inloggnings-URL** anger du URL:en: `https://sso.online.tableau.com`

    b. I textrutan **Identifierare** anger du URL:en: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster inloggning till programmets Tableau Online. Gå till **inställningar** och sedan **autentisering**.
   
    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)
    
1. Aktivera SAML, Under **autentiseringstyper** avsnittet. Kontrollera den **enkel inloggning med SAML** kryssrutan.
   
    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

1. Rulla nedåt tills **importfilen metadata i Tableau Online** avsnittet.  Klicka på Bläddra och importera metadatafilen som du har laddat ned från Azure AD. Klicka sedan på **tillämpa**.
   
   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

1. I den **matchar intyg** avsnittet, infoga motsvarande identitetsprovider assertion namn för **e-postadress**, **Förnamn**, och **efternamn**. Hämta den här informationen från Azure AD: 
  
    a. I Azure-portalen går du vidare den **Tableau Online** integrering programsidan.
    
    b. I attributavsnittet, väljer du den **”visa och redigera alla andra användarattribut”** kryssrutan. 
    
   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection.png)
      
    c. Kopiera namnområdesvärdet för dessa attribut: givenname, e-post- och efternamn med hjälp av följande steg:

   ![Azure AD enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Klicka på **user.givenname** värde 
    
    e. Kopiera värdet från den **namnområde** textrutan.

   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/attributesection2.png)

    f. För att kopiera namnområdet Följ värden för e-post och efternamn föregående steg.

    g. Växla till programmet Tableau Online och ange sedan den **Tableau Onlineattribut** avsnittet på följande sätt:
     * E-postadress: **e** eller **userprincipalname**
     * Förnamn: **givenname**
     * Efternamn: **efternamn**
   
   ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-tableau-online-test-user"></a>Skapa en Tableau Online testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Tableau Online.

1. På **Tableau Online**, klickar du på **inställningar** och sedan **autentisering** avsnittet. Rulla ned till **Välj användare** avsnittet. Klicka på **lägga till användare** och sedan **ange e-postadresser**.
   
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)
1. Välj **lägga till användare för enkel inloggning (SSO) autentisering**. I den **ange e-postadresser** textrutan Lägg till britta.simon@contoso.com
   
    ![Skapa en Azure AD-användare för testning](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)
1. Klicka på **Skapa**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Tableau Online.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Tableau Online, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Tableau Online**.

    ![Konfigurera enkel inloggning](./media/tableauonline-tutorial/tutorial_tableauonline_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på knappen **Lägg till**. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau Online i åtkomstpanelen du bör få automatiskt loggat in på programmets Tableau Online.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/tableauonline-tutorial/tutorial_general_203.png
