---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Concur Resor och kostnader | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Concur Travel and Expense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373427"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med Concur Resor och kostnader

I den här självstudien får du lära dig hur du integrerar Concur Travel and Expense med Azure Active Directory (Azure AD). När du integrerar Concur Travel and Expense med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Concur Travel and Expense.
* Gör att användarna automatiskt loggas in på Concur Travel and Expense med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Concur Travel and Expense prenumeration.
* En "Företagsadministratör" roll under ditt Concur-användarkonto. Du kan testa om du har rätt åtkomst genom att gå till [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin). Om du inte har åtkomst kontaktar du Concur support- eller implementeringsprojektledare. 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO.

* Concur Travel and Expense stöder **IDP** och **SP** initierade SSO
* Concur Travel and Expense stödjer testning av SSO i både produktions- och implementeringsmiljö 

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde för var och en av de tre regionerna: USA, EMEA och Kina. Så endast en instans kan konfigureras för varje region i en klient. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Lägga till Concur Resor och utgifter från galleriet

Om du vill konfigurera integreringen av Concur Travel and Expense i Azure AD måste du lägga till Concur Travel and Expense från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Concur Travel and Expense** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **Concur Travel and Expense** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Konfigurera och testa en azure AD-inloggning för Concur Travel and Expense

Konfigurera och testa Azure AD SSO med Concur Travel and Expense med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Concur Travel and Expense.

Om du vill konfigurera och testa Azure AD SSO med Concur Travel and Expense slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Concur Travel and Expense SSO](#configure-concur-travel-and-expense-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Concur Travel and Expense testanvändare](#create-concur-travel-and-expense-test-user)** - att ha en motsvarighet till B.Simon i Concur Resor och kostnader som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Concur Travel and Expense-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP-initierat** läge och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

    > [!NOTE]
    > Identifieraren (enhets-ID) och Svars-URL (Informationstjänst-URL) är regionspecifika. Välj baserat på datacentret för din Concur-entitet. Om du inte känner till datacentret för din Concur-enhet kontaktar du Concur-supporten. 

5. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för **Användarattribut** för att redigera inställningarna. Den unika användaridentifieraren måste matcha Concur-login_id. Vanligtvis bör du ändra **user.userprincipalname** till **user.mail**.

    ![Redigera användarattribut](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta metadata och spara dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Concur Travel and Expense.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Concur Travel and Expense**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-concur-travel-and-expense-sso"></a>Konfigurera Concur Travel and Expense SSO

1. Om du vill konfigurera enkel inloggning på **Concur Travel and Expense-sidan** måste du ladda upp den nedladdade **FEDERATIONSMETAdata-XML:n** till [Concur SSO Self-Service Tool](https://www.concursolutions.com/nui/authadmin/ssoadmin) och logga in med ett konto med rollen Företagsadministratör. 

1. Klicka på **Lägg till**.
1. Ange ett eget namn för din IdP, till exempel "Azure AD (US)". 
1. Klicka på **Ladda upp XML-fil** och bifoga **XML-koden för federationsmetadata** som du hämtade tidigare.
1. Spara ändringen genom att klicka på Lägg till **metadata.**

    ![Concur SSO självbetjäning verktyg skärmdump](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Skapa concur rese- och utgiftstestanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Concur Travel and Expense. Arbeta med Concurs supportteam för att lägga till användarna i plattformen Concur Travel and Expense. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

> [!NOTE]
> B.Simon's Concur-inloggnings-ID måste matcha B.Simons unika identifierare på Azure AD. Till exempel om B.Simon's Azure AD `B.Simon@contoso.com`unik identifer är . B.Simon's Concur inloggnings-ID måste också vara. `B.Simon@contoso.com` 

## <a name="configure-concur-mobile-sso"></a>Konfigurera Concur Mobile SSO
Om du vill aktivera Concur mobile SSO måste du ge Concur-supportteamets **användaråtkomst-URL**. Följ stegen nedan för att få **URL för användaråtkomst** från Azure AD:
1. Gå till **Enterprise-program**
1. Klicka på **Concur Resor och kostnader**
1. Klicka på **Egenskaper**
1. Kopiera **URL för användaråtkomst** och ge den här URL:en till Stöd för Concur

> [!NOTE]
> Självbetjäningsalternativet för att konfigurera SSO är inte tillgängligt så arbeta med Concur-supportteamet för att aktivera mobil SSO. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Concur Travel and Expense på åtkomstpanelen ska du automatiskt loggas in på den Concur Travel and Expense som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Concur Travel and Expense med Azure AD](https://aad.portal.azure.com/)

