---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med EBSCO | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f37085744b9a0e7785ef3a411d53e4df5d15e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595015"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med EBSCO

I den här självstudien får du lära dig hur du integrerar EBSCO med Azure Active Directory (Azure AD). När du integrerar EBSCO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EBSCO.
* Gör att användarna automatiskt loggas in på EBSCO med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* EBSCO enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* EBSCO stöder **SP och IDP** initierade SSO
* EBSCO stöder just in time-användaresetablering **Just In Time**

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-ebsco-from-the-gallery"></a>Lägga till EBSCO från galleriet

Om du vill konfigurera integreringen av EBSCO i Azure AD måste du lägga till EBSCO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **EBSCO** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **EBSCO** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Konfigurera och testa en azure AD-inloggning för EBSCO

Konfigurera och testa Azure AD SSO med EBSCO med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i EBSCO.

Så här konfigurerar och testar du Azure AD SSO med EBSCO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera EBSCO SSO](#configure-ebsco-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa EBSCO-testanvändare](#create-ebsco-test-user)** – om du vill ha en motsvarighet till B.Simon i EBSCO som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **EBSCO** Hantera på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    I textrutan **Identifierare** skriver du en URL: `pingsso.ebscohost.com`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EBSCO Client support team](mailto:support@ebsco.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    o **Unika element:**  

    o **Custid** = Ange unikt EBSCO-kund-ID 

    o **Profil** = Klienter kan skräddarsy länken för att dirigera användare till en viss profil (beroende på vad de köper från EBSCO). De kan ange ett visst profil-ID. De viktigaste ID:na är eds (EBSCO Discovery Service) och ehost (EBSOCOhost databaser). Instruktioner för samma ges [här](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. EBSCO-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

    > [!Note]
    > **Namnattributet** är obligatoriskt och mappas med **namnidentifieringsvärde** i EBSCO-programmet. Detta läggs till som standard så att du inte behöver lägga till detta manuellt.

1. Utöver ovanstående förväntar sig EBSCO-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | E-post   | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera EBSCO.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EBSCO.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **EBSCO**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-ebsco-sso"></a>Konfigurera EBSCO SSO

Om du vill konfigurera enkel inloggning på **EBSCO-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [EBSCO-supportteamet](mailto:support@ebsco.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-ebsco-test-user"></a>Skapa EBSCO-testanvändare

När det gäller EBSCO sker tillhandahållande av användare automatiskt.

**Gör följande för att etablera ett användarkonto:**

Azure AD skickar nödvändiga data till EBSCO-programmet. EBSCO:s användaretablering kan vara automatisk eller kräva ett engångsformulär. Det beror på om klienten har en hel del befintliga EBSCOhost konton med personliga inställningar sparas. Detsamma kan diskuteras med [EBSCO:s supportteam](mailto:support@ebsco.com) under implementeringen. Hursomhelst, behöver klienten inte skapa några EBSCOhost konton före testning.

   > [!Note]
   > Du kan automatisera EBSCOhost användaretablering / anpassning. Kontakta [EBSCO supportteam](mailto:support@ebsco.com) om just-in-time-användaretablering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

1. När du klickar på EBSCO-panelen i åtkomstpanelen bör du automatiskt logga in på ditt EBSCO-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

1. När du har loggat in på programmet klickar du på **inloggningsknappen** i det övre högra hörnet.

    ![EBSCO-inloggningen i listan Program](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Du kommer att få en engångsfråga att para ihop den institutionella / SAML inloggning med en **länk din befintliga MyEBSCOhost konto till ditt institutionskonto nu** eller skapa ett nytt **MyEBSCOhost konto och länka den till ditt institutionskonto**. Kontot används för anpassning på EBSCOhost-programmet. Välj alternativet **Skapa ett nytt konto** så ser du att formuläret för anpassning är ifyllt med värdena från saml-svaret som visas i skärmbilden nedan. Klicka på **Fortsätt för** att spara det här valet.
    
     ![EBSCO-användaren i listan Program](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Efter att ha slutfört ovanstående installation, rensa cookies / cache och logga in igen. Du behöver inte logga in manuellt igen och anpassningsinställningarna kommer ihåg.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova EBSCO med Azure AD](https://aad.portal.azure.com/)