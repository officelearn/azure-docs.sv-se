---
title: 'Självstudier: Azure Active Directory-integrering med RunMyProcess | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfef1371b7ac61712c0f70efd48c0e791c4c729d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162687"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Självstudier: Azure Active Directory-integrering med RunMyProcess

I den här självstudien får du lära dig hur du integrerar RunMyProcess med Azure Active Directory (AD Azure).

Integrera RunMyProcess med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till RunMyProcess
- Du kan aktivera användarna att automatiskt få loggat in på RunMyProcess (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med RunMyProcess, behöver du följande objekt:

- En Azure AD-prenumeration
- En RunMyProcess enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du kan få en månads utvärdering här:[– prova](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till RunMyProcess från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-runmyprocess-from-the-gallery"></a>Att lägga till RunMyProcess från galleriet
För att konfigurera integrering av RunMyProcess i Azure AD, som du behöver lägga till RunMyProcess från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till RunMyProcess från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **RunMyProcess**.

    ![Skapa en Azure AD-användare för testning](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

1. I resultatpanelen väljer **RunMyProcess**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med RunMyProcess baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i RunMyProcess är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i RunMyProcess upprättas.

I RunMyProcess, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med RunMyProcess, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare RunMyProcess](#creating-a-runmyprocess-test-user)**  – du har en motsvarighet för Britta Simon i RunMyProcess som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt RunMyProcess program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med RunMyProcess:**

1. I Azure-portalen på den **RunMyProcess** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

1. På den **RunMyProcess domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [RunMyProcess klienten supportteamet](mailto:support@runmyprocess.com) att hämta värdet. 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/runmyprocess-tutorial/tutorial_general_400.png)

1. På den **RunMyProcess Configuration** klickar du på **konfigurera RunMyProcess** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

1. I ett annat webbläsarfönster inloggning till RunMyProcess-klienten som administratör.

1. I den vänstra navigeringspanelen klickar du på **konto** och välj **Configuration**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Gå till **autentiseringsmetod** avsnittet och utföra stegen nedan:
   
    ![Konfigurera enkel inloggning på appsidan](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Som **metoden**väljer **SSO med Samlv2**. 

    b. I den **SSO omdirigering** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I den **utloggning omdirigering** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. I den **Format för namn-Id** textrutan, ange värdet för **Format för namn på identifierare** som **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**.

    e. Kopiera innehållet i filen nedladdade certifikatet och klistra in den i den **certifikat** textrutan. 
 
    f. Klicka på **spara** ikon.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/runmyprocess-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/runmyprocess-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/runmyprocess-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Skapa en RunMyProcess testanvändare

För att aktivera Azure AD-användare att logga in på RunMyProcess, måste de etableras i RunMyProcess. När det gäller RunMyProcess är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen RunMyProcess företag som administratör.

1. Klicka på **konto** och välj **användare** i vänstra navigeringspanelen och klicka på **ny användare**.
   
    ![Ny användare](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Ny användare")

1. I den **användarinställningar** avsnittet, utför följande steg:
   
    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil") 
  
    a. Typen i **namn** och **e** av en giltig Azure AD-konto som du vill etablera till relaterade textrutor. 

    b. Välj en **IDE språk**, **språk**, och **profil**. 

    c. Välj **skicka konto skapa e-postmeddelande till mig**. 

    d. Klicka på **Spara**.
   
    >[!NOTE]
    >Du kan använda alla andra RunMyProcess användare konto verktyg för att skapa eller API: er som tillhandahålls av RunMyProcess för att etablera Azure Active Directory användarkonton. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RunMyProcess.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon RunMyProcess, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **RunMyProcess**.

    ![Konfigurera enkel inloggning](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen RunMyProcess i åtkomstpanelen du bör få automatiskt loggat in på ditt RunMyProcess program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

