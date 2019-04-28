---
title: 'Självstudier: Azure Active Directory-integrering med xMatters OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5ec711f0e43d9d29d962d43ed8b1d86338db87
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114949"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Självstudier: Azure Active Directory-integrering med xMatters OnDemand

I den här självstudien får du lära dig hur du integrerar xMatters OnDemand med Azure Active Directory (AD Azure).

Integrera xMatters OnDemand med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till xMatters OnDemand
- Du kan aktivera användarna att automatiskt få loggat in på xMatters OnDemand (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med xMatters OnDemand, behöver du följande objekt:

- En Azure AD-prenumeration
- En xMatters OnDemand enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till xMatters OnDemand från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Att lägga till xMatters OnDemand från galleriet
För att konfigurera integrering av xMatters OnDemand i Azure AD, som du behöver lägga till xMatters OnDemand från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till xMatters OnDemand från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **xMatters OnDemand**.

    ![Skapa en Azure AD-användare för testning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. I resultatpanelen väljer **xMatters OnDemand**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med xMatters OnDemand baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i xMatters OnDemand är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i xMatters OnDemand måste upprättas.

I xMatters OnDemand, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med xMatters OnDemand, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för xMatters OnDemand](#creating-a-xmatters-ondemand-test-user)**  – du har en motsvarighet för Britta Simon i xMatters OnDemand som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din xMatters OnDemand-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med xMatters OnDemand:**

1. I Azure-portalen på den **xMatters OnDemand** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. På den **xMatters OnDemand domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. I textrutan **Identifierare** anger du en URL med följande mönster:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. I textrutan **Svars-URL** anger du en URL med följande mönster:
    
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [xMatters OnDemand supportteam](https://www.xmatters.com/company/contact-us/) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen lokalt som **c:\\XMatters OnDemand.cer**.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Behöver du vidarebefordra certifikatet som den [xMatters OnDemand supportteam](https://www.xmatters.com/company/contact-us/). Certifikatet måste överföras av supportteamet xMatters innan du kan slutföra konfigurationen för enkel inloggning. 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. På den **xMatters OnDemand Configuration** klickar du på **konfigurera xMatters OnDemand** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen XMatters OnDemand företag som administratör.

1. I verktygsfältet högst upp, klickar du på **Admin**, och klicka sedan på **Company Details** i navigeringsfältet till vänster.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

1. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![SAML-konfiguration](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-konfiguration")

    a. Välj **Aktivera SAML**.

    b. I den **identitet Provider-ID** textrutan klistra in **SAML entitets-ID** värde som du har kopierat från Azure-portalen.

    c. I den **inloggning på URL för enkel** textrutan klistra in **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure-portalen.

    d. I den **URL för enkel utloggning** textrutan klistra in **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    e. På sidan Company Details överst, **spara ändringar**.

    ![Företagets information](./media/xmatters-ondemand-tutorial/IC776797.png "företagets information")

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.

    ![Skapa en Azure AD-användare för testning](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en Azure AD-användare för testning](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en Azure AD-användare för testning](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Skapa en xMatters OnDemand testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i xMatters OnDemand.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **XMatters OnDemand** klient.

1. Klicka på **användare** fliken och klicka sedan på **Lägg till användare**.

   ![Användare](./media/xmatters-ondemand-tutorial/IC781048.png "Användare")

1. I den **lägga till en användare** avsnittet, utför följande steg:

    ![Lägga till en användare](./media/xmatters-ondemand-tutorial/IC781049.png "lägga till en användare")

    a. Välj **Active** (Aktiv).

    b. I den **användar-ID** textrutan typ användar-id för användaren som Brittasimon@contoso.com.

    c. I textrutan för **förnamn** skriver du förnamnet på användaren, till exempel Britta.

    d. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    e. I den **plats** textrutan, ange den giltiga platsen i en giltig Azure AD-konto som du vill etablera.

    f. Klicka på **Spara**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till xMatters OnDemand.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon xMatters OnDemand, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **xMatters OnDemand**.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på xMatters OnDemand-panelen i åtkomstpanelen du bör få automatiskt loggat in på ditt xMatters OnDemand-programmet.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
