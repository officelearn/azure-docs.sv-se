---
title: 'Självstudier: Azure Active Directory-integrering med Five9 Plus-kort (CTI, kontakta Center-agenter) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Five9 Plus-kort (CTI, kontakta Center-agenter).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: d5c6b2c658a899b3c4363803dc3858cc2b6bab46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Självstudier: Azure Active Directory-integrering med Five9 Plus-kort (CTI, kontakta Center-agenter)

I kursen får du lära dig hur du integrerar Five9 Plus nätverkskort (CTI, kontakta Center-agenter) med Azure Active Directory (AD Azure).

Integrera Five9 Plus nätverkskort (CTI, kontakta Center-agenter) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Five9 Plus nätverkskort (CTI, kontakta Center-agenter)
- Du kan aktivera användarna att automatiskt hämta loggat in på Five9 Plus nätverkskort (CTI, kontakta Center-agenter) (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Five9 Plus-kort (CTI, kontakta Center-agenter) behöver du följande:

- En Azure AD-prenumeration
- En Five9 Plus nätverkskort (CTI, kontakta Center-agenter) enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Five9 Plus kort (CTI, kontakta Center-agenter) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Lägga till Five9 Plus kort (CTI, kontakta Center-agenter) från galleriet
För att konfigurera integrering av Five9 Plus nätverkskort (CTI, kontakta Center-agenter) till Azure AD, måste du lägga till Five9 Plus nätverkskort (CTI, kontakta Center-agenter) från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg om du vill lägga till Five9 Plus kort (CTI, kontakta Center-agenter) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Five9 Plus nätverkskort (CTI, kontakta Center-agenter)**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-five9-tutorial/tutorial_five9_search.png)

5. Välj i resultatpanelen **Five9 Plus nätverkskort (CTI, kontakta Center-agenter)**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Five9 Plus-kort (CTI, kontakta Center-agenter) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Five9 Plus nätverkskort (CTI, kontakta Center-agenter) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Five9 Plus nätverkskort (CTI, kontakta Center-agenter) upprättas.

I Five9 Plus-kort (CTI, kontakta Center-agenter), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Five9 Plus-kort (CTI, kontakta Center-agenter), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Five9 Plus nätverkskort (CTI, kontakta Center-agenter)](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  – du har en motsvarighet för Britta Simon i Five9 Plus nätverkskort (CTI, kontakta Center-agenter) som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Five9 Plus nätverkskort (CTI, kontakta Center-agenter).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Five9 Plus-kort (CTI, kontakta Center-agenter):**

1. I Azure-portalen på den **Five9 Plus nätverkskort (CTI, kontakta Center-agenter)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-five9-tutorial/tutorial_five9_samlbase.png)

3. På den **Five9 Plus nätverkskort (CTI, kontakta Center-agenter)-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-five9-tutorial/tutorial_five9_url.png)
    
    a. I den **identifierare** textruta Skriv en URL med följande mönster:

    |    Miljö      |       URL      |
    | :-- | :-- |
    | För ”Five9 Plus Adapter för Microsoft Dynamics CRM” | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | För ”Five9 Plus kortet för Zendesk” | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | För ”Five9 Plus kortet för agenten skrivbord Toolkit” | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:

    |      Miljö     |      URL      |
    | :--                  | :--           |
    | För ”Five9 Plus Adapter för Microsoft Dynamics CRM” | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | För ”Five9 Plus kortet för Zendesk” | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | För ”Five9 Plus kortet för agenten skrivbord Toolkit” | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-five9-tutorial/tutorial_five9_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-five9-tutorial/tutorial_general_400.png)

6. På den **Five9 Plus (CTI, kontakta Center-agenter) kortkonfiguration** klickar du på **konfigurera Five9 Plus nätverkskort (CTI, kontakta Center-agenter)** att öppna **konfigurera inloggning**fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-five9-tutorial/tutorial_five9_configure.png) 

7. Konfigurera enkel inloggning på **Five9 Plus nätverkskort (CTI, kontakta Center-agenter)** sida, måste du skicka den hämtade **Certificate(Base64), Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel**till [Five9 Plus nätverkskort (CTI, kontakta Center-agenter) supportteamet](https://www.five9.com/about/contact). Dessutom, för att konfigurera enkel inloggning ytterligare följer du de nedanstående steg enligt nätverkskortet:

    a. ”Five9 Plus kortet för agenten skrivbord Toolkit” Admin-Guide: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. ”Five9 Plus Adapter för Microsoft Dynamics CRM” Admin-Guide: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. ”Five9 Plus kortet för Zendesk” Admin-Guide: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-five9-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Skapa en testanvändare Five9 Plus nätverkskort (CTI, kontakta Center-agenter)

I det här avsnittet skapar du en användare som kallas Britta Simon i Five9 Plus nätverkskort (CTI, kontakta Center-agenter). Arbeta med [Five9 Plus nätverkskort (CTI, kontakta Center-agenter) supportteamet](https://www.five9.com/about/contact) att lägga till användare i Five9 Plus nätverkskort (CTI, kontakta Center-agenter)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Five9 Plus nätverkskort (CTI, kontakta Center-agenter).

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Five9 Plus nätverkskortet (CTI, kontakta Center-agenter), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Five9 Plus nätverkskort (CTI, kontakta Center-agenter)**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-five9-tutorial/tutorial_five9_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Five9 Plus nätverkskort (CTI, kontakta Center-agenter) på åtkomstpanelen du ska hämta automatiskt loggat in på ditt program Five9 Plus nätverkskort (CTI, kontakta Center-agenter).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-five9-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-five9-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-five9-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-five9-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-five9-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-five9-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-five9-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-five9-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-five9-tutorial/tutorial_general_203.png

