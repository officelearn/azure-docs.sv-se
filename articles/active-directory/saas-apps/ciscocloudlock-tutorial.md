---
title: 'Självstudiekurs: Azure Active Directory-integrering med Cloud Security Fabric | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cloud Security Fabric.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b1471abd7e057af919ed274547daf94d356c2b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "70213603"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Självstudiekurs: Integrera cloud security-infrastrukturen med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Cloud Security Fabric med Azure Active Directory (Azure AD). När du integrerar Cloud Security Fabric med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cloud Security Fabric.
* Gör att användarna automatiskt loggas in i Cloud Security Fabric med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En-SSO-prenumeration (Cloud Security Fabric) aktiverade prenumerationen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Cloud Security Fabric **SP** stöder SP-initierad SSO

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Lägga till cloud security fabric från galleriet

Om du vill konfigurera integreringen av Cloud Security Fabric i Azure AD måste du lägga till Cloud Security Fabric från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Molnsäkerhetstyget** fabric i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Cloud Security Fabric** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Cloud Security Fabric med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cloud Security Fabric.

Så här konfigurerar och testar du Azure AD SSO med Cloud Security Fabric:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Cloud Security Fabric SSO](#configure-the-cloud-security-fabric-sso)** - för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
5. **[Skapa cloud security fabric-testanvändaren](#create-the-cloud-security-fabric-test-user)** – om du vill ha en motsvarighet till B.Simon i Cloud Security Fabric som är länkad till Azure AD-representationen för användaren.
6. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Cloud Security Fabric-program** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I rutan **Inloggnings-URL** anger du en URL: 

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [supportteamet för Cloud Security Fabric-klienten](mailto:support@cloudlock.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

5. Om du vill ändra **signeringsalternativen** enligt dina krav klickar du på **Knappen Redigera** för att öppna dialogrutan **SAML-signeringscertifikat.**

    ![Saml Svar](./media/ciscocloudlock-tutorial/saml.png)

    a. Välj alternativet **Signera SAML-svar och kontroll** för **signeringsalternativ**.

    b. Välj alternativet **SHA-256** för **signeringsalgoritm**.

    c. Klicka på **Spara**.  

6. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera Molnsäkerhetsinfrastruktur.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>Konfigurera SSO för molnsäkerhetsinfrastruktur

Om du vill konfigurera enkel inloggning på cloud **security fabric-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Cloud Security Fabric](mailto:support@cloudlock.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.
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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cloud Security Fabric.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Cloud Security **Fabric**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-the-cloud-security-fabric-test-user"></a>Skapa testanvändaren Cloud Security Fabric

I det här avsnittet skapar du en användare som heter B.Simon i Cloud Security Fabric. Arbeta med [Supportteamet för Cloud Security Fabric](mailto:support@cloudlock.com) för att lägga till användarna i plattformen Cloud Security Fabric. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Cloud Security Fabric på åtkomstpanelen bör du automatiskt loggas in på det Molnsäkerhetsinfrastruktur som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
