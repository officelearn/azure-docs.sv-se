---
title: 'Självstudier: Azure Active Directory-integrering med tillgången Bank | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och tillgången Bank.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3006ad6e-8831-41cd-94aa-7e7ae770ce7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e403da9d5711e6d0c6c79ba4cabfdbc263193367
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197848"
---
# <a name="tutorial-azure-active-directory-integration-with-asset-bank"></a>Självstudier: Azure Active Directory-integrering med tillgången Bank

Lär dig hur du integrerar tillgången Bank med Azure Active Directory (AD Azure) i den här självstudien.

Integrera tillgången Bank med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har tillgång till tillgången
- Du kan aktivera användarna att automatiskt få loggat in på tillgången Bank (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med tillgången Bank, behöver du följande objekt:

- En Azure AD-prenumeration
- En tillgång Bank enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. När du lägger till tillgången Bank från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-asset-bank-from-the-gallery"></a>När du lägger till tillgången Bank från galleriet
För att konfigurera integrering av tillgången Bank i Azure AD, som du behöver lägga till tillgången Bank från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till tillgången Bank från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **tillgången Bank**.

    ![Skapa en Azure AD-användare för testning](./media/assetbank-tutorial/tutorial_assetbank_search.png)

1. I resultatpanelen väljer **tillgången Bank**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/assetbank-tutorial/tutorial_assetbank_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med tillgången Bank baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i tillgången Bank är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i tillgången Bank upprättas.

I tillgången Bank, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med tillgången Bank, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en tillgång Bank testanvändare](#creating-an-asset-bank-test-user)**  – du har en motsvarighet för Britta Simon i tillgången Bank som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för tillgången Bank.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med tillgången Bank:**

1. I Azure-portalen på den **tillgången Bank** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/assetbank-tutorial/tutorial_assetbank_samlbase.png)

1. På den **tillgången Bank domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/assetbank-tutorial/tutorial_assetbank_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.assetbank-server.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.assetbank-server.com/shibboleth`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [tillgången Bank klienten supportteamet](mailto:support@assetbank.co.uk) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/assetbank-tutorial/tutorial_assetbank_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/assetbank-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **tillgången Bank** sida, som du behöver skicka de hämtade **XML-Metadata för** till [tillgången Bank supportteamet](mailto:support@assetbank.co.uk). 


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/assetbank-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/assetbank-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/assetbank-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/assetbank-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-asset-bank-test-user"></a>Skapa en tillgång Bank testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i tillgången Bank. Tillgången Bank stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt tillgången Bank om det inte finns ännu. 

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [tillgången Bank supportteamet](mailto:support@assetbank.co.uk).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till tillgången Bank.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon tillgången Bank, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **tillgången Bank**.

    ![Konfigurera enkel inloggning](./media/assetbank-tutorial/tutorial_assetbank_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen tillgången Bank i åtkomstpanelen du bör få automatiskt loggat in på ditt tillgången Bank-program. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/assetbank-tutorial/tutorial_general_01.png
[2]: ./media/assetbank-tutorial/tutorial_general_02.png
[3]: ./media/assetbank-tutorial/tutorial_general_03.png
[4]: ./media/assetbank-tutorial/tutorial_general_04.png

[100]: ./media/assetbank-tutorial/tutorial_general_100.png

[200]: ./media/assetbank-tutorial/tutorial_general_200.png
[201]: ./media/assetbank-tutorial/tutorial_general_201.png
[202]: ./media/assetbank-tutorial/tutorial_general_202.png
[203]: ./media/assetbank-tutorial/tutorial_general_203.png

