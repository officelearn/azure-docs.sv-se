---
title: 'Självstudier: Azure Active Directory-integrering med Syncplicity | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3df48fd42ca998e2a64f2fbe685047da16a8bae7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193498"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Självstudier: Azure Active Directory-integrering med Syncplicity

I den här självstudien får du lära dig hur du integrerar Syncplicity med Azure Active Directory (AD Azure).

Integrera Syncplicity med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Syncplicity
- Du kan aktivera användarna att automatiskt få loggat in på Syncplicity (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Syncplicity, behöver du följande objekt:

- En Azure AD-prenumeration
- En Syncplicity enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Syncplicity från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-syncplicity-from-the-gallery"></a>Att lägga till Syncplicity från galleriet
För att konfigurera integrering av Syncplicity i Azure AD, som du behöver lägga till Syncplicity från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Syncplicity från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Appar][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Appar][3]

1. I sökrutan skriver **Syncplicity**.

    ![Skapa en Azure AD-användare för testning](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. I resultatpanelen väljer **Syncplicity**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Syncplicity baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Syncplicity är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Syncplicity upprättas.

I Syncplicity, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Syncplicity, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Syncplicity](#creating-a-syncplicity-test-user)**  – du har en motsvarighet för Britta Simon i Syncplicity som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Syncplicity program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Syncplicity:**

1. I Azure-portalen på den **Syncplicity** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. På den **Syncplicity domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<companyname>.syncplicity.com`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Syncplicity klienten supportteamet](https://www.syncplicity.com/contact-us) att hämta dessa värden. 
 

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_general_400.png)

1. På den **Syncplicity Configuration** klickar du på **konfigurera Syncplicity** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Logga in på din **Syncplicity** klient.

1. Klicka på menyn längst upp **admin**väljer **inställningar**, och klicka sedan på **anpassad domän och enkel inloggning**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. På den **enkel inloggning (SSO)** dialogrutan utför följande steg:
   
    ![Enkel inloggning \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. I den **Custom Domain** textrutan skriver du namnet på din domän.
  
    b. Välj **aktiverat** som **enkel inloggningsstatus**.

    c. I den **entitets-Id** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.

    d. I den **inloggning sid-URL** textrutan klistra in den **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    e. I den **sid-URL för utloggning** textrutan klistra in den **URL: en för utloggning** som du har kopierat från Azure-portalen.

    f. I **providern identitetscertifikat**, klickar du på **Välj fil**, och sedan ladda upp det certifikat som du har hämtat från Azure-portalen. 

    g. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-syncplicity-test-user"></a>Skapa en Syncplicity testanvändare
För AAD-användare för att kunna logga in, måste de etableras till Syncplicity program. Det här avsnittet beskrivs hur du skapar AAD-användarkonton i Syncplicity.

**Om du vill konfigurera ett användarkonto till Syncplicity, utför du följande steg:**

1. Logga in på din **Syncplicity** klient (till exempel: `https://company.Syncplicity.com`).

1. Klicka på **admin** och välj **användarkonton**.

1. Klicka på **lägga till en användare**.
   
    ![Hantera användare](./media/syncplicity-tutorial/ic769764.png "hantera användare")

1. Skriv den **e-post-adresser** ett AAD-konto som du vill etablera, väljer du **användaren** som **rollen**, och klicka sedan på **nästa**.
   
    ![Kontoinformation](./media/syncplicity-tutorial/ic769765.png "kontoinformation")
   
    >[!NOTE]
    >Kontoinnehavare för AAD får ett e-postmeddelande, inklusive en länk för att bekräfta och aktivera kontot. 
    > 

1. Välj en grupp i ditt företag som de nya användarna ska bli medlem i och klicka sedan på **nästa**.
   
    ![Gruppmedlemskap](./media/syncplicity-tutorial/ic769772.png "gruppmedlemskap")
   
    >[!NOTE]
    >Om det finns inga grupper som visas, klickar du på **nästa**. 
    > 

1. Markera de mappar som du vill placera under Syncplicitys kontroll på användarens dator och klicka sedan på **nästa**.
   
    ![Syncplicity mappar](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappar")

>[!NOTE]
>Du kan använda alla andra Syncplicity användare konto verktyg för att skapa eller API: er som tillhandahålls av Syncplicity att etablera AAD-användarkonton. 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Syncplicity.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Syncplicity, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Syncplicity**.

    ![Konfigurera enkel inloggning](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Syncplicity i åtkomstpanelen du bör få automatiskt loggat in på ditt Syncplicity program.
## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

