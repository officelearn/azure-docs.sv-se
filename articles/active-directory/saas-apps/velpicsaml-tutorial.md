---
title: 'Självstudier: Azure Active Directory-integrering med Velpic SAML | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.openlocfilehash: eb59c61f89b3b23ce8b0a393ee1e0e842826e90b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811799"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Självstudier: Azure Active Directory-integrering med Velpic SAML

I den här självstudien får du lära dig hur du integrerar Velpic SAML med Azure Active Directory (AD Azure).

Integrera Velpic SAML med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Velpic SAML
- Du kan aktivera användarna att automatiskt få loggat in på Velpic SAML (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Velpic SAML, behöver du följande objekt:

- En Azure AD-prenumeration
- En Velpic SAML enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Velpic SAML från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-velpic-saml-from-the-gallery"></a>Att lägga till Velpic SAML från galleriet
För att konfigurera integrering av Velpic SAML i Azure AD, som du behöver lägga till Velpic SAML från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Velpic SAML från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Velpic SAML**.

    ![Skapa en Azure AD-användare för testning](./media/velpicsaml-tutorial/tutorial_velpicsaml_search.png)

1. I resultatpanelen väljer **Velpic SAML**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Velpic SAML utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Velpic SAML är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Velpic SAML upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Velpic SAML.

Om du vill konfigurera och testa Azure AD enkel inloggning med Velpic SAML, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Velpic SAML](#creating-a-velpic-saml-test-user)**  – du har en motsvarighet för Britta Simon i Velpic SAML som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Velpic SAML-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Velpic SAML:**

1. I hanteringsportalen för Azure på den **Velpic SAML** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

1. Ange informationen i den **Velpic SAML domän och URL: er** avsnittet -

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. I den **inloggnings-URL** textrutan Ange värdet som: `https://<sub-domain>.velpicsaml.net`

    b. I den **identifierare** textrutan klistra in den **”enkel inloggning på URL: en”** värde `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Observera att URL: en inloggning kommer att tillhandahållas av Velpic SAML-teamet och ID-värde ska vara tillgängliga när du konfigurerar plugin-programmet för SSO längs Velpic SAML. Du måste kopiera detta värde från Velpic SAML programsidan och klistra in den här.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_general_400.png)

1. Klicka på Konfigurera Velpic SAML om du vill öppna Konfigurera inloggnings-fönster på avsnittet Velpic SAML-konfiguration. Kopiera SAML entitets-ID från avsnittet Snabbreferens.

1. Logga in på webbplatsen Velpic SAML-företag som en administratör i ett annat webbläsarfönster.

1. Klicka på **hantera** fliken och gå till **integrering** avsnitt där du måste klicka på **plugin-program** för att skapa nya plugin-programmet för att logga In.

    ![Plugin](./media/velpicsaml-tutorial/velpic_1.png)

1. Klicka på den **”Lägg till plugin-program”** knappen.
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_2.png)

1. Klicka på den **SAML** panelen på sidan Lägg till plugin-programmet.
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_3.png)

1. Ange namnet på den nya SAML-plugin-programmet och klicka på den **”Lägg till”** knappen.

    ![Plugin](./media/velpicsaml-tutorial/velpic_4.png)

1. Ange information på följande sätt:

    ![Plugin](./media/velpicsaml-tutorial/velpic_5.png)

    a. I den **namn** textrutan skriver du namnet på SAML-plugin-programmet.

    b. I den **utfärdar-URL** textrutan klistra in den **SAML entitets-ID** du kopierade från den **konfigurera inloggning** fönstret i Azure-portalen.

    c. I den **providern Metadata Config** överför Metadata-XML-filen som du har hämtat från Azure-portalen.

    d. Du kan också välja att aktivera SAML just-in-time-etablering genom att aktivera den **”automatiskt skapa nya användare”** kryssrutan. Om en användare finns inte i Velpic och den här flaggan är inte aktiverad, misslyckas inloggningen från Azure. Om flaggan är aktiverad kommer automatiskt att tillhandahållas till Velpic användaren vid tidpunkten för inloggningen. 

    e. Kopiera den **enkel inloggning på URL: en** från texten rutan och klistra in den i Azure-portalen.
    
    f. Klicka på **Spara**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/velpicsaml-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/velpicsaml-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/velpicsaml-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/velpicsaml-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Skapa en testanvändare Velpic SAML

Det här steget krävs vanligtvis inte eftersom programmet stöder just-in-time användaretablering. Om automatisk användaretablering inte är aktiverat kan sedan manuell användarskapelse göras enligt beskrivningen nedan.

Logga in på webbplatsen Velpic SAML företagets som administratör och utför följande steg:
    
1. Klicka på Hantera fliken och gå till avsnittet för användare, och klicka sedan på nytt för att lägga till användare.

    ![Lägg till användare](./media/velpicsaml-tutorial/velpic_7.png)

1. På den **”skapa nya användare”** dialogrutan utför följande steg.

    ![Användare](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. I den **Förnamn** textrutan först namn för Britta Simon.

    b. I den **efternamn** textrutan anger efternamn för Britta Simon.

    c. I den **användarnamn** textrutan skriver du användarnamnet för Britta Simon.

    d. I textrutan **E-post** anger du e-postadressen för Britta Simon-kontot.

    e. Resten av informationen är valfri, så kan du fylla det om det behövs.
    
    f. Klicka på **SPARA**.  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Velpic SAML.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Velpic SAML, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Velpic SAML**.

    ![Konfigurera enkel inloggning](./media/velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. När du klickar på panelen Velpic SAML i åtkomstpanelen bör du få inloggningssidan i Velpic SAML-programmet. Du bör se den **”logga in med Azure AD-** knappen på inloggningssidan.

    ![Plugin](./media/velpicsaml-tutorial/velpic_6.png)

1. Klicka på den **”logga in med Azure AD-** knappen för att logga in på Velpic med hjälp av Azure AD-konto.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/velpicsaml-tutorial/tutorial_general_203.png

