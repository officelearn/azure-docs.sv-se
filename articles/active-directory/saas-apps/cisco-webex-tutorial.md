---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med Cisco WebEx-möten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco WebEx-möten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adda943676eb4918369d3cb8027f559d5dd0f6e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med Cisco WebEx-möten

I den här självstudien får du lära dig hur du integrerar Cisco WebEx-möten med Azure Active Directory (Azure AD). När du integrerar Cisco WebEx-möten med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco WebEx-möten.
* Gör det möjligt för användarna att logga in automatiskt till Cisco WebEx-möten med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Cisco WebEx-möten enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cisco WebEx-möten stöder **SP-och IDP** -INITIERAd SSO

* Cisco WebEx-möten stöder **just-in-Time** User-etablering

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Lägga till Cisco WebEx-möten från galleriet

Om du vill konfigurera integreringen av Cisco WebEx-möten i Azure AD måste du lägga till Cisco WebEx-möten från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Cisco WebEx-möten** i sökrutan.
1. Välj **Cisco WebEx-möten** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Konfigurera och testa enkel inloggning med Azure AD för Cisco WebEx-möten

Konfigurera och testa Azure AD SSO med Cisco WebEx-möten med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cisco WebEx-möten.

Om du vill konfigurera och testa Azure AD SSO med Cisco WebEx-möten slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera Cisco WebEx-möten SSO](#configure-cisco-webex-meetings-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Cisco WebEx-möten testa användare](#create-cisco-webex-meetings-test-user)** – för att få en motsvarighet till B. Simon i Cisco WebEx-möten som är länkade till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Cisco WebEx-möten** program integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** laddar du upp den hämtade **tjänst leverantörens metadatafil** och konfigurerar programmet i **IDP** initierat läge genom att utföra följande steg:

    >[!Note]
    >Du kommer att hämta metadata-filen för tjänst leverantören, som beskrivs senare i avsnittet **Konfigurera Cisco WebEx-möten SSO** i självstudien. 

    a. Klicka på **ladda upp metadatafilen**.

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

5. Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:
    
    I text rutan för inloggnings- **URL** skriver du URL: en med följande mönster:`https://<customername>.webex.com`

5. Cisco WebEx-Mötes program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

6. Förutom över, förväntar Cisco WebEx-möten att fler attribut skickas tillbaka i SAML-svar. I avsnittet användar anspråk i dialog rutan användarattribut, utför följande steg för att lägga till SAML-token-attributet som visas i tabellen nedan: 

    | Name | Källattribut|
    | ---------------|  --------- |
    |   förnamn    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Välj det attributvärde som visas för den raden i list rutan i listan **källattribut** .

    f. Klicka på **Spara**.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera Cisco WebEx-möten** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
    1. I **Namn**-fältet skriver du `B.Simon`.  
    1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
    1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cisco WebEx-möten.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Cisco WebEx-möten**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurera Cisco WebEx-möten SSO

1. Gå till `https://<customername>.webex.com/admin` URL med autentiseringsuppgifterna för administration.

2. Gå till **vanliga plats inställningar** och navigera till **SSO-konfiguration**.
 
    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. På sidan **Administration av WebEx** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Välj **SAML 2,0** som **Federations protokoll**.

    b. Klicka på **Importera SAML-metadata** för att ladda upp metadatafilen, som du har laddat ned från Azure Portal.

    c. Klicka på knappen **Exportera** för att ladda ned metadata-filen för tjänste leverantören och ladda upp den i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    d. I text rutan **AuthContextClassRef** skriver `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` du och om du vill aktivera MFA med Azure AD anger du de två värdena som`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Välj **Skapa automatiskt konto**.

    >[!NOTE]
    >Om du vill aktivera användar etablering **just-in-Time** måste du kontrol lera att det **automatiska kontot har skapats**. Förutom att attribut för SAML-token måste skickas i SAML-svaret.

    f. Klicka på **Spara**.

    >[!NOTE]
    >Den här konfigurationen gäller endast för kunder som använder WebEx UserID i e-postformat.

### <a name="create-cisco-webex-meetings-test-user"></a>Skapa Cisco WebEx-möten test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i Cisco WebEx-möten. Cisco WebEx-möten stöder **just-in-Time-** etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Cisco WebEx-möten, skapas en ny när du försöker komma åt Cisco WebEx-möten.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Cisco WebEx-möten på åtkomst panelen, bör du loggas in automatiskt på de Cisco WebEx-möten som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ServiceNow med Azure AD](https://aad.portal.azure.com)
