---
title: 'Självstudier: Azure Active Directory-integrering med Jitbit Helpdesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 48506be8f1e918a28ac5ca80949ab6ac547869f2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811187"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Självstudier: Azure Active Directory-integrering med Jitbit Helpdesk

I den här självstudien får du lära dig hur du integrerar Jitbit Helpdesk med Azure Active Directory (AD Azure).

Integrera Jitbit Helpdesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Jitbit Helpdesk
- Du kan aktivera användarna att automatiskt få inloggade Jitbit Helpdesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Jitbit Helpdesk, behöver du följande objekt:

- En Azure AD-prenumeration
- En Jitbit Helpdesk enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Jitbit Helpdesk från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Att lägga till Jitbit Helpdesk från galleriet
För att konfigurera integrering av Jitbit Helpdesk i Azure AD, som du behöver lägga till Jitbit Helpdesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Jitbit Helpdesk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Jitbit Helpdesk**.

    ![Skapa en Azure AD-användare för testning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

1. I resultatpanelen väljer **Jitbit Helpdesk**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Jitbit Helpdesk utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Jitbit Helpdesk är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Jitbit Helpdesk upprättas.

I Jitbit Helpdesk, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Jitbit Helpdesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Jitbit Helpdesk](#creating-a-jitbit-helpdesk-test-user)**  – du har en motsvarighet för Britta Simon i Jitbit Helpdesk som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Jitbit Helpdesk-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Jitbit Helpdesk:**

1. I Azure-portalen på den **Jitbit Helpdesk** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

1. På den **Jitbit supportavdelningen domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Jitbit supportavdelningen klienten supportteamet](https://www.jitbit.com/support/) att hämta det här värdet. 
    
    b.  I den **identifierare** textrutan anger du ett URL på följande: `https://www.jitbit.com/web-helpdesk/`

    
 


1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/jitbit-helpdesk-tutorial/tutorial_general_400.png)

1. På den **Jitbit supportavdelningen Configuration** klickar du på **konfigurera Jitbit supportavdelningen** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

1. Logga in på webbplatsen Jitbit Helpdesk företag som en administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp, klickar du på **Administration**.
   
    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **allmänna inställningar**.
   
    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777680.png "användare, företag och behörigheter")

1. I den **autentiseringsinställningar** konfiguration och utför följande steg:
   
    ![Autentiseringsinställningar](./media/jitbit-helpdesk-tutorial/ic777683.png "autentiseringsinställningar")
    
    a. Välj **aktivera SAML 2.0 enkel inloggning**, för att logga in med enkel inloggning (SSO), med **OneLogin**.

    b. I den **slutpunkts-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Öppna din **Base64-** kodade certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textrutan

    d. Klicka på **spara ändringar**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/jitbit-helpdesk-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/jitbit-helpdesk-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/jitbit-helpdesk-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typnamn som **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Skapa en testanvändare Jitbit Helpdesk

För att aktivera Azure AD-användare att logga in på Jitbit Helpdesk, måste de etableras i Jitbit Helpdesk.  När det gäller Jitbit Helpdesk är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Jitbit Helpdesk** klient.

1. Klicka på menyn längst upp **Administration**.
   
    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. Klicka på **användare, företag och behörigheter**.
   
    ![Användare, företag och behörigheter](./media/jitbit-helpdesk-tutorial/ic777682.png "användare, företag och behörigheter")

1. Klicka på **Lägg till användare**.
   
    ![Lägg till användare](./media/jitbit-helpdesk-tutorial/ic777685.png "Lägg till användare")
   
1. I avsnittet Skapa skriver du data för Azure AD-konto som du vill etablera enligt följande:

    ![Skapa](./media/jitbit-helpdesk-tutorial/ic777686.png "skapa")
   
   a. I den **användarnamn** textrutan typ **BrittaSimon**, användarnamnet som i Azure-portalen.

   b. I den **e-post** textrutan e-post för användaren som **BrittaSimon@contoso.com**.

   c. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

   d. I den **efternamn** textrutan Skriv Efternamn för användaren som **Simon**.
   
   e. Klicka på **Skapa**.

>[!NOTE]
>Du kan använda andra verktyg för Jitbit Helpdesk användare konto att skapa eller tillhandahålls av Jitbit Helpdesk-API: er för att etablera användarkonton i Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Jitbit Helpdesk.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Jitbit Helpdesk, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Jitbit Helpdesk**.

    ![Konfigurera enkel inloggning](./media/jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Jitbit Helpdesk i åtkomstpanelen, bör du få inloggningssidan i Jitbit Helpdesk-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/jitbit-helpdesk-tutorial/tutorial_general_203.png

