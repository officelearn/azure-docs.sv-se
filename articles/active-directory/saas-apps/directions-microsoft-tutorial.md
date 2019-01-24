---
title: 'Självstudier: Azure Active Directory-integrering med information om hur Microsoft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vägbeskrivningar på Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 6e031a3893bff31814d81418692b84ef26d8f80a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819721"
---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Självstudier: Azure Active Directory-integrering med anvisningarna på Microsoft

Lär dig hur du integrerar anvisningarna på Microsoft med Azure Active Directory (AD Azure) i den här självstudien.

Integrera anvisningarna på Microsoft med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till anvisningarna på Microsoft
- Du kan aktivera användarna att automatiskt få loggat in på anvisningarna på Microsoft (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Microsoft instruktioner, behöver du följande objekt:

- En Azure AD-prenumeration
- En anvisningarna på Microsoft enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till anvisningarna på Microsoft från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-directions-on-microsoft-from-the-gallery"></a>Att lägga till anvisningarna på Microsoft från galleriet
Om du vill konfigurera integreringen av anvisningarna på Microsoft i Azure AD, som du behöver lägga till anvisningarna på Microsoft från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till anvisningarna på Microsoft från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **anvisningarna på Microsoft**.

    ![Skapa en Azure AD-användare för testning](./media/directions-microsoft-tutorial/tutorial_directionsonmicrosoft_search.png)

1. I resultatpanelen väljer **anvisningarna på Microsoft**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/directions-microsoft-tutorial/tutorial_directionsonmicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med information om hur Microsoft baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i anvisningarna på Microsoft är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i riktningar på Microsoft upprättas.

I anvisningarna på Microsoft, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med anvisningarna på Microsoft, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en anvisningarna på Microsoft testanvändare](#creating-a-directions-on-microsoft-test-user)**  – du har en motsvarighet för Britta Simon i anvisningarna på Microsoft som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din riktningar på Microsoft-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med information om hur Microsoft:**

1. I Azure-portalen på den **anvisningarna på Microsoft** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/directions-microsoft-tutorial/tutorial_directionsonmicrosoft_samlbase.png)

1. På den **anvisningarna på Microsoft-Domain och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/directions-microsoft-tutorial/tutorial_directionsonmicrosoft_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    |  |
    | --- |
    | `https://www.directionsonmicrosoft.com/user/login` |
    | `https://<subdomain>.devcloud.acquia-sites.com/<companyname>` |

    b. I textrutan **Identifierare** anger du en URL med följande mönster:
    |  |
    | --- |
    | `https://rhelmdirectionsonmicrosoftcomtest.devcloud.acquia-sites.com/simplesaml/<companyname>` |
    | `https://www.directionsonmicrosoft.com/simplesaml/<companyname>` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [anvisningarna på Microsoft Client supportteam](mailto:service@DirectionsOnMicrosoft.com) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/directions-microsoft-tutorial/tutorial_directionsonmicrosoft_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/directions-microsoft-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **anvisningarna på Microsoft** sida, som du behöver skicka de hämtade **XML-Metadata för** till [anvisningarna på Microsofts supportteam](mailto:service@DirectionsOnMicrosoft.com). Om du vill aktivera anvisningarna på Microsoft support-teamet att hitta ditt medlemskap i federerad inkludera företagets information i din e-post.
    
    >[!NOTE]
    >Enkel inloggning för anvisningarna på Microsoft måste aktiveras av den [anvisningarna på Microsoft Client supportteam](mailto:service@DirectionsOnMicrosoft.com). Du får ett meddelande när enkel inloggning har aktiverats.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/directions-microsoft-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/directions-microsoft-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/directions-microsoft-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/directions-microsoft-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-directions-on-microsoft-test-user"></a>Skapa en anvisningarna på Microsoft testanvändare

Det finns inga uppgift att konfigurera användaretablering till anvisningarna på Microsoft.  

När en tilldelad användare försöker logga in på anvisningarna på Microsoft med hjälp av åtkomstpanelen, kontrollerar anvisningarna på Microsoft om användaren finns. Om det finns inget konto ännu, skapas den automatiskt av anvisningarna på Microsoft.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till anvisningarna på Microsoft.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon anvisningarna på Microsoft, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **anvisningarna på Microsoft**.

    ![Konfigurera enkel inloggning](./media/directions-microsoft-tutorial/tutorial_directionsonmicrosoft_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
 
När du klickar på anvisningarna på Microsoft-panel i åtkomstpanelen du bör få automatiskt loggat in på ditt anvisningarna på Microsoft-program.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/directions-microsoft-tutorial/tutorial_general_01.png
[2]: ./media/directions-microsoft-tutorial/tutorial_general_02.png
[3]: ./media/directions-microsoft-tutorial/tutorial_general_03.png
[4]: ./media/directions-microsoft-tutorial/tutorial_general_04.png

[100]: ./media/directions-microsoft-tutorial/tutorial_general_100.png

[200]: ./media/directions-microsoft-tutorial/tutorial_general_200.png
[201]: ./media/directions-microsoft-tutorial/tutorial_general_201.png
[202]: ./media/directions-microsoft-tutorial/tutorial_general_202.png
[203]: ./media/directions-microsoft-tutorial/tutorial_general_203.png

