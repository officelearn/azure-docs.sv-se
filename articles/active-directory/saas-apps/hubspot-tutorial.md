---
title: 'Självstudiekurs: Azure Active Directory-integrering med HubSpot | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944453"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Självstudiekurs: Azure Active Directory-integrering med HubSpot

I den här självstudien får du lära dig hur du integrerar HubSpot med Azure Active Directory (Azure AD).

Genom att integrera HubSpot med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till HubSpot.
* Användare kan automatiskt loggas in på HubSpot med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns [i Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med HubSpot behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En HubSpot-prenumeration med enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du azure AD-enkel inloggning i en testmiljö och integrerar HubSpot med Azure AD.

HubSpot stöder följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="add-hubspot-in-the-azure-portal"></a>Lägg till HubSpot i Azure-portalen

Om du vill integrera HubSpot med Azure AD måste du lägga till HubSpot i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj Azure Active **Directory**på den vänstra menyn .

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program väljer du **Nytt program**.

    ![Alternativet Nytt program](common/add-new-app.png)

1. Skriv **HubSpot**i sökrutan . I sökresultaten väljer du **HubSpot**och väljer sedan **Lägg till**.

    ![HubSpot i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med HubSpot baserat på en testanvändare som heter **Britta Simon**. För enkel inloggning för att fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i HubSpot.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med HubSpot måste du slutföra följande byggblock:

| Aktivitet | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera enkel inloggning i HubSpot](#configure-hubspot-single-sign-on)** | Konfigurerar de enskilda inloggningsinställningarna i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Testar Azure AD enkel inloggning för en användare som heter Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda azure AD enkel inloggning. |
| **[Skapa en HubSpot-testanvändare](#create-a-hubspot-test-user)** | Skapar en motsvarighet till Britta Simon i HubSpot som är länkad till Azure AD-representationen av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du Azure AD enkel inloggning med HubSpot i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning i fönstret **HubSpot-programintegrering**. **HubSpot**

    ![Konfigurera alternativet enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enda inloggningsmetod** väljer du **SAML-** eller **SAML/WS-Fed-läge** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret Konfigurera enkel inloggning med SAML väljer du **Redigera** (pennikonen) för att öppna fönstret **Grundläggande SAML-konfiguration.** **Set up Single Sign-On with SAML**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **Grundläggande SAML-konfiguration,** för att konfigurera *IDP-initierat läge,* utför du följande steg:

    1. I rutan **Identifierare** anger du en URL som\/har följande mönster: https: /api.hubspot.com/login-api/v1/saml/login?portalId=\<KUND-ID\>.

    1. I rutan **Svars-URL** anger du en URL\/som har följande\<mönster:\>https: /api.hubspot.com/login-api/v1/saml/acs?portalId= KUND-ID .

    ![HubSpot-domän och webbadresser med enkel inloggning](common/idp-intiated.png)

    > [!NOTE]
    > Om du vill formatera webbadresserna kan du också referera till de mönster som visas i fönstret **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Så här konfigurerar du programmet i *SP-initierat* läge:

    1. Välj **Ange ytterligare webbadresser**.

    1. I rutan **Logga in på URL** anger du **https:\//app.hubspot.com/login**.

    ![Alternativet Ange ytterligare webbadresser](common/metadata-upload-additional-signon.png)

1. Välj **Hämta** bredvid Certifikat (Base64) i avsnittet **SAML-signeringscertifikat** i fönstret **Konfigurera enkel inloggning med SAML-signeringscertifikat** . **Certificate (Base64)** Välj ett nedladdningsalternativ baserat på dina behov. Spara certifikatet på datorn.

    ![Hämtat certifikat (Base64)](common/certificatebase64.png)

1. Kopiera följande webbadresser i avsnittet **Konfigurera HubSpot** baserat på dina behov:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurera enkel inloggning i HubSpot

1. Öppna en ny flik i webbläsaren och logga in på hubspot-administratörskontot.

1. Välj ikonen **Inställningar** längst upp till höger på sidan.

    ![Ikonen Inställningar i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **Kontostandardvärden**.

    ![Alternativet Standard för konto i HubSpot](./media/hubspot-tutorial/config2.png)

1. Bläddra ned till avsnittet **Säkerhet** och välj sedan **Konfigurera**.

    ![Alternativet Konfigurera i HubSpot](./media/hubspot-tutorial/config3.png)

1. Gör så här i avsnittet **Konfigurera enkel inloggning:**

    1. I rutan **Målgrupps-URL(Service Provider Entity ID)** väljer du **Kopiera** för att kopiera värdet. Klistra in värdet i rutan Identifierare i fönstret **Grundläggande SAML-konfiguration** i **Azure-portalen.**

    1. I rutan **Logga in på URl, ACS, Mottagare eller Omdirigera** väljer du **Kopiera** för att kopiera värdet. Klistra in värdet i rutan **Svars-URL** i Azure-portalen i fönstret **Grundläggande SAML-konfiguration.**

    1. I rutan **Identitetsprovideridentifierare eller Utfärdare** i HubSpot klistrar du in värdet för **Azure AD-identifierare** som du kopierade i Azure-portalen.

    1. I rutan Url för **identitetsprovider i url:en identitetsprovider** klistrar du in värdet för **inloggnings-URL** som du kopierade i Azure-portalen.

    1. Öppna filen Certificate(Base64) som du hämtade i Windows Notepad. Markera och kopiera innehållet i filen. Klistra sedan in den i rutan **X.509 Certificate** i HubSpot.

    1. Välj **Verify** (Verifiera).

        ![Avsnittet Konfigurera enkel inloggning i HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Users** > **All users**.

    ![Alternativen Användare och alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet Ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. Ange **User name** **\@\<brittasimon-din-företagsdomän> i rutan Användarnamn.\< förlängning\>**. Till **exempel,\@brittasimon contoso.com**.

    1. Markera kryssrutan **Visa lösenord.** Skriv ned värdet som visas i rutan **Lösenord.**

    1. Välj **Skapa**.

    ![Användarfönstret](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ger du Britta Simon åtkomst till HubSpot så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **Enterprise-program** > **Alla program** > **HubSpot**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **HubSpot**i programlistan .

    ![HubSpot i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **Användare och grupper** väljer du **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du den relevanta rollen för användaren i listan i fönstret **Välj roll.** Välj **Välj**.

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .

### <a name="create-a-hubspot-test-user"></a>Skapa en HubSpot-testanvändare

Om du vill att Azure AD en användare ska kunna logga in på HubSpot måste användaren etableras i HubSpot. I HubSpot är etablering en manuell aktivitet.

Så här etablerar du ett användarkonto i HubSpot:

1. Logga in på hubspotföretagets webbplats som administratör.

1. Välj ikonen **Inställningar** längst upp till höger på sidan.

    ![Ikonen Inställningar i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **Användare & Teams**.

    ![Alternativet Användare & Teams i HubSpot](./media/hubspot-tutorial/user1.png)

1. Välj **Skapa användare**.

    ![Alternativet Skapa användare i HubSpot](./media/hubspot-tutorial/user2.png)

1. I rutan **Lägg till e-posttillägg anger** du användarens e-postadress i\@formatet brittasimon contoso.com och väljer sedan **Nästa**.

    ![Rutan Lägg till e-postadresser i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user3.png)

1. Välj varje flik i avsnittet **Skapa användare.** Ange relevanta alternativ och behörigheter för användaren på varje flik. Välj sedan **Nästa**.

    ![Flikar i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user4.png)

1. Om du vill skicka inbjudan till användaren väljer du **Skicka**.

    ![Alternativet Skicka i HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Användaren aktiveras när användaren har accepterat inbjudan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din Azure AD-konfiguration med hjälp av portalen Mina appar.

När du har konfigurerat enkel inloggning loggas du in på **HubSpot** i portalen Mina appar automatiskt i HubSpot. Mer information om portalen Mina appar finns [i Komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill veta mer:

- [Lista över självstudier för integrering av SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
