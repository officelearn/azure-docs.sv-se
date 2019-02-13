---
title: 'Självstudier: Azure Active Directory-integrering med EBSCO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0973001cda4a817ab464f825ad50deb52019f65
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180665"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Självstudier: Azure Active Directory-integrering med EBSCO

I den här självstudien får du lära dig hur du integrerar EBSCO med Azure Active Directory (AD Azure).

Integrera EBSCO med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till EBSCO.
- Du kan aktivera användarna att automatiskt få loggat in på EBSCO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med EBSCO, behöver du följande objekt:

- En Azure AD-prenumeration
- En EBSCO enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till EBSCO från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ebsco-from-the-gallery"></a>Att lägga till EBSCO från galleriet
För att konfigurera integrering av EBSCO i Azure AD, som du behöver lägga till EBSCO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till EBSCO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **EBSCO**väljer **EBSCO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![EBSCO i resultatlistan](./media/ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med EBSCO baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i EBSCO är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i EBSCO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med EBSCO, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare EBSCO](#create-an-ebsco-test-user)**  – du kan automatisera EBSCOhost etablering/Användaranpassning. EBSCO stöder Just-In-Time etableringen av användare.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt EBSCO program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med EBSCO:**

1. I Azure-portalen på den **EBSCO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/ebsco-tutorial/tutorial_ebsco_samlbase.png)

1. På den **EBSCO domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![EBSCO domän och URL: er med enkel inloggning för information](./media/ebsco-tutorial/tutorial_ebsco_url.png)

    I den **identifierare** textrutan anger du ett URL: `pingsso.ebscohost.com`

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![EBSCO domän och URL: er med enkel inloggning för information](./media/ebsco-tutorial/tutorial_ebsco_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EBSCO klienten supportteamet](mailto:sso@ebsco.com) att hämta värdet. 

    o **unika element:**  

    o **Custid** = RETUR unika EBSCO kund-ID 

    o **profil** = klienter kan anpassa på länken för att dirigera användare till en specifik profil (beroende på vad de köper från EBSCO). De kan ange en specifik profil-ID. De huvudsakliga ID: N är eds (EBSCO identifieringstjänst) och ehost (EBSOCOhost databaser). Instruktioner för samma ges [här](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. EBSCO program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning](./media/ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > Den **namn** attributet är obligatoriskt och det är kopplat med **användaridentifierare** i EBSCO program. Detta läggs som standard så du inte behöver lägga till detta manuellt.
    
1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | E-post   | user.mail |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ebsco-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/ebsco-tutorial/tutorial_attribute_05.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/ebsco-tutorial/tutorial_ebsco_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/ebsco-tutorial/tutorial_general_400.png)
    
1. Att konfigurera enkel inloggning på **EBSCO** sida, som du behöver skicka de hämtade **XML-Metadata för** till [EBSCO supportteam](mailto:sso@ebsco.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ebsco-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ebsco-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ebsco-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ebsco-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-ebsco-test-user"></a>Skapa en EBSCO testanvändare

När det gäller EBSCO är etableringen av användare automatisk.

**Utför följande steg för att etablera ett användarkonto:**

Azure AD skickar nödvändiga data till EBSCO program. Etableringen av EBSCOS användare kan vara automatisk eller kräver ett enstaka formulär. Det beror på om klienten har en massa befintlig EBSCOhost konton med personliga inställningar sparas. Samma kan diskuteras med den [EBSCO supportteam](mailto:sso@ebsco.com) under genomförandet. I båda fallen klienten behöver inte skapa EBSCOhost konton innan du testar.

   >[!Note]
   >Du kan automatisera EBSCOhost etablering/Användaranpassning. Kontakta [EBSCO supportteam](mailto:sso@ebsco.com) om Just-In-Time etableringen av användare. 
 
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EBSCO.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon EBSCO, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **EBSCO**.

    ![Länken EBSCO i listan med program](./media/ebsco-tutorial/tutorial_ebsco_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. När du klickar på panelen EBSCO i åtkomstpanelen du bör få automatiskt loggat in på ditt EBSCO program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

1. När du loggar in till programmet, klickar du på den **logga in** knappen i övre högra hörnet.

    ![EBSCO inloggning från i listan med program](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
1. Du får ett enstaka meddelande att para ihop institutionella SAML/logga in med ett **länka ditt befintliga MyEBSCOhost-konto till din institution konto nu** eller **skapa ett nytt konto MyEBSCOhost och länka det till din institution konto**. Kontot används för användaranpassning på EBSCOhost-programmet. Välj alternativet **skapar ett nytt konto** och ser du att formuläret för anpassning före slutfördes med värden från saml-svar som visas i skärmbilden nedan. Klicka på **”Fortsätt”** att spara det här alternativet.
    
     ![EBSCO användaren i listan med program](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. När du har slutfört inställningarna ovan kan du rensa cookies/cacheminnet och logga in igen. Du behöver inte manuellt inloggning från igen och anpassningsinställningar ska sparas

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ebsco-tutorial/tutorial_general_01.png
[2]: ./media/ebsco-tutorial/tutorial_general_02.png
[3]: ./media/ebsco-tutorial/tutorial_general_03.png
[4]: ./media/ebsco-tutorial/tutorial_general_04.png

[100]: ./media/ebsco-tutorial/tutorial_general_100.png

[200]: ./media/ebsco-tutorial/tutorial_general_200.png
[201]: ./media/ebsco-tutorial/tutorial_general_201.png
[202]: ./media/ebsco-tutorial/tutorial_general_202.png
[203]: ./media/ebsco-tutorial/tutorial_general_203.png

