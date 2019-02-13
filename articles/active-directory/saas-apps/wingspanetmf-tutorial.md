---
title: 'Självstudier: Azure Active Directory-integrering med Wingspan eTMF | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wingspan eTMF.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ace320d3-521c-449c-992f-feabe7538de7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eafaf6a724b3e09c808bd20f7097f796fff4069
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166155"
---
# <a name="tutorial-azure-active-directory-integration-with-wingspan-etmf"></a>Självstudier: Azure Active Directory-integrering med Wingspan eTMF

I den här självstudien får du lära dig hur du integrerar Wingspan eTMF med Azure Active Directory (AD Azure).

Integrera Wingspan eTMF med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Wingspan eTMF
- Du kan aktivera användarna att automatiskt få loggat in på Wingspan eTMF (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Wingspan eTMF, behöver du följande objekt:

- En Azure AD-prenumeration
- En Wingspan eTMF enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Wingspan eTMF från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-wingspan-etmf-from-the-gallery"></a>Att lägga till Wingspan eTMF från galleriet
För att konfigurera integrering av Wingspan eTMF i Azure AD, som du behöver lägga till Wingspan eTMF från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Wingspan eTMF från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Wingspan eTMF**.

    ![Skapa en Azure AD-användare för testning](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_search.png)

1. I resultatpanelen väljer **Wingspan eTMF**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Wingspan eTMF baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Wingspan eTMF är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Wingspan eTMF upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Wingspan eTMF.

Om du vill konfigurera och testa Azure AD enkel inloggning med Wingspan eTMF, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för Wingspan eTMF](#creating-a-wingspan-etmf-test-user)**  – du har en motsvarighet för Britta Simon i Wingspan eTMF som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Wingspan eTMF program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Wingspan eTMF:**

1. I Azure-portalen på den **Wingspan eTMF** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_samlbase.png)

1. På den **Wingspan eTMF domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_url11.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<customer name>.<instance name>.mywingspan.com/saml`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `http://saml.<instance name>.wingspan.com/shibboleth`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<customer name>.<instance name>.mywingspan.com/`
     
    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och svars-URL, inklusive faktiska kund- och instansnamn. Kontakta [Wingspan eTMF klienten supportteamet](http://www.wingspan.com/contact-us/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/wingspanetmf-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **Wingspan eTMF** sida, som du behöver skicka de hämtade **XML-Metadata för** till [Wingspan eTMF support](http://www.wingspan.com/contact-us/). De har konfigurerat detta för att ha SAML SSO-anslutning som är korrekt inställda på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/wingspanetmf-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/wingspanetmf-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/wingspanetmf-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/wingspanetmf-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-wingspan-etmf-test-user"></a>Skapa en Wingspan eTMF testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Wingspan eTMF. Arbeta med [Wingspan eTMF support](http://www.wingspan.com/contact-us/) att lägga till användare i Wingspan eTMF programmet. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Wingspan eTMF.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Wingspan eTMF, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Wingspan eTMF**.

    ![Konfigurera enkel inloggning](./media/wingspanetmf-tutorial/tutorial_wingspanetmf_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen. 

Klicka på panelen Wingspan eTMF i åtkomstpanelen, omdirigeras du till organisation logga på sidan. Efter genomförd inloggning du kommer att loggat in på ditt Wingspan eTMF program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/wingspanetmf-tutorial/tutorial_general_01.png
[2]: ./media/wingspanetmf-tutorial/tutorial_general_02.png
[3]: ./media/wingspanetmf-tutorial/tutorial_general_03.png
[4]: ./media/wingspanetmf-tutorial/tutorial_general_04.png

[100]: ./media/wingspanetmf-tutorial/tutorial_general_100.png

[200]: ./media/wingspanetmf-tutorial/tutorial_general_200.png
[201]: ./media/wingspanetmf-tutorial/tutorial_general_201.png
[202]: ./media/wingspanetmf-tutorial/tutorial_general_202.png
[203]: ./media/wingspanetmf-tutorial/tutorial_general_203.png

