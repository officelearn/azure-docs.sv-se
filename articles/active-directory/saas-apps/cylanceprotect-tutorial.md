---
title: 'Självstudier: Azure Active Directory-integrering med CylancePROTECT | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CylancePROTECT.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea392d8c-c8aa-4475-99d0-b08524ef0f3a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jeedes
ms.openlocfilehash: 9eb64777c6ddfbedd1716fa17946499c2556dcc8
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968016"
---
# <a name="tutorial-azure-active-directory-integration-with-cylanceprotect"></a>Självstudier: Azure Active Directory-integrering med CylancePROTECT

I kursen får lära du att integrera CylancePROTECT med Azure Active Directory (AD Azure).

Integrera CylancePROTECT med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till CylancePROTECT.
- Du kan aktivera användarna att automatiskt hämta loggat in på CylancePROTECT (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med CylancePROTECT, behöver du följande:

- En Azure AD-prenumeration
- En CylancePROTECT enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till CylancePROTECT från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cylanceprotect-from-the-gallery"></a>Att lägga till CylancePROTECT från galleriet
Du måste lägga till CylancePROTECT från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av CylancePROTECT i Azure AD.

**Utför följande steg för att lägga till CylancePROTECT från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **CylancePROTECT**väljer **CylancePROTECT** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![CylancePROTECT i resultatlistan](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med CylancePROTECT baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i CylancePROTECT motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i CylancePROTECT upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med CylancePROTECT, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare CylancePROTECT](#create-a-cylanceprotect-test-user)**  – du har en motsvarighet för Britta Simon i CylancePROTECT som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt CylancePROTECT program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med CylancePROTECT:**

1. I Azure-portalen på den **CylancePROTECT** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_samlbase.png)

3. På den **CylancePROTECT domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och CylancePROTECT domän med enkel inloggning information](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_url.png)

    a. I den **identifierare** textruta anger du URL:
    
    | Region | URL-värdet |
    |----------|---------|
    | Asien nordöst (APNE1)| ` https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asien/Stillahavsområdet, sydost (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Nordamerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sydamerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    
    b. I den **Reply URL** textruta anger du URL:
    
    | Region | URL-värdet |
    |----------|---------|
    | Asien nordöst (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asien/Stillahavsområdet, sydost (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Nordamerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sydamerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara sedan filen certifikat på datorn.

    ![Länken hämta certifikatet](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/cylanceprotect-tutorial/tutorial_general_400.png)

6. På den **CylancePROTECT Configuration** klickar du på **konfigurera CylancePROTECT** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![CylancePROTECT konfiguration](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_configure.png) 

7. Konfigurera enkel inloggning på **CylancePROTECT** sida, måste du skicka den hämtade **Certificate(Base64), Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** till konsolen administratören. De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/cylanceprotect-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/cylanceprotect-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/cylanceprotect-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/cylanceprotect-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-cylanceprotect-test-user"></a>Skapa en testanvändare CylancePROTECT

I det här avsnittet skapar du en användare som kallas Britta Simon i CylancePROTECT. Arbeta med konsolen administratören lägga till användare i CylancePROTECT-plattformen. Innehavaren för Azure Active Directory-konto ett e-postmeddelande och länken för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till CylancePROTECT.

![Tilldela rollen][200] 

**Om du vill tilldela CylancePROTECT Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **CylancePROTECT**.

    ![Länken CylancePROTECT i listan med program](./media/cylanceprotect-tutorial/tutorial_cylanceprotect_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen CylancePROTECT på åtkomstpanelen du bör få automatiskt loggat in på ditt CylancePROTECT program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cylanceprotect-tutorial/tutorial_general_01.png
[2]: ./media/cylanceprotect-tutorial/tutorial_general_02.png
[3]: ./media/cylanceprotect-tutorial/tutorial_general_03.png
[4]: ./media/cylanceprotect-tutorial/tutorial_general_04.png

[100]: ./media/cylanceprotect-tutorial/tutorial_general_100.png

[200]: ./media/cylanceprotect-tutorial/tutorial_general_200.png
[201]: ./media/cylanceprotect-tutorial/tutorial_general_201.png
[202]: ./media/cylanceprotect-tutorial/tutorial_general_202.png
[203]: ./media/cylanceprotect-tutorial/tutorial_general_203.png

