---
title: 'Självstudier: Azure Active Directory-integrering med OfficeSpace programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OfficeSpace programvara.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d1c48c10d2c58e5cb2ffd7df296390bfaf765bd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206403"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Självstudier: Azure Active Directory-integrering med OfficeSpace programvara

I den här självstudien får du lära dig hur du integrerar OfficeSpace programvara med Azure Active Directory (AD Azure).

Integrera OfficeSpace programvara med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till OfficeSpace programvara.
- Du kan aktivera användarna att automatiskt få loggat in på OfficeSpace programvara (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OfficeSpace programvara, behöver du följande objekt:

- En Azure AD-prenumeration
- En OfficeSpace programvara enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägga till OfficeSpace programvara från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-officespace-software-from-the-gallery"></a>Lägga till OfficeSpace programvara från galleriet
För att konfigurera integrering av OfficeSpace programvara i Azure AD, som du behöver lägga till OfficeSpace programvara från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till OfficeSpace programvara från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **OfficeSpace programvara**väljer **OfficeSpace programvara** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![OfficeSpace programvara i resultatlistan](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med OfficeSpace programvara baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i OfficeSpace programvara till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i OfficeSpace programvara upprättas.

I OfficeSpace programvara, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med OfficeSpace programvara, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare OfficeSpace programvara](#create-a-officespace-software-test-user)**  – du har en motsvarighet för Britta Simon OfficeSpace programvara som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i OfficeSpace programmet.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med OfficeSpace programvara:**

1. I Azure-portalen på den **OfficeSpace programvara** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

1. På den **OfficeSpace programvara domän och URL: er** avsnittet, utför följande steg:

    ![OfficeSpace programvara domän och URL: er med enkel inloggning för information](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. I textrutan **Identifierare** anger du en URL med följande mönster: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [OfficeSpace klientprogrammet supportteamet](mailto:support@officespacesoftware.com) att hämta dessa värden. 

1. OfficeSpace programvara förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera attribut](./media/officespace-tutorial/tutorial_officespace_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan **user.mail** som **användaridentifierare** och för varje rad som visas i i tabellen nedan, utför följande steg:
    
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | e-post | user.mail |
    | namn | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera Lägg till ](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Konfigurera attribut](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Länk för nedladdning av certifikatet](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/officespace-tutorial/tutorial_general_400.png)

1. På den **OfficeSpace programvarukonfiguration** klickar du på **konfigurera OfficeSpace programvara** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![OfficeSpace Software Configuration](./media/officespace-tutorial/tutorial_officespace_configure.png) 

1. Logga in på din klient för OfficeSpace programvara som en administratör i ett annat webbläsarfönster.

1. Gå till **inställningar** och klicka på **Anslutningsappar**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_002.png)

1. Klicka på **SAML-autentisering**.

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_003.png)

1. I den **SAML-autentisering** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning på appsidan](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. I den **utloggnings-url för providern** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.

    b. I den **klienten idp målwebbadressen** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL** som du har kopierat från Azure-portalen.

    c. Klistra in den **tumavtryck** värde som du har kopierat från Azure-portalen i den **klienten IDP certifikat fingeravtryck** textrutan. 

    d. Klicka på **spara inställningarna för**.


> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/officespace-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/officespace-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/officespace-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/officespace-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-officespace-software-test-user"></a>Skapa en testanvändare OfficeSpace programvara

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon OfficeSpace programvara. OfficeSpace programvara stöder just-in-time-etablering, vilket är som standard aktiverat.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt OfficeSpace programvara om det inte finns ännu.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta [OfficeSpace programvara supportteamet](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OfficeSpace programvara.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon OfficeSpace programvara, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **OfficeSpace programvara**.

    ![Länken OfficeSpace programvara i listan med program](./media/officespace-tutorial/tutorial_officespace_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen OfficeSpace programvara i åtkomstpanelen du bör få automatiskt loggat in på programmets OfficeSpace programvara.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

