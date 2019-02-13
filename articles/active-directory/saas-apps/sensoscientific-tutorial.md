---
title: 'Självstudier: Azure Active Directory-integrering med SensoScientific trådlösa temperatur övervakningssystemet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SensoScientific trådlösa temperatur av systemet.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a625e82f41bee1b8e3980192076d24a7471953
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183419"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Självstudier: Azure Active Directory-integrering med SensoScientific trådlösa temperatur av systemet

I den här självstudien får du lära dig hur du integrerar SensoScientific trådlösa temperatur av systemet med Azure Active Directory (AD Azure).

Integrera SensoScientific trådlösa temperatur av systemet med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SensoScientific trådlösa temperatur av systemet
- Du kan aktivera användarna att automatiskt få loggat in på SensoScientific trådlösa temperatur av systemet (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SensoScientific trådlösa temperatur av systemet, behöver du följande objekt:

- En Azure AD-prenumeration
- En SensoScientific trådlösa temperatur övervakningssystemet enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SensoScientific trådlösa temperatur av systemet från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Att lägga till SensoScientific trådlösa temperatur av systemet från galleriet
För att konfigurera integrering av SensoScientific trådlösa temperatur av systemet i Azure AD, som du behöver lägga till SensoScientific trådlösa temperatur av systemet från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SensoScientific trådlösa temperatur av systemet från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **SensoScientific trådlösa temperatur övervakningssystemet**.

    ![Skapa en Azure AD-användare för testning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

1. I resultatpanelen väljer **SensoScientific trådlösa temperatur övervakningssystemet**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SensoScientific trådlösa temperatur av systemet baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SensoScientific trådlösa temperatur av systemet är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SensoScientific trådlösa temperatur av systemet upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i SensoScientific trådlösa temperatur av systemet.

Om du vill konfigurera och testa Azure AD enkel inloggning med SensoScientific trådlösa temperatur av systemet, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare SensoScientific trådlösa temperatur övervakningssystemet](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  – du har en motsvarighet för Britta Simon i SensoScientific trådlösa temperatur av systemet som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SensoScientific trådlösa temperatur av systemet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SensoScientific trådlösa temperatur av systemet:**

1. I Azure-portalen på den **SensoScientific trådlösa temperatur övervakningssystemet** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

1. På den **SensoScientific trådlösa temperatur övervakning System domän och URL: er** avsnittet, behöver du inte utföra några steg som appen är redan förintegrerade med Azure:

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_general_400.png)

1. På den **övervakning systemkonfigurationen för SensoScientific trådlösa temperatur** klickar du på **konfigurera SensoScientific trådlösa temperatur övervakningssystemet** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID** och **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

1. Logga in på ditt övervakningssystem för SensoScientific trådlösa temperatur-program som administratör.

1. På överst navigeringsmenyn klickar du på **Configuration** och gå till **konfigurera** under **Single Sign On** att öppna enkel inloggning på inställningarna.

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

1. I **enkel inloggning på inställningar** formuläret utför följande steg:
 
    a. Välj **Utfärdarens namn** som Azure AD.
    
    b. Klistra in den **SAML entitets-ID** som du har kopierat från Azure-portalen i utfärdar-URL-textrutan.
    
    c. Klistra in den **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen i enkel inloggning för tjänsten URL-textrutan.

    d. Klistra in den **URL: en för utloggning** som du har kopierat från Azure-portalen i textrutan för tjänst-URL för enkel utloggning.

    e. Bläddra certifikatet som du har hämtat från Azure-portalen och laddar upp här.
    
    f. Klicka på **Spara**.
  
> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/sensoscientific-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/sensoscientific-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/sensoscientific-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/sensoscientific-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Skapa en testanvändare SensoScientific trådlösa temperatur av systemet

Om du vill aktivera Azure AD-användare att logga in på SensoScientific trådlösa temperatur av systemet, måste de etableras i SensoScientific trådlösa temperatur av systemet. Arbeta med [SensoScientific trådlösa temperatur övervakningssystemet supportteamet](https://www.sensoscientific.com/contact-us/) att lägga till användare i plattformen SensoScientific trådlösa temperatur av systemet. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SensoScientific trådlösa temperatur av systemet.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SensoScientific trådlösa temperatur av systemet, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **SensoScientific trådlösa temperatur övervakningssystemet**.

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen. Klicka på panelen SensoScientific trådlösa temperatur av systemet i åtkomstpanelen, du kommer att automatiskt loggat in på programmets SensoScientific trådlösa temperatur av systemet. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/sensoscientific-tutorial/tutorial_general_203.png

