---
title: 'Självstudier: Azure Active Directory-integrering med StatusPage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4494996ed54b25be71367dd3e3043023d0958074
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224047"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Självstudier: Azure Active Directory-integrering med StatusPage

I den här självstudien får du lära dig hur du integrerar StatusPage med Azure Active Directory (AD Azure).

Integrera StatusPage med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till StatusPage
- Du kan aktivera användarna att automatiskt få loggat in på StatusPage (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med StatusPage, behöver du följande objekt:

- En Azure AD-prenumeration
- En StatusPage enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till StatusPage från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-statuspage-from-the-gallery"></a>Att lägga till StatusPage från galleriet
För att konfigurera integrering av StatusPage i Azure AD, som du behöver lägga till StatusPage från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till StatusPage från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **StatusPage**.

    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_search.png)

1. I resultatpanelen väljer **StatusPage**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med StatusPage baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i StatusPage är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i StatusPage upprättas.

I StatusPage, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med StatusPage, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare StatusPage](#creating-a-statuspage-test-user)**  – du har en motsvarighet för Britta Simon i StatusPage som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt StatusPage program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med StatusPage:**

1. I Azure-portalen på den **StatusPage** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

1. På den **StatusPage domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster:

    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. I textrutan **Svars-URL** anger du en URL med följande mönster: 
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

   > [!NOTE]
   > Kontakta supporten StatusPage på [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)att begära metadata som behövs för att konfigurera enkel inloggning. 
   > 
   > a. Kopiera utfärdarvärdet från metadata, och klistra in den i den **identifierare** textrutan.
   > 
   > b. Från metadata, kopiera svars-URL och klistra in den i den **svars-URL** textrutan.

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_general_400.png)

1. På den **StatusPage Configuration** klickar du på **konfigurera StatusPage** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

1. I ett nytt webbläsarfönster inloggning till webbplatsen StatusPage företag som administratör.

1. I verktygsfältet klickar du på **Hantera konto**.
   
    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

1. Klicka på den **enkel inloggning** fliken. 
   
    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

1. På konfigurationssidan för enkel inloggning utför du följande steg:
   
      ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

      ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
      a. I den **mål-URL för enkel inloggning** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

      b. Öppna din nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan. 

      c. Klicka på **Spara konfiguration**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-statuspage-test-user"></a>Skapa en StatusPage testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i StatusPage.

StatusPage har stöd för just-in-time-etablering. Du har redan har aktiverat det i [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

**Om du vill skapa en användare som kallas Britta Simon i StatusPage, utför du följande steg:**

1. Inloggning till webbplatsen StatusPage företag som administratör.

1. Klicka på menyn längst upp **Hantera konto**.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på den **teammedlemmar** fliken. 
   
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klicka på **Lägg till GRUPPMEDLEM**. 
   
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Skriv den **e-postadress**, **Förnamn**, och **efternamn** av en giltig användare som du vill etablera till relaterade textrutor. 
   
    ![Skapa en Azure AD-användare för testning](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Som **rollen**, Välj **Klientadministratör**.

1. Klicka på **skapa konto**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till StatusPage.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon StatusPage, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **StatusPage**.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen StatusPage i åtkomstpanelen du bör få automatiskt loggat in på ditt StatusPage program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

