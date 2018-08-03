---
title: 'Självstudier: Azure Active Directory-integration med Palo Alto Networks - internt Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks - internt Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: fa47eaea590ecb84386a6e0ce4eff0a6933be554
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444209"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Självstudier: Azure Active Directory-integration med Palo Alto Networks - internt Portal

I den här självstudien får du lära dig hur du integrerar Palo Alto Networks - internt Portal med Azure Active Directory (AD Azure).

Integrera Palo Alto Networks - internt Portal med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Palo Alto Networks - internt Portal.
- Du kan aktivera användarna att automatiskt få loggat in på Palo Alto Networks - internt Portal (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo Alto Networks - internt-portalen behöver du följande objekt:

- En Azure AD-prenumeration
- En Palo Alto Networks - internt Portal enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Palo Alto Networks - internt portalen från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Att lägga till Palo Alto Networks - internt portalen från galleriet
Om du vill konfigurera integreringen av Palo Alto Networks - internt Portal till Azure AD som du behöver lägga till Palo Alto Networks - internt portalen från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Palo Alto Networks - internt portalen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Palo Alto Networks - internt Portal**väljer **Palo Alto Networks - internt Portal** resultatet panelen klickar **Lägg till** för att lägga till programmet .

    ![Palo Alto Networks - internt Portal i resultatlistan](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - internt Portal baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Palo Alto Networks - internt portalen är att en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Palo Alto Networks - internt Portal upprättas.

I Palo Alto Networks - internt Portal tilldelar du värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Palo Alto Networks - internt-portalen måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Palo Alto Networks - internt Portal testanvändare](#create-a-palo-alto-networks---captive-portal-test-user)**  – du har en motsvarighet för Britta Simon i Palo Alto Networks - internt Portal som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Palo Alto Networks - internt portalprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Palo Alto Networks - internt Portal:**

1. I Azure-portalen på den **Palo Alto Networks - internt Portal** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_samlbase.png)

1. På den **Palo Alto Networks - internt Portal domän och URL: er** avsnittet, utför följande steg:

    ![Palo Alto Networks - internt Portal domän och URL: er enkel inloggning för information](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta [Palo Alto Networks - internt Portal supportteamet](https://support.paloaltonetworks.com/support) att hämta dessa värden.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_400.png)

1. Öppna webbplatsen Palo Alto som en administratör i ett annat webbläsarfönster.

1. Klicka på **enhet**.

    ![Konfigurera Palo Alto enkel inloggning](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

1. Välj **SAML-identitetsprovider** i det vänstra navigeringsfönstret och klickar på ”Importera” om du vill importera metadatafilen.

    ![Konfigurera Palo Alto enkel inloggning](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Utför följande åtgärder i fönstret Import

    ![Konfigurera Palo Alto enkel inloggning](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. I den **profilnamn** textrutan, ange ett namn t.ex Azure AD-Admin-Gränssnittet.
    
    b. I **identitet providern Metadata**, klickar du på **Bläddra** och välj metadata.xml-fil som du har hämtat från Azure-portalen
    
    c. Klicka på **OK**

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/paloaltonetworks-captiveportal-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-palo-alto-networks---captive-portal-test-user"></a>Skapa en Palo Alto Networks - internt Portal testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Palo Alto Networks - internt Portal. Palo Alto Networks - internt Portal har stöd för just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Palo Alto Networks - internt portalen om det inte finns ännu. 

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta den [Palo Alto Networks - internt Portal supportteamet](https://support.paloaltonetworks.com/support).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Palo Alto Networks - internt Portal.

![Tilldela rollen][200] 

**Utför följande steg om du vill tilldela Britta Simon Palo Alto Networks - internt Portal:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Palo Alto Networks - internt Portal**.

    ![Palo Alto-nätverk – internt Portal-länken i programlistan](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltocaptiveportal_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Internt Portal är konfigurerad bakom brandväggen på Windows-VM.  Att testa enkel inloggning på internt Portal, logga in på Windows-dator med RDP. Öppna en webbläsare till valfri webbplats i RDP-session från, den automatiskt ska öppna URL: en för enkel inloggning och fråga efter autentisering. När så är klar kan ska du kunna navgiate till webbplatser. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-captiveportal-tutorial/tutorial_general_203.png

