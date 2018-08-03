---
title: 'Självstudier: Azure Active Directory-integration med FreshDesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: d0fbed347805a581fb66e0218290993817277214
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428340"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudier: Azure Active Directory-integration med FreshDesk

I den här självstudien får du lära dig hur du integrerar FreshDesk med Azure Active Directory (AD Azure).

Integrera FreshDesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FreshDesk
- Du kan aktivera användarna att automatiskt få loggat in på FreshDesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FreshDesk, behöver du följande objekt:

- En Azure AD-prenumeration
- En FreshDesk enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till FreshDesk från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-freshdesk-from-the-gallery"></a>Att lägga till FreshDesk från galleriet
För att konfigurera integrering av FreshDesk i Azure AD, som du behöver lägga till FreshDesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FreshDesk från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **FreshDesk**.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/tutorial_freshdesk_search.png)

1. I resultatpanelen väljer **FreshDesk**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med FreshDesk baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i FreshDesk är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i FreshDesk upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i FreshDesk.

Om du vill konfigurera och testa Azure AD enkel inloggning med FreshDesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare FreshDesk](#creating-a-freshdesk-test-user)**  – du har en motsvarighet för Britta Simon i FreshDesk som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt FreshDesk-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FreshDesk:**

1. I hanteringsportalen för Azure på den **FreshDesk** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

1. På den **FreshDesk domän och URL: er** avsnittet, ange den **inloggnings-URL** som: `https://<tenant-name>.freshdesk.com` eller ett annat värde som har föreslagits Freshdesk.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [FreshDesk klienten supportteamet](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) att hämta det här värdet.  

1. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatet på datorn.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_general_400.png)

1. På den **FreshDesk Configuration** klickar du på **konfigurera FreshDesk** öppna Konfigurera inloggnings-fönstret. Kopiera SAML enkel inloggning för tjänstens URL och URL: en för utloggning från den **Snabbreferens** avsnittet.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

1. Logga in på webbplatsen Freshdesk företag som en administratör i ett annat webbläsarfönster.

1. Klicka på menyn längst upp **Admin**.
   
   ![Administratören](./media/freshdesk-tutorial/IC776768.png "Admin")

1. I den **allmänna inställningar** fliken **Security**.
   
   ![Security](./media/freshdesk-tutorial/IC776769.png "säkerhet")

1. I den **Security** avsnittet, utför följande steg:
   
    ![Enkel inloggning](./media/freshdesk-tutorial/IC776770.png "enkel inloggning")
   
    a. För **enkel inloggning (SSO)** väljer **på**.

    b. Välj **SAML SSO**.

    c. Typen i **SAML enkel inloggning för tjänst-URL** du kopierade från Azure-portalen till den **inloggnings-URL för SAML** textrutan.

    d. Typen i **URL: en för utloggning** du kopierade från Azure-portalen till den **URL för utloggning** textrutan.

    e. Kopiera den **tumavtryck** värde från det nedladdade certifikatet från Azure-portalen och klistra in den i den **Security certifikat fingeravtryck** textrutan.  
 
    >[!TIP]
    >Mer information finns i [hur du hämtar en certifikatets tumavtrycksvärde](http://youtu.be/YKQF266SAxI). 
    
    f. Klicka på **Spara**.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-freshdesk-test-user"></a>Skapa en FreshDesk testanvändare

För att aktivera Azure AD-användare att logga in på FreshDesk, måste de etableras i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Utför följande steg för att tillhandahålla en användarkonton:**

1. Logga in på din **Freshdesk** klient.
1. Klicka på menyn längst upp **Admin**.
   
   ![Administratören](./media/freshdesk-tutorial/IC776772.png "Admin")

1. I den **allmänna inställningar** fliken **agenter**.
   
   ![Agenter](./media/freshdesk-tutorial/IC776773.png "agenter")

1. Klicka på **ny Agent**.
   
    ![Ny Agent](./media/freshdesk-tutorial/IC776774.png "ny Agent")

1. I dialogrutan agentinformation utför du följande steg:
   
   ![Agentinformation](./media/freshdesk-tutorial/IC776775.png "agentinformation")
   
   a. I den **fullständigt namn** textrutan skriver du namnet på Azure AD-konto som du vill etablera.

   b. I den **e-post** textrutan Ange Azure AD-e-postadress för Azure AD-konto som du vill etablera.

   c. I den **rubrik** textrutan skriver du namnet på Azure AD-konto som du vill etablera.

   d. Välj **agenter rollen**, och klicka sedan på **tilldela**.
       
   e. Klicka på **Spara**.     
   
    >[!NOTE]
    >Azure AD-kontoinnehavare får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det aktiveras. 
    > 
    
    >[!NOTE]
    >Du kan använda alla andra Freshdesk användare konto verktyg för att skapa eller API: er som tillhandahålls av Freshdesk att etablera AAD-användarkonton. till FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst till Box.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon FreshDesk, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **FreshDesk**.

    ![Konfigurera enkel inloggning](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen FreshDesk i åtkomstpanelen, bör du få inloggningssida för att hämta loggat in på ditt FreshDesk-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png

