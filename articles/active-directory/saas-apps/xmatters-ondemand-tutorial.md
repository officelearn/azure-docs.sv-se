---
title: 'Självstudier: Azure Active Directory-integrering med xMatters OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 9a3555f9efa34a5da2e6fa624da0f80d52dab077
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211643"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Självstudier: Azure Active Directory-integrering med xMatters OnDemand

I kursen får lära du att integrera xMatters OnDemand med Azure Active Directory (AD Azure).

Integrera xMatters OnDemand med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till xMatters OnDemand
- Du kan aktivera användarna att automatiskt hämta loggat in på xMatters OnDemand (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med xMatters OnDemand, behöver du följande:

- En Azure AD-prenumeration
- En xMatters OnDemand enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till xMatters OnDemand från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Att lägga till xMatters OnDemand från galleriet
Du måste lägga till xMatters OnDemand från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av xMatters OnDemand i Azure AD.

**Utför följande steg för att lägga till xMatters OnDemand från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **xMatters OnDemand**.

    ![Skapa en testanvändare i Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

5. Välj i resultatpanelen **xMatters OnDemand**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med xMatters OnDemand baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i xMatters OnDemand är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i xMatters OnDemand måste upprättas.

I xMatters OnDemand, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med xMatters OnDemand, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för xMatters OnDemand](#creating-a-xmatters-ondemand-test-user)**  – du har en motsvarighet för Britta Simon i xMatters OnDemand som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din xMatters OnDemand-programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med xMatters OnDemand:**

1. I Azure-portalen på den **xMatters OnDemand** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

3. På den **xMatters OnDemand domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. I den **identifierare** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [xMatters OnDemand supportteam](https://www.xmatters.com/company/contact-us/) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen lokalt som **c:\\XMatters OnDemand.cer**.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Du behöver att vidarebefordra certifikatet som den [xMatters OnDemand supportteam](https://www.xmatters.com/company/contact-us/). Certifikatet måste överföras av supportteamet xMatters innan du kan slutföra konfigurationen för enkel inloggning. 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

6. På den **xMatters OnDemand Configuration** klickar du på **konfigurera xMatters OnDemand** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen XMatters OnDemand företag som administratör.

8. Klicka på i verktygsfältet högst upp **Admin**, och klicka sedan på **företagsinformation** i navigeringsfältet till vänster.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

9. På den **SAML-konfiguration** utför följande steg:

    ![SAML-konfiguration](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-konfiguration")

    a. Välj **aktivera SAML**.

    b. I den **identitet Provider-ID** textruta klistra in **SAML enhets-ID** värde som du har kopierat från Azure-portalen.

    c. I den **enkel inloggning på URL: en** textruta klistra in **SAML inloggning tjänst-URL för enkel** värde som du har kopierat från Azure-portalen.

    d. I den **URL för enkel logga ut** textruta klistra in **Sign-Out URL**, som du har kopierat från Azure-portalen.

    e. På sidan företagsinformation överst **spara ändringar**.

    ![Företagets information](./media/xmatters-ondemand-tutorial/IC776797.png "företagets information")

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.

    ![Skapa en testanvändare i Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.

    ![Skapa en testanvändare i Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Skapa en testanvändare i Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Skapa en xMatters OnDemand testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i xMatters OnDemand. xMatters OnDemand stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](xmatters-ondemand-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt gör du följande:**

1. Logga in på ditt **XMatters OnDemand** klient.

2.  Klicka på **användare** fliken och klicka sedan på **Lägg till användare**.

    ![Användare](./media/xmatters-ondemand-tutorial/IC781048.png "användare")

3. I den **lägga till en användare** avsnittet, utför följande steg:

    ![Lägga till en användare](./media/xmatters-ondemand-tutorial/IC781049.png "lägga till en användare")

    a. Välj **Active**.

    b. I den **användar-ID** textruta ange användar-id för användare som Brittasimon@contoso.com.

    c. I den **Förnamn** textruta första typnamnet för användaren som Britta.

    d. I den **efternamn** textruta Skriv Efternamn för användaren som Simon.

    e. I den **plats** textruta RETUR giltig plats för ett giltigt Azure AD-kontot som du vill etablera.

    f. Klicka på **Spara**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till xMatters OnDemand.

![Tilldela användare][200] 

**Om du vill tilldela xMatters OnDemand Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **xMatters OnDemand**.

    ![Konfigurera enkel inloggning](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på xMatters OnDemand-panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt xMatters OnDemand-programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](xmatters-ondemand-provisioning-tutorial.md)

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
