---
title: 'Självstudier: Azure Active Directory-integration med Samanage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: c90c0c2f831a622b54a56db5c9a3d4efb2f57eaa
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041865"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Självstudier: Azure Active Directory-integration med Samanage

I den här självstudien får du lära dig hur du integrerar Samanage med Azure Active Directory (AD Azure).

Integrera Samanage med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Samanage
- Du kan aktivera användarna att automatiskt få loggat in på Samanage (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Samanage, behöver du följande objekt:

- En Azure AD-prenumeration
- En Samanage enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Samanage från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-samanage-from-the-gallery"></a>Att lägga till Samanage från galleriet
För att konfigurera integrering av Samanage i Azure AD, som du behöver lägga till Samanage från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Samanage från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Samanage**.

    ![Skapa en Azure AD-användare för testning](./media/samanage-tutorial/tutorial_samanage_search.png)

5. I resultatpanelen väljer **Samanage**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Samanage baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Samanage är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Samanage upprättas.

I Samanage, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Samanage, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Samanage](#creating-a-samanage-test-user)**  – du har en motsvarighet för Britta Simon i Samanage som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Samanage program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Samanage:**

1. I Azure-portalen på den **Samanage** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/samanage-tutorial/tutorial_samanage_samlbase.png)

3. På den **Samanage domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/samanage-tutorial/tutorial_samanage_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare som beskrivs senare i självstudien. Mer information kontaktar [Samanage klienten supportteamet](https://www.samanage.com/support).    
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/samanage-tutorial/tutorial_samanage_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/samanage-tutorial/tutorial_general_400.png)

6. På den **Samanage Configuration** klickar du på **konfigurera Samanage** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/samanage-tutorial/tutorial_samanage_configure.png) 

7. Logga in på webbplatsen Samanage företag som en administratör i ett annat webbläsarfönster.

8. Klicka på **instrumentpanelen** och välj **installationsprogrammet** i vänstra navigeringsfönstret.
   
    ![Instrumentpanelen](./media/samanage-tutorial/tutorial_samanage_001.png "instrumentpanel")

9. Klicka på **enkel inloggning**.
   
    ![Enkel inloggning](./media/samanage-tutorial/tutorial_samanage_002.png "enkel inloggning")

10. Gå till **logga in med SAML** avsnittet, utför följande steg:
   
    ![Logga in med SAML](./media/samanage-tutorial/tutorial_samanage_003.png "logga in med SAML")
 
    a. Klicka på **aktivera enkel inloggning med SAML**.  
 
    b. I den **-URL för identitetsprovider** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.    
 
    c. Bekräfta de **inloggnings-URL** matchar den **inloggning på URL: en** av **Samanage domän och URL: er** avsnitt i Azure-portalen.
 
    d. I den **URL för utloggning** textrutan anger du värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
 
    e. I den **SAML utfärdare** textrutan, skriver du in URI för app-id som angetts i din identitetsprovider.
 
    f. Öppna din Base64-kodat certifikat som hämtats från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **klistra in din identitetsprovider x.509 Certificate nedan** textrutan.
 
    g. Klicka på **skapa användare om de inte finns i Samanage**.
 
    h. Klicka på **uppdatering**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/samanage-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/samanage-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/samanage-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/samanage-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-samanage-test-user"></a>Skapa en Samanage testanvändare

Om du vill aktivera Azure AD-användare att logga in på Samanage, måste de etableras i Samanage.  
När det gäller Samanage är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen Samanage företag som administratör.

2. Klicka på **instrumentpanelen** och välj **installationsprogrammet** i vänstra navigeringsrutan.
   
    ![Installationsprogrammet](./media/samanage-tutorial/tutorial_samanage_001.png "installationen")

3. Klicka på den **användare** fliken
   
    ![Användare](./media/samanage-tutorial/tutorial_samanage_006.png "användare")

4. Klicka på **ny användare**.
   
    ![Ny användare](./media/samanage-tutorial/tutorial_samanage_007.png "ny användare")

5. Skriv den **namn** och **e-postadress** för ett Azure Active Directory-konto som du vill etablera och klicka på **skapa användare**.
   
    ![Skapa användare](./media/samanage-tutorial/tutorial_samanage_008.png "skapa användare")
   
   >[!NOTE]
   >Azure Active Directory-kontoinnehavare kommer ett e-postmeddelande och följ en länk för att bekräfta sina konton innan den blir aktiv. Du kan använda alla andra Samanage användare konto verktyg för att skapa eller API: er som tillhandahålls av Samanage för att etablera Azure Active Directory användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Samanage.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Samanage, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Samanage**.

    ![Konfigurera enkel inloggning](./media/samanage-tutorial/tutorial_samanage_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Samanage i åtkomstpanelen du bör få automatiskt loggat in på ditt Samanage program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samanage-tutorial/tutorial_general_01.png
[2]: ./media/samanage-tutorial/tutorial_general_02.png
[3]: ./media/samanage-tutorial/tutorial_general_03.png
[4]: ./media/samanage-tutorial/tutorial_general_04.png

[100]: ./media/samanage-tutorial/tutorial_general_100.png

[200]: ./media/samanage-tutorial/tutorial_general_200.png
[201]: ./media/samanage-tutorial/tutorial_general_201.png
[202]: ./media/samanage-tutorial/tutorial_general_202.png
[203]: ./media/samanage-tutorial/tutorial_general_203.png

