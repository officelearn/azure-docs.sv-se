---
title: 'Självstudier: Azure Active Directory-integration med ITRP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 0af96b750c7e316d1d394a00781f727358f2c4e8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428323"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Självstudier: Azure Active Directory-integration med ITRP

I den här självstudien får du lära dig hur du integrerar ITRP med Azure Active Directory (AD Azure).

Integrera ITRP med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ITRP
- Du kan aktivera användarna att automatiskt få loggat in på ITRP (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ITRP, behöver du följande objekt:

- En Azure AD-prenumeration
- En ITRP enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ITRP från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-itrp-from-the-gallery"></a>Att lägga till ITRP från galleriet
Om du vill konfigurera integreringen av ITRP i till Azure AD, som du behöver lägga till ITRP från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ITRP från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **ITRP**.

    ![Skapa en Azure AD-användare för testning](./media/itrp-tutorial/tutorial_itrp_search.png)

1. I resultatpanelen väljer **ITRP**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/itrp-tutorial/tutorial_itrp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ITRP baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i ITRP är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ITRP upprättas.

I ITRP, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med ITRP, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en ITRP testanvändare](#creating-an-itrp-test-user)**  – du har en motsvarighet för Britta Simon i ITRP som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ITRP program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ITRP:**

1. I Azure-portalen på den **ITRP** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/itrp-tutorial/tutorial_itrp_samlbase.png)

1. På den **ITRP domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/itrp-tutorial/tutorial_itrp_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.itrp.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.itrp.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [ITRP klienten supportteamet](https://www.itrp.com/support) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/itrp-tutorial/tutorial_itrp_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/itrp-tutorial/tutorial_general_400.png)

1. På den **ITRP Configuration** klickar du på **konfigurera ITRP** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggnings-URL: en och URL: en för utloggning** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/itrp-tutorial/tutorial_itrp_configure.png) 

1. I ett annat webbläsarfönster logga du in på webbplatsen ITRP företag som administratör.

1. I verktygsfältet högst upp, klickar du på **inställningar**.
   
    ![ITRP](./media/itrp-tutorial/ic775570.png "ITRP")

1. I det vänstra navigeringsfönstret väljer **enkel inloggning**.
   
    ![Enkel inloggning](./media/itrp-tutorial/ic775571.png "enkel inloggning")

1. Utför följande steg i konfigurationsavsnittet enkel inloggning:
   
    ![Enkel inloggning](./media/itrp-tutorial/ic775572.png "enkel inloggning")
    
    ![Enkel inloggning](./media/itrp-tutorial/ic775573.png "enkel inloggning")   

    a. Klicka på **Aktivera**.

    b. I **Remote logga ut URL** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    c. I **URL för SAML SSO** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    d.In **certifikat fingeravtryck** textrutan klistra in den **tumavtryck** värdet för certifikat som du har kopierat från Azure-portalen. 
      
1. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/itrp-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/itrp-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/itrp-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/itrp-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-itrp-test-user"></a>Skapa en ITRP testanvändare

Om du vill aktivera Azure AD-användare att logga in på ITRP, måste de vara etablerade i att ITRP.  

När det gäller ITRP är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **ITRP** klient.

1. I verktygsfältet högst upp, klickar du på **poster**.
   
    ![Administratören](./media/itrp-tutorial/ic775575.png "Admin")

1. Popup-menyn väljer **personer**.
   
    ![Personer](./media/itrp-tutorial/ic775587.png "personer")

1. Klicka på **Lägg till ny Person** (”+”).
   
    ![Administratören](./media/itrp-tutorial/ic775576.png "Admin")

1. I dialogrutan Lägg till ny Person utför du följande steg:
   
    ![Användaren](./media/itrp-tutorial/ic775577.png "användare") 
      
    a. Skriv den **namn**, **e-post** för ett giltigt AAD-konto som du vill etablera.

    b. Klicka på **Spara**.

>[!NOTE]
>Du kan använda alla andra ITRP användare konto verktyg för att skapa eller API: er som tillhandahålls av ITRP att etablera AAD-användarkonton. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ITRP.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon ITRP, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **ITRP**.

    ![Konfigurera enkel inloggning](./media/itrp-tutorial/tutorial_itrp_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ITRP i åtkomstpanelen du bör få automatiskt loggat in på ditt ITRP program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/itrp-tutorial/tutorial_general_01.png
[2]: ./media/itrp-tutorial/tutorial_general_02.png
[3]: ./media/itrp-tutorial/tutorial_general_03.png
[4]: ./media/itrp-tutorial/tutorial_general_04.png

[100]: ./media/itrp-tutorial/tutorial_general_100.png

[200]: ./media/itrp-tutorial/tutorial_general_200.png
[201]: ./media/itrp-tutorial/tutorial_general_201.png
[202]: ./media/itrp-tutorial/tutorial_general_202.png
[203]: ./media/itrp-tutorial/tutorial_general_203.png

