---
title: 'Självstudier: Azure Active Directory-integrering med PerformanceCentre | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: d363f47301517e7d4f8d8f8fdf45ffd8c146279e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165174"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Självstudier: Azure Active Directory-integrering med PerformanceCentre

I den här självstudien får du lära dig hur du integrerar PerformanceCentre med Azure Active Directory (AD Azure).

Integrera PerformanceCentre med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till PerformanceCentre
- Du kan aktivera användarna att automatiskt få loggat in på PerformanceCentre (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med PerformanceCentre, behöver du följande objekt:

- En Azure AD-prenumeration
- En PerformanceCentre enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till PerformanceCentre från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-performancecentre-from-the-gallery"></a>Att lägga till PerformanceCentre från galleriet
För att konfigurera integrering av PerformanceCentre i Azure AD, som du behöver lägga till PerformanceCentre från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PerformanceCentre från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **PerformanceCentre**.

    ![Skapa en Azure AD-användare för testning](./media/performancecentre-tutorial/tutorial_performancecentre_search.png)

1. I resultatpanelen väljer **PerformanceCentre**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med PerformanceCentre baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i PerformanceCentre är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i PerformanceCentre upprättas.

I PerformanceCentre, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med PerformanceCentre, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare PerformanceCentre](#creating-a-performancecentre-test-user)**  – du har en motsvarighet för Britta Simon i PerformanceCentre som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt PerformanceCentre program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med PerformanceCentre:**

1. I Azure-portalen på den **PerformanceCentre** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

1. På den **PerformanceCentre domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `http://companyname.performancecentre.com/saml/SSO`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [PerformanceCentre klienten supportteamet](https://www.performancecentre.com/contact-us/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/performancecentre-tutorial/tutorial_general_400.png)

1. På den **PerformanceCentre Configuration** klickar du på **konfigurera PerformanceCentre** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/performancecentre-tutorial/tutorial_performancecentre_configure.png) 

1. Inloggning till din **PerformanceCentre** företagets plats som administratör.

1. Klicka på fliken på vänster sida **konfigurera**.
   
    ![Azure AD enkel inloggning][10]

1. Klicka på fliken på vänster sida **diverse**, och klicka sedan på **Single Sign On**.
   
    ![Azure AD enkel inloggning][11]

1. Som **protokollet**väljer **SAML**.
   
    ![Azure AD enkel inloggning][12]

1. Öppna din hämtade metadatafilen i anteckningar, kopiera innehållet, klistra in den i den **identitet providern Metadata** textrutan och klicka sedan på **spara**.
   
    ![Azure AD enkel inloggning][13]

1. Kontrollera att värdena för den **entitet bas-URL** och **Entity ID URL** är korrekta.
    
     ![Azure AD enkel inloggning][14]

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/performancecentre-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/performancecentre-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/performancecentre-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/performancecentre-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-performancecentre-test-user"></a>Skapa en PerformanceCentre testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i PerformanceCentre.

**Om du vill skapa en användare som kallas Britta Simon i PerformanceCentre, utför du följande steg:**

1. Logga in på webbplatsen PerformanceCentre företag som administratör.

1. I menyn till vänster, klickar du på **Interrelate**, och klicka sedan på **skapa deltagare**.
   
    ![Skapa användare][400]

1. På den **samband – skapa deltagare** dialogrutan utför följande steg:
   
    ![Skapa användare][401]
    
    a. Ange attributen som krävs för Britta Simon i relaterade textrutor.
    
    >[!IMPORTANT]
    >Brittas användarnamn-attribut i PerformanceCentre måste vara samma som användarnamnet i Azure AD.
    
    b. Välj **Klientadministratör** som **väljer du rollen**.
    
    c. Klicka på **Spara**. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PerformanceCentre.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon PerformanceCentre, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **PerformanceCentre**.

    ![Konfigurera enkel inloggning](./media/performancecentre-tutorial/tutorial_performancecentre_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.  

När du klickar på panelen PerformanceCentre i åtkomstpanelen du bör få automatiskt loggat in på ditt PerformanceCentre program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png

