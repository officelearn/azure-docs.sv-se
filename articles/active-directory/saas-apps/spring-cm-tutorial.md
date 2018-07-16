---
title: 'Självstudier: Azure Active Directory-integration med SpringCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.openlocfilehash: cf0495e27d7fc06c54266eba6640e0c0fb99be42
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051209"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Självstudier: Azure Active Directory-integration med SpringCM

I den här självstudien får du lära dig hur du integrerar SpringCM med Azure Active Directory (AD Azure).

Integrera SpringCM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SpringCM
- Du kan aktivera användarna att automatiskt få loggat in på SpringCM (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SpringCM, behöver du följande objekt:

- En Azure AD-prenumeration
- En SpringCM enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SpringCM från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-springcm-from-the-gallery"></a>Att lägga till SpringCM från galleriet
För att konfigurera integrering av SpringCM i Azure AD, som du behöver lägga till SpringCM från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SpringCM från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **SpringCM**.

    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/tutorial_springcm_search.png)

5. I resultatpanelen väljer **SpringCM**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SpringCM baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SpringCM är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i SpringCM upprättas.

I SpringCM, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SpringCM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SpringCM](#creating-a-springcm-test-user)**  – du har en motsvarighet för Britta Simon i SpringCM som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SpringCM program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SpringCM:**

1. I Azure-portalen på den **SpringCM** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_samlbase.png)

3. På den **SpringCM domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [SpringCM klienten supportteamet](https://knowledge.springcm.com/support) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Raw)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_general_400.png)

6. På den **SpringCM Configuration** klickar du på **konfigurera SpringCM** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_configure.png)     

7. I ett annat webbläsarfönster, loggar du in på ditt **SpringCM** företagets plats som administratör.

8. Klicka på menyn längst upp **gå till**, klickar du på **inställningar**, och klicka sedan på **kontoinställningar** klickar du på **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

9. Utför följande steg i konfigurationsavsnittet Identity-providern:
   
    ![Identitet providerkonfigurationen](./media/spring-cm-tutorial/ic797052.png "identitet Providerkonfiguration")
    
    a. Om du vill ladda upp din hämtade Azure Active Directory-certifikatet klickar du på **Välj utfärdarcertifikatet** eller **ändra utfärdarcertifikatet**.
    
    b. Klistra in **SAML entitets-ID** värde, som du har kopierat från Azure-portalen till den **utfärdare** textrutan.
    
    c. Klistra in **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen till den **slutpunkten för Service Provider (SP) initieras** textrutan.
            
    d. Välj **SAML aktiverat** som **aktivera**.

    e. Klicka på **Spara**.
 
> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/spring-cm-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-springcm-test-user"></a>Skapa en SpringCM testanvändare

Om du vill aktivera Azure Active Directory-användare att logga in på SpringCM, måste de etableras i SpringCM. När det gäller SpringCM är etablering en manuell aktivitet.

>[!NOTE]
>Mer information finns i [skapar och redigerar en SpringCM användare](http://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Om du vill konfigurera ett användarkonto till SpringCM, utför du följande steg:**

1. Logga in på din **SpringCM** företagets plats som administratör.

2. Klicka på **GOTO**, och klicka sedan på **ADRESSBOKEN**.
   
    ![Skapa användare](./media/spring-cm-tutorial/ic797054.png "skapa användare")

3. Klicka på **skapa användare**.

4. Välj en **användarrollen**.

5. Välj **skickar Aktiveringsmeddelandet**.

6. Ange den förnamn, efternamn och e-postadress för ett giltigt Azure Active Directory-användarkonto som du vill etablera till relaterade textrutor.

7. Lägga till användare i en **säkerhetsgrupp**.

8. Klicka på **Spara**.

  >[!NOTE]
  >Du kan använda alla andra SpringCM användare konto verktyg för att skapa eller API: er som tillhandahålls av SpringCM att etablera AAD-användarkonton.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SpringCM.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon SpringCM, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SpringCM**.

    ![Konfigurera enkel inloggning](./media/spring-cm-tutorial/tutorial_springcm_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.
 
När du klickar på panelen SpringCM i åtkomstpanelen du bör få automatiskt loggat in på ditt SpringCM program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/spring-cm-tutorial/tutorial_general_203.png

