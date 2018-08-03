---
title: 'Självstudier: Azure Active Directory-integration med RFPIO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 04ba94e3263af03279b74b4832b8291ad6414274
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426568"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Självstudier: Azure Active Directory-integration med RFPIO

I den här självstudien får du lära dig hur du integrerar RFPIO med Azure Active Directory (AD Azure).

Integrera RFPIO med Azure AD ger dig följande fördelar:

- Du kan styra vem i Azure AD som har åtkomst till RFPIO.
- Du kan aktivera användarna att automatiskt få loggat in på RFPIO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med RFPIO, behöver du följande objekt:

- En Azure AD-prenumeration.
- En RFPIO enkel inloggning på aktiverad prenumeration.

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägger till RFPIO från galleriet.
1. Konfigurera och testa Azure AD enkel inloggning.

## <a name="add-rfpio-from-the-gallery"></a>Lägg till RFPIO från galleriet
För att konfigurera integrering av RFPIO i Azure AD, som du behöver lägga till RFPIO från galleriet i din lista över hanterade SaaS-appar.

### <a name="to-add-rfpio-from-the-gallery"></a>Lägga till RFPIO från galleriet

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret, väljer den **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Välj **företagsprogram**, och välj sedan **alla program**.

    ![Program][2]
    
1. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **RFPIO**.

    ![Skapa en Azure AD-användare för testning](./media/rfpio-tutorial/tutorial_rfpio_search.png)

1. I resultatpanelen väljer **RFPIO**, och välj sedan den **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med RFPIO baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till relationen mellan motsvarande användare i RFPIO och en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i RFPIO upprättas.

I RFPIO, tilldela värdet för **användarnamn** i Azure AD som värde för **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med RFPIO, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**--vill tillåta att användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**– testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare RFPIO](#creating-a-rfpio-test-user)**  – har en motsvarighet för Britta Simon i RFPIO som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**– aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  --att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt RFPIO program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med RFPIO:**

1. I Azure-portalen på den **RFPIO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_rfpio_samlbase.png)

1. På den **RFPIO domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_rfpio_url.png)

    a. I den **identifierare** textrutan anger du URL: `https://www.rfpio.com`

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Kontrollera **visa avancerade URL-inställningar**.

    c. I den **Vidarebefordransstatus** textrutan anger du ett strängvärde. Kontakta [RFPIO supportteam](https://www.rfpio.com/contact/) att hämta det här värdet. 

1. Kontrollera **visa avancerade URL-inställningar**. Om du vill konfigurera programmet i **SP** initierade läge: 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_rfpio_url2.png)

    I den **inloggnings-URL** textrutan anger du URL: `https://www.app.rfpio.com`

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_rfpio_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster, logga in på den **RFPIO** webbplats som administratör.

1. Klicka på listrutan längst ned till vänster.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app1.png)

1. Klicka på den **organisationsinställningar**. 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app2.png)

1. Klicka på den **funktioner och integrering**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app4.png)

1. I den **SAML SSO-konfiguration** klickar du på **redigera**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app3.png)

1. I det här avsnittet utför du följande åtgärder:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app5.png)
    
    a. Kopiera innehållet i den **XML-Metadata för hämtade** och klistra in den i den **identitet configuration** fält.

    > [!NOTE]
    >Om du vill kopiera innehållet i nedladdade **XML-Metadata för** Använd **anteckningar ++** eller till rätt **XML-redigerare**. 

    b. Klicka på **Validera**.

    c. När du klickar på **Validera**, vänd **SAML(Enabled)** till på.

    d. Klicka på **skicka**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/rfpio-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/rfpio-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/rfpio-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/rfpio-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-rfpio-test-user"></a>Skapa en RFPIO testanvändare

Om du vill aktivera Azure AD-användare att logga in på RFPIO, måste de etableras i RFPIO.  
När det gäller RFPIO är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen RFPIO företag som administratör.

1. Klicka på listrutan längst ned till vänster.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app1.png)

1. Klicka på den **organisationsinställningar**. 

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app2.png)

1. Klicka på **TEAMMEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app6.png)

1. Klicka på **Lägg till MEDLEMMAR**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app7.png)

1. I den **lägga till nya medlemmar** avsnittet. Utför följande åtgärder:

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/app8.png)

    a. Ange **e-postadress** i den **ange en e-postadress per rad** fält.

    b. Välj igen **rollen** enligt dina krav.

    c. Klicka på **Lägg till MEDLEMMAR**.
        
    > [!NOTE]
    > Azure Active Directory-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till RFPIO.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon RFPIO, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **RFPIO**.

    ![Konfigurera enkel inloggning](./media/rfpio-tutorial/tutorial_rfpio_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen RFPIO i åtkomstpanelen du bör få automatiskt loggat in på ditt RFPIO program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/rfpio-tutorial/tutorial_general_01.png
[2]: ./media/rfpio-tutorial/tutorial_general_02.png
[3]: ./media/rfpio-tutorial/tutorial_general_03.png
[4]: ./media/rfpio-tutorial/tutorial_general_04.png

[100]: ./media/rfpio-tutorial/tutorial_general_100.png

[200]: ./media/rfpio-tutorial/tutorial_general_200.png
[201]: ./media/rfpio-tutorial/tutorial_general_201.png
[202]: ./media/rfpio-tutorial/tutorial_general_202.png
[203]: ./media/rfpio-tutorial/tutorial_general_203.png

