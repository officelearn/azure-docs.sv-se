---
title: 'Självstudier: Azure Active Directory-integration med Thoughtworks Mingle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a685b5702aa9f74f3e0abf2a06774a30ac0d996f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437002"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Självstudier: Azure Active Directory-integration med Thoughtworks Mingle

I den här självstudien får du lära dig hur du integrerar Thoughtworks Mingle med Azure Active Directory (AD Azure).

Integrera Thoughtworks Mingle med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Thoughtworks Mingle
- Du kan aktivera användarna att automatiskt få loggat in på Thoughtworks Mingle (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Thoughtworks Mingle, behöver du följande objekt:

- En Azure AD-prenumeration
- En Thoughtworks Mingle enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Thoughtworks Mingle från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Att lägga till Thoughtworks Mingle från galleriet
För att konfigurera integrering av Thoughtworks Mingle i Azure AD, som du behöver lägga till Thoughtworks Mingle från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Thoughtworks Mingle från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Thoughtworks Mingle**väljer **Thoughtworks Mingle** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Thoughtworks Mingle i resultatlistan](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Thoughtworks Mingle baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Thoughtworks Mingle är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Thoughtworks Mingle upprättas.

I Thoughtworks Mingle, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Thoughtworks Mingle, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Thoughtworks Mingle](#create-a-thoughtworks-mingle-test-user)**  – du har en motsvarighet för Britta Simon i Thoughtworks Mingle som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Thoughtworks Mingle program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Thoughtworks Mingle:**

1. I Azure-portalen på den **Thoughtworks Mingle** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

1. På den **Thoughtworks Mingle domän och URL: er** avsnittet, utför följande steg:

    ![Thoughtworks Mingle domän och URL: er med enkel inloggning för information](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med faktiska inloggnings-URL: en. Kontakta [Thoughtworks Mingle klienten supportteamet](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) att hämta värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/thoughtworks-mingle-tutorial/tutorial_general_400.png)

1. Logga in på din **Thoughtworks Mingle** företagets plats som administratör.

1. Klicka på den **Admin** fliken och klicka sedan på **SSO Config**.
   
    ![Fliken Administration](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO-konfiguration")

1. I den **SSO Config** avsnittet, utför följande steg:
   
    ![SSO-Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO-konfiguration")
    
    a. Om du vill ladda upp metadatafilen, klickar du på **Välj fil**. 

    b. Klicka på **spara ändringar**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/thoughtworks-mingle-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/thoughtworks-mingle-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/thoughtworks-mingle-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>Skapa en Thoughtworks Mingle testanvändare

För Azure AD-användare för att kunna logga in, måste de etableras till programmet Thoughtworks Mingle med deras Azure Active Directory-användarnamn. När det gäller Thoughtworks Mingle är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på webbplatsen Thoughtworks Mingle företag som administratör.

1. Klicka på **profil**.
   
    ![Ditt första projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "ditt första projekt")

1. Klicka på den **Admin** fliken och klicka sedan på **användare**.
   
    ![Användare](./media/thoughtworks-mingle-tutorial/ic785161.png "användare")

1. Klicka på **ny användare**.
   
    ![Ny användare](./media/thoughtworks-mingle-tutorial/ic785162.png "ny användare")

1. På den **ny användare** dialogrutan utför följande steg:
   
    ![Dialogrutan Ny användare](./media/thoughtworks-mingle-tutorial/ic785163.png "ny användare")  
 
    a. Skriv den **inloggningsnamn**, **visningsnamn**, **Välj lösenord**, **Bekräfta lösenord** av en giltig Azure AD-konto som du vill etablera i den relaterade textrutor. 

    b. Som **användartyp**väljer **användarens fullständiga**.

    c. Klicka på **skapa den här profilen**.

>[!NOTE]
>Du kan använda alla andra Thoughtworks Mingle användare konto verktyg för att skapa eller API: er som tillhandahålls av Thoughtworks Mingle att etablera AAD-användarkonton.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Thoughtworks Mingle.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Thoughtworks Mingle, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Thoughtworks Mingle**.

    ![Länken Thoughtworks Mingle i listan med program](./media/thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Thoughtworks Mingle i åtkomstpanelen du bör få automatiskt loggat in på ditt Thoughtworks Mingle program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/thoughtworks-mingle-tutorial/tutorial_general_203.png

