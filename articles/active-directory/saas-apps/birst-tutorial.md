---
title: 'Självstudier: Azure Active Directory-integrering med Birst flexibel affärsanalys | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Birst flexibel affärsanalys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 761db64856f0ea6068512e4ba6f9ddcff403244c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821098"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Självstudier: Azure Active Directory-integrering med Birst flexibel Företagsanalys

Lär dig hur du integrerar Birst flexibel Business Analytics med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Birst flexibel Business Analytics med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Birst flexibel Företagsanalys
- Du kan aktivera användarna att automatiskt få loggat in på Birst flexibel affärsanalys (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Birst flexibel affärsanalys, behöver du följande objekt:

- En Azure AD-prenumeration
- En Birst flexibel affärsanalys enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Birst flexibel affärsanalys från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Att lägga till Birst flexibel affärsanalys från galleriet
För att konfigurera integrering av Birst flexibel affärsanalys i Azure AD, som du behöver lägga till Birst flexibel affärsanalys från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Birst flexibel affärsanalys från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Birst flexibel affärsanalys**.

    ![Skapa en Azure AD-användare för testning](./media/birst-tutorial/tutorial_birst_search.png)

1. I resultatpanelen väljer **Birst flexibel affärsanalys**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Birst flexibel affärsanalys baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Birst flexibel Business Analytics är en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Birst flexibel BA upprättas.

I Birst flexibel affärsanalys, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Birst flexibel affärsanalys, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Birst flexibel affärsanalys](#creating-a-birst-agile-business-analytics-test-user)**  – du har en motsvarighet för Britta Simon i Birst flexibel Företagsanalys som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Birst flexibel Business Analytics.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Birst flexibel Affärsanalys:**

1. I Azure-portalen på den **Birst flexibel affärsanalys** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/birst-tutorial/tutorial_birst_samlbase.png)

1. På den **Birst flexibel Business Analytics domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/birst-tutorial/tutorial_birst_url.png)

     I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     URL: en är beroende av datacenter att ditt konto Birst finns: 

     * För USA användning på datacenter följer mönstret: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` 

     * Använd följande mönster för datacenter för Europa: `https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Birst flexibel Business Analytics Client supportteamet](mailto:info@birst.com) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/birst-tutorial/tutorial_birst_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/birst-tutorial/tutorial_general_400.png)

1. På den **Birst flexibel Business Analytics Configuration** klickar du på **konfigurera Birst flexibel affärsanalys** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/birst-tutorial/tutorial_birst_configure.png) 

1. Att konfigurera enkel inloggning på **Birst flexibel affärsanalys** sida, som du behöver skicka de hämtade **certifikat (Base64)**, **URL för utloggning, SAML entitets-ID och SAML enkel inloggning Tjänst-URL för** till [Birst flexibel affärsanalys supportteam](mailto:info@birst.com). 

    > [!NOTE]
    > Nämner till Birst-teamet att den här integreringen behöver SHA256-algoritmen (SHA1 stöds inte) så att de kan ställa in den enkel inloggning på rätt server som **app2101** osv.
  

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/birst-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/birst-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/birst-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/birst-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Skapa en testanvändare Birst flexibel Företagsanalys

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Birst flexibel BA. Arbeta med [Birst flexibel affärsanalys supportteam](mailto:info@birst.com) att lägga till användare i Birst-konto. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Birst flexibel affärsanalys.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Birst flexibel affärsanalys, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Birst flexibel affärsanalys**.

    ![Konfigurera enkel inloggning](./media/birst-tutorial/tutorial_birst_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Birst flexibel affärsanalys i åtkomstpanelen du bör få automatiskt loggat in på programmets Birst flexibel affärsanalys. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/birst-tutorial/tutorial_general_01.png
[2]: ./media/birst-tutorial/tutorial_general_02.png
[3]: ./media/birst-tutorial/tutorial_general_03.png
[4]: ./media/birst-tutorial/tutorial_general_04.png

[100]: ./media/birst-tutorial/tutorial_general_100.png

[200]: ./media/birst-tutorial/tutorial_general_200.png
[201]: ./media/birst-tutorial/tutorial_general_201.png
[202]: ./media/birst-tutorial/tutorial_general_202.png
[203]: ./media/birst-tutorial/tutorial_general_203.png

