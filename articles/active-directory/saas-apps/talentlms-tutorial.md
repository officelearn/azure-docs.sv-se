---
title: 'Självstudier: Azure Active Directory-integrering med TalentLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa78ec2b5623dfd010a8fe5709916a47e221a9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57902203"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Självstudier: Azure Active Directory-integrering med TalentLMS

I den här självstudien får du lära dig hur du integrerar TalentLMS med Azure Active Directory (AD Azure).

Integrera TalentLMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TalentLMS
- Du kan aktivera användarna att automatiskt få loggat in på TalentLMS (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TalentLMS, behöver du följande objekt:

- En Azure AD-prenumeration
- En TalentLMS enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du få en tre månaders kostnadsfri utvärdering här: [Utvärderingserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till TalentLMS från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-talentlms-from-the-gallery"></a>Att lägga till TalentLMS från galleriet
För att konfigurera integrering av TalentLMS i Azure AD, som du behöver lägga till TalentLMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TalentLMS från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **TalentLMS**.

    ![Skapa en Azure AD-användare för testning](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. I resultatpanelen väljer **TalentLMS**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TalentLMS baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i TalentLMS är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i TalentLMS upprättas.

I TalentLMS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med TalentLMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare TalentLMS](#creating-a-talentlms-test-user)**  – du har en motsvarighet för Britta Simon i TalentLMS som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TalentLMS program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TalentLMS:**

1. I Azure-portalen på den **TalentLMS** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. På den **TalentLMS domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenant-name>.TalentLMSapp.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [TalentLMS klienten supportteamet](https://www.talentlms.com/contact) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet från certifikatet.

    ![Konfigurera enkel inloggning](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/talentlms-tutorial/tutorial_general_400.png)

1. På den **TalentLMS Configuration** klickar du på **konfigurera TalentLMS** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. I ett annat webbläsarfönster logga du in på webbplatsen TalentLMS företag som administratör.

1. I den **konto och inställningar** klickar du på den **användare** fliken.
   
    ![Konto & inställningar](./media/talentlms-tutorial/IC777296.png "konto och inställningar")

1. Klicka på **enkel inloggning (SSO)**,

1. Gör följande i avsnittet Enkel inloggning:
   
    ![Enkel inloggning](./media/talentlms-tutorial/IC777297.png "Enkel inloggning")   

    a. Från den **SSO Integrationstyp** väljer **SAML 2.0**.

    b. I den **identitetsprovider (IDP)** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.
 
    c. Klistra in den **tumavtryck** värdet från Azure-portalen till den **certifikat fingeravtryck** textrutan.    

    d.  I den **Remote inloggning URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
 
    e. I den **utloggnings-URL** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    f. Fyll i följande: 

    * I den **TargetedID** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * I den **Förnamn** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * I den **efternamn** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * I den **e-post** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
1. Klicka på **Spara**.
 
> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/talentlms-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/talentlms-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/talentlms-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-talentlms-test-user"></a>Skapa en TalentLMS testanvändare

Om du vill aktivera Azure AD-användare att logga in på TalentLMS, måste de etableras i TalentLMS. När det gäller TalentLMS är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **TalentLMS** klient.

1. Klicka på **användare**, och klicka sedan på **Lägg till användare**.

1. På den **Lägg till användare** dialogrutan utför följande steg:
   
    ![Lägg till användare](./media/talentlms-tutorial/IC777299.png "Lägg till användare")  

    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.
 
    c. I den **e-postadress** textrutan Ange e-postadress för användaren som **brittasimon\@contoso.com**.

    d. Klicka på **Lägg till användare**.

>[!NOTE]
>Du kan använda alla andra TalentLMS användare konto verktyg för att skapa eller API: er som tillhandahålls av TalentLMS att etablera AAD-användarkonton.
 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TalentLMS.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon TalentLMS, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **TalentLMS**.

    ![Konfigurera enkel inloggning](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TalentLMS i åtkomstpanelen du bör få automatiskt loggat in på ditt TalentLMS-program

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png

