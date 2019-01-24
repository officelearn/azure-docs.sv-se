---
title: 'Självstudier: Azure Active Directory-integrering med Hackerone | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hackerone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: fe070505970516efcd4e2ae46dedff2792f95b08
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817205"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Självstudier: Azure Active Directory-integrering med HackerOne

I den här självstudien får du lära dig hur du integrerar HackerOne med Azure Active Directory (AD Azure).

Integrera HackerOne med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HackerOne
- Du kan aktivera användarna att automatiskt få loggat in på HackerOne (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HackerOne, behöver du följande objekt:

- En Azure AD-prenumeration
- En HackerOne enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till HackerOne från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-hackerone-from-the-gallery"></a>Att lägga till HackerOne från galleriet
För att konfigurera integrering av HackerOne i Azure AD, som du behöver lägga till HackerOne från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HackerOne från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **HackerOne**.

    ![Skapa en Azure AD-användare för testning](./media/hackerone-tutorial/tutorial_hackerone_search.png)

1. I resultatpanelen väljer **HackerOne**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HackerOne baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i HackerOne är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i HackerOne upprättas.

I HackerOne, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med HackerOne, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare HackerOne](#creating-a-hackerone-test-user)**  – du har en motsvarighet för Britta Simon i HackerOne som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt HackerOne program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med HackerOne:**

1. I Azure-portalen på den **HackerOne** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_samlbase.png)

1. På den **HackerOne Single inloggnings-URL och identifierare** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://hackerone.com/<company name>/authentication`

    b. I den **identifierare** textrutan anger du ett URL: en som:  `https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [HackerOne supportteamet](mailto:support@hackerone.com) att hämta det här värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_general_400.png)

1. På den **HackerOne Configuration** klickar du på **konfigurera HackerOne** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_configure.png) 

1. Inloggning till HackerOne-klienten som administratör.

1. Klicka på menyn längst upp i ”**inställningar**”.
   
    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_001.png) 

1. Navigera till ”**autentisering**” och klicka på ”**lägga till SAML-inställningar**”.
   
    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_003.png) 

1. På den **SAML-inställningar** dialogrutan utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_004.png) 

    a. I den **e-postdomän** textrutan skriver en registrerad domän.

    b. I  **inloggning på URL för enkel** textrutor, klistra in värdet för **SAML enkel inloggning för tjänst-URL** som du har kopierat från Azure-portalen.

    c. Öppna din **certifikatfilen** i anteckningar som hämtats från Azure-portalen, kopierar du innehållet i den till Urklipp och klistra in den till den **X509 certifikat**  textrutan.
    
    d. Klicka på **Spara**.

1. Utför följande steg i dialogrutan Inställningar för autentisering:
   
    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Klicka på **Kör test**.

    b. Om värdet för den **Status** fältet är lika med **senast teststatus: skapade**, kontakta din [HackerOne supportteamet](mailto:support@hackerone.com) att begära en granskning av din konfiguration.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/hackerone-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/hackerone-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/hackerone-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/hackerone-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-hackerone-test-user"></a>Skapa en HackerOne testanvändare

Därefter skapar du en användare som kallas Britta Simon i HackerOne. HackerOne stöder just-in-time-etablering, som är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. När du har åtkomst till HackerOne skapas en ny användare om den inte finns.

>[!NOTE]
>Om du vill skapa en användare manuellt kan behöva du kontakta supporten Certify. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HackerOne.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon HackerOne, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **HackerOne**.

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Slutligen kan testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.  

När du klickar på panelen HackerOne i åtkomstpanelen du bör få automatiskt loggat in på ditt HackerOne program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hackerone-tutorial/tutorial_general_01.png
[2]: ./media/hackerone-tutorial/tutorial_general_02.png
[3]: ./media/hackerone-tutorial/tutorial_general_03.png
[4]: ./media/hackerone-tutorial/tutorial_general_04.png

[100]: ./media/hackerone-tutorial/tutorial_general_100.png

[200]: ./media/hackerone-tutorial/tutorial_general_200.png
[201]: ./media/hackerone-tutorial/tutorial_general_201.png
[202]: ./media/hackerone-tutorial/tutorial_general_202.png
[203]: ./media/hackerone-tutorial/tutorial_general_203.png

