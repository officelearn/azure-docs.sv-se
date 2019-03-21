---
title: 'Självstudier: Azure Active Directory-integrering med SpringCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64aebaedd9221b2570b66bdb8550bff36b54492c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084767"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Självstudier: Azure Active Directory-integrering med SpringCM

I den här självstudien får du lära dig hur du integrerar SpringCM med Azure Active Directory (AD Azure).

Integrera SpringCM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SpringCM
- Du kan aktivera användarna att automatiskt få loggat in på SpringCM (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SpringCM, behöver du följande objekt:

- En Azure AD-prenumeration
- En SpringCM enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SpringCM från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-springcm-from-the-gallery"></a>Att lägga till SpringCM från galleriet
För att konfigurera integrering av SpringCM i Azure AD, som du behöver lägga till SpringCM från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SpringCM från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **SpringCM**.

    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/tutorial_springcm_search.png)

1. I resultatpanelen väljer **SpringCM**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SpringCM baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SpringCM är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SpringCM upprättas.

I SpringCM, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SpringCM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare SpringCM](#creating-a-springcm-test-user)**  – du har en motsvarighet för Britta Simon i SpringCM som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SpringCM program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SpringCM:**

1. I Azure-portalen på den **SpringCM** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_samlbase.png)

1. På den **SpringCM domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_url.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SpringCM klienten supportteamet](https://knowledge.springcm.com/support) att hämta det här värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_general_400.png)

1. På den **SpringCM Configuration** klickar du på **konfigurera SpringCM** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_configure.png)     

1. I ett annat webbläsarfönster, loggar du in på ditt **SpringCM** företagets plats som administratör.

1. Klicka på menyn längst upp **gå till**, klickar du på **inställningar**, och klicka sedan på **kontoinställningar** klickar du på **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Utför följande steg i konfigurationsavsnittet Identity-providern:
   
    ![Identitet providerkonfigurationen](./media/spring-cm-tutorial/ic797052.png "identitet Providerkonfiguration")
    
    a. Om du vill ladda upp din hämtade Azure Active Directory-certifikatet klickar du på **Välj utfärdarcertifikatet** eller **ändra utfärdarcertifikatet**.
    
    b. Klistra in **SAML entitets-ID** värde, som du har kopierat från Azure-portalen till den **utfärdare** textrutan.
    
    c. Klistra in **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen till den **slutpunkten för Service Provider (SP) initieras** textrutan.
            
    d. Välj **SAML aktiverat** som **aktivera**.

    e. Klicka på **Spara**.
 
> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-springcm-test-user"></a>Skapa en SpringCM testanvändare

Om du vill aktivera Azure Active Directory-användare att logga in på SpringCM, måste de etableras i SpringCM. När det gäller SpringCM är etablering en manuell aktivitet.

>[!NOTE]
>Mer information finns i [skapar och redigerar en SpringCM användare](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Om du vill konfigurera ett användarkonto till SpringCM, utför du följande steg:**

1. Logga in på din **SpringCM** företagets plats som administratör.

1. Klicka på **GOTO**, och klicka sedan på **ADRESSBOKEN**.
   
    ![Skapa användare](./media/spring-cm-tutorial/ic797054.png "Skapa användare")

1. Klicka på **Skapa användare**.

1. Välj en **användarrollen**.

1. Välj **skickar Aktiveringsmeddelandet**.

1. Ange den förnamn, efternamn och e-postadress för ett giltigt Azure Active Directory-användarkonto som du vill etablera till relaterade textrutor.

1. Lägga till användare i en **säkerhetsgrupp**.

1. Klicka på **Spara**.

   >[!NOTE]
   >Du kan använda alla andra SpringCM användare konto verktyg för att skapa eller API: er som tillhandahålls av SpringCM att etablera AAD-användarkonton.  
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SpringCM.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SpringCM, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SpringCM**.

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
 
När du klickar på panelen SpringCM i åtkomstpanelen du bör få automatiskt loggat in på ditt SpringCM program.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/spring-cm-tutorial/tutorial_general_203.png

