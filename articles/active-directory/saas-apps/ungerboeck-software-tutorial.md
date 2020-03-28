---
title: 'Självstudiekurs: Azure Active Directory-integrering med Ungerboeck Software | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ungerboeck Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bd861069-8a15-4d29-aa26-0d7be3a0a94a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69c7a7a8fe0ef346cb26e3b0b4f14d3dae7211b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67370308"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Självstudiekurs: Integrera Ungerboeck-programvara med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Ungerboeck-programvara med Azure Active Directory (Azure AD). När du integrerar Ungerboeck Software med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Ungerboeck Software.
* Gör det möjligt för användarna att automatiskt loggas in på Ungerboeck Software med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Ungerboeck Software enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Ungerboeck Software stöder **SP** initierade SSO.

## <a name="adding-ungerboeck-software-from-the-gallery"></a>Lägga Ungerboeck Software från galleriet

Om du vill konfigurera integreringen av Ungerboeck Software i Azure AD måste du lägga till Ungerboeck Software från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Ungerboeck Software** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Ungerboeck Software** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Ungerboeck Software med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Ungerboeck Software.

Så här konfigurerar och testar du Azure AD SSO med Ungerboeck Software:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Ungerboeck Software SSO](#configure-ungerboeck-software-sso)** för att konfigurera SSO-inställningarna på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa Ungerboeck Software testanvändare](#create-ungerboeck-software-test-user)** att ha en motsvarighet till B.Simon i Ungerboeck Software som är kopplad till Azure AD representation av användaren.
6. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan Integrering av **Ungerboeck-programprogram** hittar du avsnittet **Hantera** och väljer **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    
       **För produktionsmiljö:**

       | |
       |-|
       | `https://<SUBDOMAIN>.ungerboeck.com/prod` |
       | `https://<SUBDOMAIN>.ungerboeck.net/prod` |
       | `https://<SUBDOMAIN>.ungerboeck.io/prod` |
       | |

        **För testmiljö:**

       | |
       |-|
       | `https://<SUBDOMAIN>.ungerboeck.com/test` |
       | `https://<SUBDOMAIN>.ungerboeck.net/test` |
       | `https://<SUBDOMAIN>.ungerboeck.io/test` |
       | |

   > [!NOTE]
   > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren som förklaras senare i avsnittet **Konfigurera Ungerboeck Software Single Sign-On** i självstudien.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **tumavtrycket** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera Ungerboeck-programvara.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ungerboeck-software-sso"></a>Konfigurera Ungerboeck Software SSO

Om du vill konfigurera enkel inloggning på **Ungerboecks programvarusida** måste du skicka **tumavtrycksvärdet** och lämpliga kopierade url:er från Azure-portalen till [Ungerboeck Software supportteam](mailto:Rhonda.Jannings@ungerboeck.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Ungerboeck Software.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Ungerboeck Software**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-ungerboeck-software-test-user"></a>Skapa Ungerboeck Software testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Ungerboeck Software. Arbeta med [Ungerboeck Software supportteam](mailto:Rhonda.Jannings@ungerboeck.com) för att lägga till användarna i Ungerboeck Software plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Ungerboeck Software på åtkomstpanelen ska du automatiskt loggas in på ungerboeck-programvaran som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)