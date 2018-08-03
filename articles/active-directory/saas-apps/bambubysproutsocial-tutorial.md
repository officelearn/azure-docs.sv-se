---
title: 'Självstudier: Azure Active Directory-integration med Bambu av brysselkål sociala | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bambu av brysselkål sociala.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d2b9ddbc-cab7-40d6-aca1-5b171cab4199
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 1240049fd84633365aa55cd07dfb0e4ef75d24d5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447514"
---
# <a name="tutorial-azure-active-directory-integration-with-bambu-by-sprout-social"></a>Självstudier: Azure Active Directory-integration med Bambu av brysselkål sociala

I den här självstudien får du lära dig hur du integrerar Bambu av brysselkål sociala med Azure Active Directory (AD Azure).

Integrera Bambu av brysselkål sociala med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bambu av brysselkål sociala
- Du kan aktivera användarna att automatiskt få loggat in på Bambu av brysselkål sociala (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bambu av brysselkål sociala, behöver du följande objekt:

- En Azure AD-prenumeration
- En Bambu av brysselkål sociala vid enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Bambu av brysselkål sociala från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bambu-by-sprout-social-from-the-gallery"></a>Att lägga till Bambu av brysselkål sociala från galleriet
För att konfigurera integrering av Bambu av brysselkål sociala i Azure AD, som du behöver lägga till Bambu av brysselkål sociala från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bambu av brysselkål sociala från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Klicka på **nytt program** knappen överst i dialogrutan för att lägga till nytt program.

    ![Program][3]

1. I sökrutan skriver **Bambu av brysselkål sociala**.

    ![Skapa en Azure AD-användare för testning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_search.png)

1. I resultatpanelen väljer **Bambu av brysselkål sociala**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Bambu av brysselkål sociala baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Bambu av brysselkål sociala är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Bambu av brysselkål sociala upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Bambu av brysselkål sociala.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bambu av brysselkål sociala, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Bambu av brysselkål sociala testanvändare](#creating-a-bambu-by-sprout-social-test-user)**  – du har en motsvarighet för Britta Simon i Bambu av brysselkål sociala som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Bambu av brysselkål sociala program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bambu av brysselkål sociala:**

1. I Azure-portalen på den **Bambu av brysselkål sociala** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_samlbase.png)

1. På den **Bambu av brysselkål sociala domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure. 

    ![Konfigurera enkel inloggning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_url.png)

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/bambubysproutsocial-tutorial/tutorial_general_400.png)
    
1. På den **Bambu av brysselkål sociala konfigurationen** klickar du på **konfigurera Bambu av brysselkål sociala** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_configure.png) 

1. Att konfigurera enkel inloggning på **Bambu av brysselkål sociala** sida, som du behöver skicka de hämtade **XML-Metadata för** och **SAML enkel inloggning för tjänst-URL** till [ Bambu av brysselkål sociala support](mailto:support@getbambu.com). De kommer har konfigurerat för att få SAML SSO-anslutningen korrekt inställda på båda sidorna.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

<!--### Next steps

To ensure users can sign-in to Bambu by Sprout Social after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Bambu by Sprout Social prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Bambu by Sprout Social in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Bambu by Sprout Social users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/bambubysproutsocial-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/bambubysproutsocial-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/bambubysproutsocial-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/bambubysproutsocial-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **Britta Simon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för Britta Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-bambu-by-sprout-social-test-user"></a>Skapa en Bambu av brysselkål sociala testanvändare

Programmet stöder bara i tid användaretablering och -autentiserade användare kommer att skapas i programmet automatiskt.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Bambu av brysselkål sociala.

![Tilldela användare][200] 

**Om du vill tilldela Bambu av brysselkål sociala Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Bambu av brysselkål sociala**.

    ![Konfigurera enkel inloggning](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Bambu av brysselkål sociala panel i åtkomstpanelen du bör få automatiskt loggat in på ditt Bambu av brysselkål sociala program. Mer information om åtkomstpanelen finns i [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bambubysproutsocial-tutorial/tutorial_general_01.png
[2]: ./media/bambubysproutsocial-tutorial/tutorial_general_02.png
[3]: ./media/bambubysproutsocial-tutorial/tutorial_general_03.png
[4]: ./media/bambubysproutsocial-tutorial/tutorial_general_04.png

[100]: ./media/bambubysproutsocial-tutorial/tutorial_general_100.png

[200]: ./media/bambubysproutsocial-tutorial/tutorial_general_200.png
[201]: ./media/bambubysproutsocial-tutorial/tutorial_general_201.png
[202]: ./media/bambubysproutsocial-tutorial/tutorial_general_202.png
[203]: ./media/bambubysproutsocial-tutorial/tutorial_general_203.png
