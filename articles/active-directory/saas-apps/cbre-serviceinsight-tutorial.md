---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med CBRE ServiceInsight | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CBRE ServiceInsight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31d7cedf-2b70-4fa4-9b05-80066d2e8bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 462f44206b4f92629079eb5ae24099eae17291f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121386"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cbre-serviceinsight"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med CBRE ServiceInsight

I den här självstudien får du lära dig hur du integrerar CBRE ServiceInsight med Azure Active Directory (Azure AD). När du integrerar CBRE ServiceInsight med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CBRE ServiceInsight.
* Gör att användarna automatiskt loggas in på CBRE ServiceInsight med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* CBRE ServiceInsight enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* CBRE ServiceInsight stöder **SP**-initierad enkel inloggning
* CBRE ServiceInsight stöder **JIT**-användaretablering (Just-In-Time)

## <a name="adding-cbre-serviceinsight-from-the-gallery"></a>Lägga till CBRE ServiceInsight från galleriet

För att konfigurera integreringen av CBRE ServiceInsight i Azure AD måste du lägga till CBRE ServiceInsight från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **CBRE ServiceInsight** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **CBRE ServiceInsight** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cbre-serviceinsight"></a>Konfigurera och testa en azure AD-inloggning för CBRE ServiceInsight

Konfigurera och testa Azure AD SSO med CBRE ServiceInsight med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i CBRE ServiceInsight.

Så här konfigurerar och testar du Azure AD SSO med CBRE ServiceInsight:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera CBRE ServiceInsight SSO](#configure-cbre-serviceinsight-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa CBRE ServiceInsight-testanvändare](#create-cbre-serviceinsight-test-user)** – om du vill ha en motsvarighet till B.Simon i CBRE ServiceInsight som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **CBRE ServiceInsight-programintegrering** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL i textrutan **Sign-on-URL:**`https://adfs4.mainstreamsasp.com/adfs/ls/`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [CBRE ServiceInsight-kundsupporten](mailto:SISupport@cbre.com) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till CBRE ServiceInsight.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **CBRE ServiceInsight** i listan med program.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-cbre-serviceinsight-sso"></a>Konfigurera CBRE ServiceInsight SSO

För att konfigurera enkel inloggning på **CBRE ServiceInsight**-sidan måste du skicka **URL:en för appfederationsmetadata** till [CBRE ServiceInsight-supportteamet](mailto:SISupport@cbre.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cbre-serviceinsight-test-user"></a>Skapa CBRE ServiceInsight-testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i CBRE ServiceInsight. CBRE ServiceInsight stöder JIT-etablering (Just-In-Time), som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i CBRE ServiceInsight skapas en ny när du försöker komma åt CBRE ServiceInsight.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för CBRE ServiceInsight på åtkomstpanelen bör du loggas in automatiskt i CBRE ServiceInsight-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CBRE ServiceInsight med Azure AD](https://aad.portal.azure.com/)