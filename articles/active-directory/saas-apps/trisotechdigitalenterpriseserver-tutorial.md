---
title: 'Självstudier: Azure Active Directory-integration med Trisotech digitala Enterprise Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trisotech digitala Enterprise-Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: e36a4be3a95b67c040855171d4b167e495a22496
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439634"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Självstudier: Azure Active Directory-integration med Trisotech digitala Enterprise Server

I den här självstudien får du lära dig hur du integrerar Trisotech digitala Enterprise Server med Azure Active Directory (AD Azure).

Integrera Trisotech digitala Enterprise Server med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Trisotech digitala Enterprise-Server.
- Du kan aktivera användarna att automatiskt få loggat in på Trisotech digitala Enterprise Server (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Trisotech digitala Enterprise Server behöver du följande objekt:

- En Azure AD-prenumeration
- En Trisotech digitala Enterprise Server enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Trisotech digitala Enterprise Server från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Att lägga till Trisotech digitala Enterprise Server från galleriet
För att konfigurera integrering av Trisotech digitala Enterprise Server i Azure AD, som du behöver lägga till Trisotech digitala Enterprise Server från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Trisotech digitala Enterprise Server från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Trisotech digitala Enterprise Server**väljer **Trisotech digitala Enterprise Server** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Trisotech digitala Enterprise-Server i listan med resultat](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Trisotech digitala Enterprise Server baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Trisotech digitala Enterprise Server till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Trisotech digitala Enterprise Server upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Trisotech digitala Enterprise Server, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Trisotech digitala Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)**  – du har en motsvarighet för Britta Simon i Trisotech digitala företagsservern som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Trisotech digitala Enterprise Server-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Trisotech digitala Enterprise Server:**

1. I Azure-portalen på den **Trisotech digitala Enterprise Server** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. På den **Trisotech digitala Enterprise Server-domän och URL: er** avsnittet, utför följande steg:

    ![Trisotech digitala Enterprise Server-domän och URL: er med enkel inloggning för information](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.trisotech.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Trisotech digitala Enterprise Server Client supportteamet](mailto:support@trisotech.com) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar. 

    ![Länk för hämtning av certifikat](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster logga du in på webbplatsen Trisotech digitala Enterprise-serverkonfiguration företagets som administratör.

1. Klicka på den **menyikonen** och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. Välj **Användarprovider**.

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. I den **providern användarkonfigurationer** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Välj **skyddas Assertion Markup Language 2.2 SAML** i listrutan i det **autentiseringsmetod**.

    b. I den **Metadata_url** textrutan klistra in den **Appfederationsmetadata** värde, som du har kopierat utgör Azure-portalen.

    c. I den **program-ID** textrutan anger du URL: en med hjälp av följande mönster: `https://<companyname>.trisotech.com`.

    d. Klicka på **Spara**

    e. Ange domännamnet i den **tillåtna domäner (empty innebär alla)** textrutan automatiskt tilldelar licenser för användare som matchar de domäner som tillåts

    f. Klicka på **Spara**

 ### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Skapa en testanvändare Trisotech digitala Enterprise Server

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Trisotech digitala Enterprise-Server. Trisotech digitala Enterprise Server stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Trisotech digitala Enterprise Server om den inte finns.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Trisotech digitala Enterprise Server-supportteamet](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Trisotech digitala Enterprise-Server.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Trisotech digitala Enterprise Server, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Trisotech digitala Enterprise Server**.

    ![Länken Trisotech digitala Enterprise-Server i listan med program](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Trisotech digitala Enterprise Server i åtkomstpanelen du bör få automatiskt loggat in på ditt Trisotech digitala Enterprise Server-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

