---
title: 'Självstudier: Azure Active Directory-integrering med d & E Express | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 334f5fa2309c44bebe8583f497fdaa3c7578e5ae
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433424"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Självstudier: Azure Active Directory-integrering med d & E Express

I den här självstudien får du lära dig hur du integrerar d & E Express med Azure Active Directory (AD Azure).

Integrera d & E Express med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till T & E Express
- Du kan aktivera användarna att automatiskt få loggat in på d & E Express (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med d & E Express, behöver du följande objekt:

- En Azure AD-prenumeration
- En d & E Express enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till T & E Express från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-te-express-from-the-gallery"></a>Att lägga till T & E Express från galleriet
För att konfigurera integrering av d & E Express i Azure AD, som du behöver lägga till T & E Express från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till T & E Express från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **d & E Express**.

    ![Skapa en Azure AD-användare för testning](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

1. I resultatpanelen väljer **d & E Express**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med d & E Express baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i T & E Express är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i T & E Express upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i T & E Express.

Om du vill konfigurera och testa Azure AD enkel inloggning med d & E Express, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare d & E Express](#creating-a-te-express-test-user)**  – du har en motsvarighet för Britta Simon i T & E Express som är länkad till en Azure AD-representation av henne.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt T & E Express-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med d & E Express:**

1. I hanteringsportalen för Azure på den **d & E Express** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

1. På den **& E Express domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. I den **identifierare** textrutan Ange värdet som: `https://<domain>.tyeexpress.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera dessa värden med de faktiska identifierare och svars-URL. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Kontakta [d & E Express supportteam](http://www.tyeexpress.com/contacto.aspx) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **d & E uttryckliga** sida, inloggning till T & E express program utan SAML enkel inloggning om hur du använder autentiseringsuppgifter som administratör.

1. Under den **Admin** fliken, klickar du på **SAML domän** att öppna inställningssidan för SAML.

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tye-SAML.png)

1. Välj den **Activar(Activate)** alternativet från **nr** till **SI(Yes)**. I den **identitet providern Metadata** textrutan klistra in metadata XML som du har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Klicka på den **Guardar(Save)** för att spara inställningarna.  


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/tyeexpress-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/tyeexpress-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/tyeexpress-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-te-express-test-user"></a>Skapa en testanvändare d & E Express

För att aktivera Azure AD-användare att logga in på d & E Express, måste de etableras i T & E Express.  
När det gäller T & E Express är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen d & E Express företagets som administratör.

1. Under Admin-tagg, klickar du på användare att öppna sidan som användare.

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-adminusers.png)

1. På startsidan klickar du på **+** att lägga till användarna.

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-usershome.png)

1. Ange all obligatorisk information som och svar i formuläret och klicka på knappen Spara för att spara information.

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Lägga till medarbetare](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst d & E Express.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon D & E Express, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **d & E Express**.

    ![Konfigurera enkel inloggning](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen d & E Express i åtkomstpanelen du bör få automatiskt loggat in på ditt T & E Express-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

