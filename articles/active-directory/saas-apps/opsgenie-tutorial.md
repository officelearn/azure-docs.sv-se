---
title: 'Självstudier: Azure Active Directory-integrering med OpsGenie | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 715035072ddc2ceb087d003dd5da5bc47572e9b9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444359"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Självstudier: Azure Active Directory-integrering med OpsGenie

I den här självstudien får du lära dig hur du integrerar OpsGenie med Azure Active Directory (AD Azure).

Integrera OpsGenie med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OpsGenie
- Du kan aktivera användarna att automatiskt få loggat in på OpsGenie (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OpsGenie, behöver du följande objekt:

- En Azure AD-prenumeration
- En OpsGenie enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till OpsGenie från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-opsgenie-from-the-gallery"></a>Att lägga till OpsGenie från galleriet
För att konfigurera integrering av OpsGenie i Azure AD, som du behöver lägga till OpsGenie från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OpsGenie från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **OpsGenie**.

    ![Skapa en Azure AD-användare för testning](./media/opsgenie-tutorial/tutorial_opsgenie_search.png)

1. I resultatpanelen väljer **OpsGenie**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OpsGenie baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i OpsGenie är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i OpsGenie upprättas.

I OpsGenie, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med OpsGenie, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare OpsGenie](#creating-a-opsgenie-test-user)**  – du har en motsvarighet för Britta Simon i OpsGenie som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program med OpsGenie.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med OpsGenie:**

1. I Azure-portalen på den **OpsGenie** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

1. På den **OpsGenie domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/opsgenie-tutorial/tutorial_opsgenie_url.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://app.opsgenie.com/auth/login`

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för hämtning av certifikat](./media/opsgenie-tutorial/tutorial_opsgenie_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/opsgenie-tutorial/tutorial_general_400.png)

1. På den **OpsGenie Configuration** klickar du på **konfigurera OpsGenie** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från avsnittet Snabbreferens.

    ![Konfigurera enkel inloggning](./media/opsgenie-tutorial/tutorial_opsgenie_configure.png)

1. Öppna en annan webbläsare instans och sedan logga in till OpsGenie som administratör.

1. Klicka på **inställningar**, och klicka sedan på den **Single Sign On** fliken.
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

1. Välj för att aktivera SSO **aktiverad**.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

1. I den **Provider** klickar du på den **Azure Active Directory** fliken.
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

1. Utför följande steg på sidan för Azure Active Directory-dialogrutan:
   
    ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. I den **slutpunkt för SAML 2.0** textrutan klistra in **enkel inloggning på tjänstens URL**värde som du har kopierat från Azure-portalen.
    
    b. I den **Metadata-Url:** textrutan klistra in **Appfederationsmetadata** värde som du har kopierat från Azure-portalen.
    
    c. Klicka på **spara ändringar**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/opsgenie-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/opsgenie-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/opsgenie-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/opsgenie-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-opsgenie-test-user"></a>Skapa en testanvändare OpsGenie

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i OpsGenie. 

1. Logga in på din OpsGenie-klient som en administratör i ett webbläsarfönster.

1. Gå till användarlistan genom att klicka på **användaren** i vänster panel.
   
   ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

1. Klicka på **lägga till användare**.

1. På den **Lägg till användare** dialogrutan utför följande steg:
   
   ![Inställningar för OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. I den **e-post** textrutan e-postadressen sorts BrittaSimon åtgärdas i Azure Active Directory.
   
   b. I den **fullständigt namn** textrutan typ **Britta Simon**.
   
   c. Klicka på **Spara**. 

>[!NOTE]
>Britta får ett e-postmeddelande med instruktioner för att konfigurera sin profil.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OpsGenie.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon OpsGenie, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **OpsGenie**.

    ![Konfigurera enkel inloggning](./media/opsgenie-tutorial/tutorial_opsgenie_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen OpsGenie i åtkomstpanelen du bör få automatiskt loggat in på ditt OpsGenie program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/opsgenie-tutorial/tutorial_general_203.png

