---
title: 'Självstudier: Azure Active Directory-integration med Freshservice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.openlocfilehash: eb848ede258d8d25d4734664bd500235f34359e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440668"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Självstudier: Azure Active Directory-integration med Freshservice

I den här självstudien får du lära dig hur du integrerar Freshservice med Azure Active Directory (AD Azure).

Integrera Freshservice med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Freshservice
- Du kan aktivera användarna att automatiskt få loggat in på Freshservice (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Freshservice, behöver du följande objekt:

- En Azure AD-prenumeration
- En Freshservice enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Freshservice från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-freshservice-from-the-gallery"></a>Att lägga till Freshservice från galleriet
För att konfigurera integrering av Freshservice i Azure AD, som du behöver lägga till Freshservice från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Freshservice från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Freshservice**.

    ![Skapa en Azure AD-användare för testning](./media/freshservice-tutorial/tutorial_freshservice_search.png)

1. I resultatpanelen väljer **Freshservice**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Freshservice baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Freshservice är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Freshservice upprättas.

I Freshservice, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Freshservice, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Freshservice](#creating-a-freshservice-test-user)**  – du har en motsvarighet för Britta Simon i Freshservice som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Freshservice.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Freshservice:**

1. I Azure-portalen på den **Freshservice** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/freshservice-tutorial/tutorial_freshservice_samlbase.png)

1. På den **Freshservice domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/freshservice-tutorial/tutorial_freshservice_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<democompany>.freshservice.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Freshservice klienten supportteamet](https://support.freshservice.com/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera **TUMAVTRYCK** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/freshservice-tutorial/tutorial_freshservice_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/freshservice-tutorial/tutorial_general_400.png)

1. På den **Freshservice Configuration** klickar du på **konfigurera Freshservice** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/freshservice-tutorial/tutorial_freshservice_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen Freshservice företag som administratör.

1. Klicka på menyn längst upp **Admin**.
   
    ![Administratören](./media/freshservice-tutorial/ic790814.png "Admin")

1. I den **kundportalen**, klickar du på **Security**.
   
    ![Security](./media/freshservice-tutorial/ic790815.png "säkerhet")

1. I den **Security** avsnittet, utför följande steg:
   
    ![Enkel inloggning](./media/freshservice-tutorial/ic790816.png "enkel inloggning")
   
    a. Växeln **enkel inloggning**.

    b. Välj **SAML SSO**.

    c. I den **inloggnings-URL för SAML** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    d. I den **URL för utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    e. I **Security certifikat fingeravtryck** textrutan klistra in den **TUMAVTRYCK** värdet för certifikat som du har kopierat från Azure-portalen.

    f. Klicka på **Spara**

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/freshservice-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/freshservice-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/freshservice-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/freshservice-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-freshservice-test-user"></a>Skapa en Freshservice testanvändare

Om du vill aktivera Azure AD-användare att logga in på FreshService, måste de etableras i FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **FreshService** företagets plats som administratör.

1. Klicka på menyn längst upp **Admin**.
   
    ![Administratören](./media/freshservice-tutorial/ic790814.png "Admin")

1. I den **Användarhantering** klickar du på **beställare**.
   
    ![Beställare](./media/freshservice-tutorial/ic790818.png "beställare")

1. Klicka på **nya beställaren**.
   
    ![Ny beställare](./media/freshservice-tutorial/ic790819.png "nya beställare")

1. I den **nya beställaren** avsnittet, utför följande steg:
   
    ![Ny beställaren](./media/freshservice-tutorial/ic790820.png "nya beställare")   

    a. Ange den **Förnamn** och **e-post** attribut för ett giltigt Azure Active Directory-konto som du vill etablera till relaterade textrutor.

    b. Klicka på **Spara**.
   
    >[!NOTE]
    >Azure Active Directory-kontoinnehavare hämtar ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktiv
    >  

>[!NOTE]
>Du kan använda alla andra FreshService användare konto verktyg för att skapa eller API: er som tillhandahålls av FreshService att etablera AAD-användarkonton.
>  

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Freshservice, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Freshservice**.

    ![Konfigurera enkel inloggning](./media/freshservice-tutorial/tutorial_freshservice_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Freshservice i åtkomstpanelen du bör få automatiskt loggat in på programmets Freshservice.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshservice-tutorial/tutorial_general_01.png
[2]: ./media/freshservice-tutorial/tutorial_general_02.png
[3]: ./media/freshservice-tutorial/tutorial_general_03.png
[4]: ./media/freshservice-tutorial/tutorial_general_04.png

[100]: ./media/freshservice-tutorial/tutorial_general_100.png

[200]: ./media/freshservice-tutorial/tutorial_general_200.png
[201]: ./media/freshservice-tutorial/tutorial_general_201.png
[202]: ./media/freshservice-tutorial/tutorial_general_202.png
[203]: ./media/freshservice-tutorial/tutorial_general_203.png

