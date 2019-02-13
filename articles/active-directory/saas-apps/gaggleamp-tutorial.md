---
title: 'Självstudier: Azure Active Directory-integrering med GaggleAMP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GaggleAMP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9cc1a4b7-964b-406b-9e0c-05cb1a7c9856
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce28f3667689134a2de177ed5c0dfae810dc1889
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173580"
---
# <a name="tutorial-azure-active-directory-integration-with-gaggleamp"></a>Självstudier: Azure Active Directory-integrering med GaggleAMP

I den här självstudien får du lära dig hur du integrerar GaggleAMP med Azure Active Directory (AD Azure).

Integrera GaggleAMP med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till GaggleAMP
- Du kan aktivera användarna att automatiskt få loggat in på GaggleAMP (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med GaggleAMP, behöver du följande objekt:

- En Azure AD-prenumeration
- En GaggleAMP enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till GaggleAMP från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-gaggleamp-from-the-gallery"></a>Att lägga till GaggleAMP från galleriet
För att konfigurera integrering av GaggleAMP i Azure AD, som du behöver lägga till GaggleAMP från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till GaggleAMP från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **GaggleAMP**.

    ![Skapa en Azure AD-användare för testning](./media/gaggleamp-tutorial/tutorial_gaggleamp_search.png)

1. I resultatpanelen väljer **GaggleAMP**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/gaggleamp-tutorial/tutorial_gaggleamp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med GaggleAMP baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i GaggleAMP är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i GaggleAMP upprättas.

I GaggleAMP, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med GaggleAMP, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare GaggleAMP](#creating-a-gaggleamp-test-user)**  – du har en motsvarighet för Britta Simon i GaggleAMP som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt GaggleAMP program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med GaggleAMP:**

1. I Azure-portalen på den **GaggleAMP** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_gaggleamp_samlbase.png)

1. På den **GaggleAMP domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_gaggleamp_url.png)

     I den **identifierare** textrutan anger du URL: `https://accounts.gaggleamp.com/auth/saml/callback`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_gaggleamp_url1.png)

     I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://gaggleamp.com/i/<customerid>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [GaggleAMP klienten supportteamet](mailto:sales@gaggleamp.com) att hämta det här värdet.
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_gaggleamp_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_general_400.png)

1. På den **GaggleAMP Configuration** klickar du på **konfigurera GaggleAMP** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_gaggleamp_configure.png) 

1. I en annan instans av webbläsare, navigerar du till SAML SSO-sidan som skapats för dig av Gaggle supportteam (till exempel: *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).

1. På din **SAML SSO** utför följande steg:  
   
    ![GaggleAMP Single Sign-On](./media/gaggleamp-tutorial/tutorial_gaggleamp_06.png)

    a. Välj **andra** formuläret i **identitetsprovider** nedrullningsbara menyn.
    
    b. I den **Providerutgivare** textrutan klistra in värdet för **utfärdar-URL** som du har kopierat från Azure-portalen.
    
    c. I den **enkel inloggnings-URL för identitetsprovider** textrutan klistra in värdet för **enkel inloggnings-URL för** som du har kopierat från Azure-portalen.
    
    d. Öppna din hämtade **Certificate(Base64)** filen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan.
    
    e. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/gaggleamp-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/gaggleamp-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/gaggleamp-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/gaggleamp-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-gaggleamp-test-user"></a>Skapa en GaggleAMP testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i GaggleAMP. GaggleAMP stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt GaggleAMP om det inte finns ännu. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till GaggleAMP.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon GaggleAMP, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **GaggleAMP**.

    ![Konfigurera enkel inloggning](./media/gaggleamp-tutorial/tutorial_gaggleamp_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen GaggleAMP i åtkomstpanelen du bör få automatiskt loggat in på ditt GaggleAMP program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/gaggleamp-tutorial/tutorial_general_01.png
[2]: ./media/gaggleamp-tutorial/tutorial_general_02.png
[3]: ./media/gaggleamp-tutorial/tutorial_general_03.png
[4]: ./media/gaggleamp-tutorial/tutorial_general_04.png

[100]: ./media/gaggleamp-tutorial/tutorial_general_100.png

[200]: ./media/gaggleamp-tutorial/tutorial_general_200.png
[201]: ./media/gaggleamp-tutorial/tutorial_general_201.png
[202]: ./media/gaggleamp-tutorial/tutorial_general_202.png
[203]: ./media/gaggleamp-tutorial/tutorial_general_203.png
