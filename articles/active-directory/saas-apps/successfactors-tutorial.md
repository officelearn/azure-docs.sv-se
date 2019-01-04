---
title: 'Självstudier: Azure Active Directory-integrering med SuccessFactors | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 467db4046c0600142338dcfa39e136f45255caba
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976757"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Självstudier: Azure Active Directory-integrering med SuccessFactors

I den här självstudien får du lära dig hur du integrerar SuccessFactors med Azure Active Directory (AD Azure).

Integrera SuccessFactors med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SuccessFactors.
- Du kan aktivera användarna att automatiskt få loggat in på SuccessFactors (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SuccessFactors, behöver du följande objekt:

- En Azure AD-prenumeration
- En SuccessFactors enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SuccessFactors från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-successfactors-from-the-gallery"></a>Att lägga till SuccessFactors från galleriet

För att konfigurera integrering av SuccessFactors i Azure AD, som du behöver lägga till SuccessFactors från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SuccessFactors från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SuccessFactors**väljer **SuccessFactors** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SuccessFactors i resultatlistan](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SuccessFactors baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SuccessFactors är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SuccessFactors upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SuccessFactors, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SuccessFactors](#creating-a-successfactors-test-user)**  – du har en motsvarighet för Britta Simon i SuccessFactors som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SuccessFactors program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SuccessFactors:**

1. I Azure-portalen på den **SuccessFactors** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SuccessFactors domän och URL: er med enkel inloggning för information](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och svars-URL. Kontakta [SuccessFactors klienten supportteamet](https://www.successfactors.com/content/ssf-site/en/support.html) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. På den **konfigurera SuccessFactors** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggnings-URL

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![SuccessFactors konfiguration](common/configuresection.png)

7. Logga in på i ett annat webbläsarfönster din **SuccessFactors administrationsportalen** som administratör.
    
8. Besök **programsäkerhet** och **enkel inloggning på funktionen**. 

9. Placera något värde i den **återställa Token** och klicka på **spara Token** att aktivera SAML SSO.
   
    ![Konfigurera enkel inloggning på app-sida][11]

    > [!NOTE] 
    > Det här värdet används som på/av-omkopplare. Om ett värde sparas, har SAML SSO värdet ON. Om ett tomt värde sparas har SAML SSO värdet OFF.

10. Inbyggt i skärmbilden nedan och utför följande åtgärder:
   
    ![Konfigurera enkel inloggning på app-sida][12]
   
    a. Välj den **SAML v2 SSO** alternativknapp
   
    b. Ange den **SAML bevisar partens namn**(till exempel SAML utfärdare + företagsnamn).
   
    c. I den **utfärdar-URL** textrutan klistra in den **Azure AD-identifierare** värde som du har kopierat från Azure-portalen.
   
    d. Välj **Assertion** som **kräver obligatorisk signatur**.
   
    e. Välj **aktiverat** som **aktivera SAML-flaggan**.
   
    f. Välj **nr** som **inloggning begära signatur (SF genereras/SP/RP)**.
   
    g. Välj **webbläsaren/Post-profilen** som **SAML-profilen**.
   
    h. Välj **nr** som **framtvinga giltighetsperioden för certifikatet**.
   
    i. Kopiera innehållet i filen nedladdade certifikatet från Azure-portalen och klistra in den i den **verifiera SAML-certifikatet** textrutan.

    > [!NOTE] 
    > Certifikatinnehåll måste ha starttaggar certifikat- och slutdatum certifikat.

11. Gå till SAML-V2 och utför sedan följande steg:
   
    ![Konfigurera enkel inloggning på app-sida][13]
   
    a. Välj **Ja** som **stöder SP-initierat globala utloggning**.
   
    b. I den **globala Utloggning (LogoutRequest mål)** textrutan klistra in den **URL: en för utloggning** värde som du har kopierat utgör Azure-portalen.
   
    c. Välj **nr** som **kräver sp måste kryptera alla NameID-element**.
   
    d. Välj **Ospecificerad** som **NameID-Format**.
   
    e. Välj **Ja** som **aktivera sp initierade inloggning (AuthnRequest)**.
   
    f. I den **sändningsbegäranden som utfärdare för hela företaget** textrutan klistra in **SAML enkel inloggning för tjänst-URL** värde som du har kopierat från Azure-portalen.

12. Utför de här stegen om du vill se användarnamnen inloggningen inte skiftlägeskänsligt.
   
    ![Konfigurera enkel inloggning][29]
    
    a. Besök **Företagsinställningar**(nästan längst ned).
   
    b. Markera kryssrutan nära **aktivera icke skiftlägeskänslig användarnamn**.
   
    c.Click **spara**.
   
    > [!NOTE] 
    > Om du försöker aktivera det här alternativet kontrollerar systemet om skapas ett dublettnamn i SAML-inloggning. Till exempel om kunden har användarnamn Användare1 och user1. Tar bort skiftlägeskänslighet gör dessa dubbletter. Systemet får du ett felmeddelande och aktivera inte funktionen. Kunden måste ändra något av användarnamnen så att de stavas olika.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-successfactors-test-user"></a>Skapa en SuccessFactors testanvändare

Om du vill aktivera Azure AD-användare att logga in på SuccessFactors, måste de etableras i SuccessFactors.  
När det gäller SuccessFactors är etablering en manuell aktivitet.

För att få användare som skapats i SuccessFactors kan du behöva kontakta den [SuccessFactors supportteam](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SuccessFactors.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **SuccessFactors**.

    ![Konfigurera enkel inloggning](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SuccessFactors i åtkomstpanelen du bör få automatiskt loggat in på ditt SuccessFactors program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
