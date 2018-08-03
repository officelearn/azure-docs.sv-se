---
title: 'Självstudier: Azure Active Directory-integrering med Rally programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rally programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 2bb9df9fe0cb20cdd50d7ba716ee5cba562f3e1b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448265"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Självstudier: Azure Active Directory-integrering med Rally programvara

I den här självstudien får du lära dig hur du integrerar Rally programvara med Azure Active Directory (AD Azure).

Integrera Rally programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Rally programvara.
- Du kan aktivera användarna att automatiskt få loggat in på Rally programvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Rally programvara, behöver du följande objekt:

- En Azure AD-prenumeration
- En Rally programvara enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till Rally programvara från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-rally-software-from-the-gallery"></a>Lägga till Rally programvara från galleriet
För att konfigurera integrering av Rally programvara i Azure AD, som du behöver lägga till Rally programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rally programvara från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Rally programvara**väljer **Rally programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Rally programvara i resultatlistan](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Rally programvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Rally programvara till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Rally programvara upprättas.

I Rally programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Rally programvara, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Rally programvara](#create-a-rally-software-test-user)**  – du har en motsvarighet för Britta Simon Rally programvara som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Rally programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Rally programvara:**

1. I Azure-portalen på den **Rally programvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

1. På den **Rally programvara domän och URL: er** avsnittet, utför följande steg:

    ![Rally programvara domän och URL: er enkel inloggning för information](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.rally.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Rally klientprogrammet supportteamet](https://help.rallydev.com/) att hämta dessa värden. 
 


1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/rally-software-tutorial/tutorial_general_400.png)

1. På den **Rally programvarukonfiguration** klickar du på **konfigurera Rally programvara** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Rally programvarukonfiguration](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

1. Logga in på din **Rally programvara** klient.

1. I verktygsfältet högst upp, klickar du på **installationsprogrammet**, och välj sedan **prenumeration**.
   
    ![Prenumeration](./media/rally-software-tutorial/ic769531.png "prenumeration")

1. Klicka på den **åtgärd** knappen. Välj **redigera prenumeration** på upp till höger i verktygsfältet.

1. På den **prenumeration** dialogrutan sida, utför följande steg och klicka sedan på **spara och Stäng**:
   
    ![Autentisering](./media/rally-software-tutorial/ic769542.png "autentisering")
   
    a. Välj **Rally eller SSO autentisering** listrutan för autentisering.

    b. I den **-URL för identitetsprovider** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen. 

    c. I den **SSO utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/rally-software-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/rally-software-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/rally-software-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/rally-software-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-rally-software-test-user"></a>Skapa en testanvändare Rally programvara

För Azure AD-användare för att kunna logga in, måste de etableras till programmet Rally programvara med hjälp av deras Azure Active Directory-användarnamn.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din Rally programvara-klient.

1. Gå till **installationsprogrammet \> användare**, och klicka sedan på **+ Lägg till ny**.
   
    ![Användare](./media/rally-software-tutorial/ic781039.png "användare")

1. Skriv namnet i den nya användaren textrutan och klicka sedan på **Lägg till med information om**.

1. I den **Create User** avsnittet, utför följande steg:
   
    ![Skapa användare](./media/rally-software-tutorial/ic781040.png "skapa användare")

    a. I den **användarnamn** textrutan skriver du namnet på användaren som **Brittsimon**.
   
    b. I **e-postadress** textrutan Ange e-postadress för användaren som **brittasimon@contoso.com**.

    c. I **Förnamn** text, ange först namnet på användaren som **Britta**.

    d. I **efternamn** text anger efternamn för användaren som **Simon**.

    e. Klicka på **spara och Stäng**.

   >[!NOTE]
   >Du kan använda andra Rally användaren-konto skapas programverktyg eller API: er som tillhandahålls av Rally programvara för att etablera användarkonton i Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Rally programvara.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Rally programvara, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Rally programvara**.

    ![Länken Rally programvara i listan med program](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Rally programvara i åtkomstpanelen du bör få automatiskt loggat in på ditt Rally programvara.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png

