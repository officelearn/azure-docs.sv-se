---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler privat åtkomst (ZPA) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privat åtkomst (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6471b6bd634c7fe3053fc7748eb925c049c17c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179574"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Självstudier: Azure Active Directory-integrering med Zscaler privat åtkomst (ZPA)

I den här självstudien får du lära dig hur du integrerar Zscaler privat åtkomst (ZPA) med Azure Active Directory (AD Azure).

Integrera Zscaler privat åtkomst (ZPA) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler privat åtkomst (ZPA)
- Du kan aktivera användarna att automatiskt få loggat in till Zscaler privat åtkomst (ZPA) (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler privat åtkomst (ZPA), behöver du följande objekt:

- En Azure AD-prenumeration
- En Zscaler privat åtkomst (ZPA) enkel inloggning aktiverad prenumeration


> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.


Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Zscaler privat åtkomst (ZPA) från galleriet
1. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Att lägga till Zscaler privat åtkomst (ZPA) från galleriet
För att konfigurera integrering av Zscaler privat åtkomst (ZPA) till Azure AD, som du behöver lägga till Zscaler privat åtkomst (ZPA) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler privat åtkomst (ZPA) från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Zscaler privat åtkomst (ZPA)**.

    ![Skapa en Azure AD-användare för testning](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

1. I resultatpanelen väljer **Zscaler privat åtkomst (ZPA)**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst (ZPA) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Zscaler privat åtkomst (ZPA) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Zscaler privat åtkomst (ZPA) upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Zscaler privat åtkomst (ZPA).

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler privat åtkomst (ZPA), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **Skapa en testanvändare Zscaler privat åtkomst (ZPA)** – du har en motsvarighet för Britta Simon i Zscaler privat åtkomst (ZPA) som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt program med Zscaler privat åtkomst (ZPA).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler privat åtkomst (ZPA):**

1. I hanteringsportalen för Azure på den **Zscaler privat åtkomst (ZPA)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
1. På den **Zscaler privat åtkomst (ZPA)-domän och URL: er** avsnittet, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. I den **inloggning på URL: en** textrutan anger du ett URL med hjälp av följande mönster: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. I den **identifierare** textrutan typ: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera dessa värden med faktiska logga på URL och identifierare. Här föreslår vi att du för att använda det unika värdet av URL: en i identifierare. Kontakta [Zscaler privat åtkomst (ZPA) supportteamet](https://help.zscaler.com/zpa-submit-ticket) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Skapa nytt certifikat**.

    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_general_400.png)     

1. På den **Skapa nytt certifikat** dialogrutan klickar du på kalenderikonen och väljer en **förfallodatum**. Klicka sedan på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_general_500.png)

1. På den **SAML-signeringscertifikat** väljer **gör nytt certifikat aktivt** och klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

1. På popup-fönstret **förnyelsecertifikatet** fönstret klickar du på **OK**.

    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_general_600.png)

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

1. Logga in på webbplatsen för företagets Zscaler privat åtkomst (ZPA) som en administratör i ett annat webbläsarfönster.

1. Gå till **administratör** och klicka sedan på **Idp-konfigurationen**.

    ![Konfigurera enkel inloggning på appsidan](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

1. I den **Idp-konfigurationen** klickar du på **lägga till nya IDP-konfigurationen**.

    ![Konfigurera enkel inloggning på appsidan](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

1. I den **nya IDP-konfigurationen** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning på appsidan](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Klicka på **Välj fil** och ladda upp din hämtade metadatafilen.

    b. Klicka på knappen **Spara**.
    


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/zscalerprivateaccess-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/zscalerprivateaccess-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/zscalerprivateaccess-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Skapa en testanvändare Zscaler privat åtkomst (ZPA)

I det här avsnittet skapar du en användare som kallas Britta Simon i Zscaler privat åtkomst (ZPA). Kontakta [Zscaler privat åtkomst (ZPA) supportteamet](https://help.zscaler.com/zpa-submit-ticket) att lägga till användare i Zscaler privat åtkomst (ZPA)-plattformen.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst till Zscaler privat åtkomst (ZPA).

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon till Zscaler privat åtkomst (ZPA), utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Zscaler privat åtkomst (ZPA)**.

    ![Konfigurera enkel inloggning](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler privat åtkomst (ZPA) i åtkomstpanelen du bör få automatiskt loggat in på programmets Zscaler privat åtkomst (ZPA).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccess-tutorial/tutorial_general_203.png
