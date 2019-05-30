---
title: 'Självstudier: Azure Active Directory-integrering med HubSpot | Microsoft Docs'
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
ms.openlocfilehash: 3d74cc1665867568032bb1343e4f2c26c50fe15a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65770179"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Självstudier: Azure Active Directory-integrering med HubSpot

I den här självstudien får du lära dig hur du integrerar HubSpot med Azure Active Directory (AD Azure).

Integrera HubSpot med Azure AD ger dig följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till HubSpot.
* Användare kan vara loggas in automatiskt till HubSpot med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med HubSpot, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En HubSpot prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera HubSpot med Azure AD.

HubSpot har stöd för följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="add-hubspot-in-the-azure-portal"></a>Lägg till HubSpot i Azure portal

Om du vill integrera HubSpot med Azure AD, måste du lägga till HubSpot i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn väljer du **Azure Active Directory**.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program, Välj **nytt program**.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **HubSpot**. I sökresultaten väljer **HubSpot**, och välj sedan **Lägg till**.

    ![HubSpot i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HubSpot baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i HubSpot.

Om du vill konfigurera och testa Azure AD enkel inloggning med HubSpot, måste du utföra följande byggblock:

| Uppgift | Beskrivning |
| --- | --- |
| **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera HubSpot enkel inloggning](#configure-hubspot-single-sign-on)** | Konfigurerar inställningar för enkel inloggning i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Med namnet Britta Simon tester Azure AD enkel inloggning för en användare. |
| **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda Azure AD enkel inloggning. |
| **[Skapa en HubSpot testanvändare](#create-a-hubspot-test-user)** | Skapar en motsvarighet för Britta Simon i HubSpot som är länkad till en Azure AD-representation av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Kontrollerar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet konfigurerar du Azure AD enkel inloggning med HubSpot i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)i den **HubSpot** application integration väljer **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML** eller **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I den **SAML grundkonfiguration** fönstret Konfigurera *IDP-initierad läge*, gör du följande:

    1. I den **identifierare** anger en URL som har följande mönster: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<kund-ID\>.

    1. I den **svars-URL** anger en URL som har följande mönster: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<kund-ID\>.

    ![HubSpot domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    > [!NOTE]
    > Om du vill formatera URL: er, kan du även gå till de mönster som visas i den **SAML grundkonfiguration** fönstret i Azure-portalen.

1. Konfigurera programmet i *SP-initierat* läge:

    1. Välj **ange ytterligare webbadresser**.

    1. I den **inloggnings-URL** anger **https:\//app.hubspot.com/login**.

    ![Set-alternativ för ytterligare URL: er](common/metadata-upload-additional-signon.png)

1. I den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** bredvid **certifikat (Base64)** . Markera ett nedladdningsalternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtningsalternativet certifikat (Base64)](common/certificatebase64.png)

1. I den **konfigurera HubSpot** avsnittet, kopiera följande URL: er utifrån dina behov:

    * Inloggningswebbadress
    * Microsoft Azure Active Directory-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurera HubSpot enkel inloggning

1. Öppna en ny flik i webbläsaren och logga in på ditt administratörskonto för HubSpot.

1. Välj den **inställningar** ikonen i det övre högra hörnet på sidan.

    ![Ikonen för inställningar i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **konto standardvärden**.

    ![Det konto som standard alternativet i HubSpot](./media/hubspot-tutorial/config2.png)

1. Rulla ned till den **Security** avsnittet och välj sedan **konfigurera**.

    ![Ställ in alternativet i HubSpot](./media/hubspot-tutorial/config3.png)

1. I den **Konfigurera enkel inloggning** avsnittet, gör du följande:

    1. I den **målgrupp URl (Service Provider entitets-ID)** väljer **kopia** att kopiera värdet. I Azure-portalen i den **grundläggande SAML-konfiguration** fönstret klistrar du in värdet i den **identifierare** box.

    1. I den **logga på URL: en, ACS, mottagaren eller omdirigering** väljer **kopia** att kopiera värdet. I Azure-portalen i den **grundläggande SAML-konfiguration** fönstret klistrar du in värdet i den **svars-URL** box.

    1. I HubSpot, i den **identitet identifierare eller utfärdar-URL** rutan, klistra in värdet för **Azure AD-identifierare** som du kopierade i Azure-portalen.

    1. I HubSpot, i den **enkel inloggnings-URL för identitetsprovider** rutan, klistra in värdet för **inloggnings-URL** som du kopierade i Azure-portalen.

    1. Öppna filen Certificate(Base64) som du laddade ned i anteckningar för Windows. Markera och kopiera innehållet i filen. Sedan i HubSpot, klistra in den i den **X.509-certifikat** box.

    1. Välj **Kontrollera**.

        ![Ställ in enkel inloggning-avsnittet i HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användarna och alternativ för alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **brittasimon\@\<din företagsdomän >.\< tillägget\>** . Till exempel **brittasimon\@contoso.com**.

    1. Välj den **Show lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska ge du Britta Simon åtkomst till HubSpot så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **HubSpot**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan med program väljer **HubSpot**.

    ![HubSpot i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** fönstret Välj rollen relevant för användaren i listan. Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-hubspot-test-user"></a>Skapa en HubSpot testanvändare

Att aktivera Azure AD som en användare att logga in på HubSpot, användaren måste etableras i HubSpot. I HubSpot är etablering en manuell aktivitet.

Att etablera ett användarkonto i HubSpot:

1. Logga in på webbplatsen HubSpot företag som administratör.

1. Välj den **inställningar** ikonen i det övre högra hörnet på sidan.

    ![Ikonen för inställningar i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **användare och team**.

    ![Alternativet användare och team i HubSpot](./media/hubspot-tutorial/user1.png)

1. Välj **skapa användare**.

    ![Alternativet Skapa användare i HubSpot](./media/hubspot-tutorial/user2.png)

1. I den **Lägg till e-addess(es)** ange användarens e-postadress i formatet brittasimon\@contoso.com och välj sedan **nästa**.

    ![Lägg till e-postadresserna rutan i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user3.png)

1. I den **skapa användare** väljer du varje flik. Ange de relevanta alternativ och behörigheter för användaren på varje flik. Välj sedan **Nästa**.

    ![Flikar i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user4.png)

1. Skicka inbjudan till användaren, Välj **skicka**.

    ![Alternativet Skicka i HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Användaren är aktiverat när användaren har accepterat inbjudan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du har konfigurerat enkel inloggning, när du väljer **HubSpot** i portalen Mina appar loggas du automatiskt till HubSpot. Läs mer om portalen Mina appar [öppna och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du granska dessa artiklar:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
