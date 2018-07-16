---
title: 'Självstudier: Azure Active Directory-integration med Panorama9 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 7285ef4bea1dccb651360520bfe8bc4289d54f8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046047"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Självstudier: Azure Active Directory-integration med Panorama9

I den här självstudien får du lära dig hur du integrerar Panorama9 med Azure Active Directory (AD Azure).

Integrera Panorama9 med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Panorama9
- Du kan aktivera användarna att automatiskt få loggat in på Panorama9 (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Panorama9, behöver du följande objekt:

- En Azure AD-prenumeration
- En Panorama9 enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Panorama9 från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-panorama9-from-the-gallery"></a>Att lägga till Panorama9 från galleriet
För att konfigurera integrering av Panorama9 i Azure AD, som du behöver lägga till Panorama9 från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Panorama9 från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Panorama9**.

    ![Skapa en Azure AD-användare för testning](./media/panorama9-tutorial/tutorial_panorama9_search.png)

5. I resultatpanelen väljer **Panorama9**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Panorama9 baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Panorama9 är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Panorama9 upprättas.

I Panorama9, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Panorama9, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Panorama9](#creating-a-panorama9-test-user)**  – du har en motsvarighet för Britta Simon i Panorama9 som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Panorama9 program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Panorama9:**

1. I Azure-portalen på den **Panorama9** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

3. På den **Panorama9 domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://dashboard.panorama9.com/saml/access/3262`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Panorama9 klienten supportteamet](https://support.panorama9.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/panorama9-tutorial/tutorial_general_400.png)

6. På den **Panorama9 Configuration** klickar du på **konfigurera Panorama9** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

5. Logga in på webbplatsen Panorama9 företag som en administratör i ett annat webbläsarfönster.

6. I verktygsfältet högst upp, klickar du på **hantera**, och klicka sedan på **tillägg**.
   
   ![Tillägg](./media/panorama9-tutorial/ic790023.png "tillägg")
7. På den **tillägg** dialogrutan klickar du på **enkel inloggning**.
   
   ![Enkel inloggning](./media/panorama9-tutorial/ic790024.png "enkel inloggning")
8. I den **inställningar** avsnittet, utför följande steg:
   
   ![Inställningar för](./media/panorama9-tutorial/ic790025.png "inställningar")
   
    a. I **-URL för identitetsprovider** textrutan klistra in värdet för **enkel inloggnings-URL för**, som du har kopierat från Azure-portalen.
   
    b. I **certifikat fingeravtryck** textrutan klistra in den **tumavtryck** värdet för certifikat som du har kopierat från Azure-portalen.    
         
9. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/panorama9-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/panorama9-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/panorama9-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-panorama9-test-user"></a>Skapa en Panorama9 testanvändare

För att aktivera Azure AD-användare att logga in på Panorama9, måste de etableras i Panorama9.  

När det gäller Panorama9 är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din **Panorama9** företagets plats som administratör.

2. Klicka på menyn längst upp **hantera**, och klicka sedan på **användare**.
   
  ![Användare](./media/panorama9-tutorial/ic790027.png "användare")

3. I avsnittet användare klickar du på **+** att lägga till nya användare.

 ![Användare](./media/panorama9-tutorial/ic790028.png "användare")

4. Gå till avsnittet användaren data, ange en giltig Azure Active Directory-användare som du vill etablera i e-postadress i **e-post** textrutan.

5. Gå till avsnittet för användare, klicka på **spara**.
   
> [!NOTE]
    > Azure Active Directory-kontoinnehavare tar emot ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den blir aktiv.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Panorama9.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Panorama9, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Panorama9**.

    ![Konfigurera enkel inloggning](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Panorama9 i åtkomstpanelen du bör få automatiskt loggat in på Panorama9 program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

