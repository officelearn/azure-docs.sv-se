---
title: 'Snabb start: Konfigurera enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient'
description: Den här snabb starten vägleder dig genom processen med att konfigurera enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038998"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: Konfigurera enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient

Kom igång med förenklade användar inloggningar genom att konfigurera SSO för ett program som du har lagt till i din Azure AD-klient. När du har konfigurerat SSO kan användarna logga in i ett program med sina autentiseringsuppgifter för Azure AD. SSO ingår i den kostnads fria versionen av Azure AD.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera SSO för ett program som du har lagt till i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- Ett program som stöder enkel inloggning och som redan har förkonfigurerats och lagts till i Azure AD-galleriet. De flesta appar kan använda Azure AD för enkel inloggning. Apparna i Azure AD-galleriet har förkonfigurerats. Om din app inte finns med i listan eller om den är en anpassad utvecklad app kan du fortfarande använda den med Azure AD. Titta närmare på självstudierna och annan dokumentation i innehålls förteckningen. Den här snabb starten fokuserar på appar som har förkonfigurerats för enkel inloggning och lagts till i Azure AD-galleriet av apps-utvecklare.
- (Valfritt: slut för ande av [Visa dina appar](view-applications-portal.md)).
- (Valfritt: slut för ande av [Lägg till en app](add-application-portal.md)).
- (Valfritt: Slutför [konfiguration av en app](add-application-portal-configure.md)).


>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktions miljö för att testa stegen i den här snabb starten.


## <a name="enable-single-sign-on-for-an-app"></a>Aktivera enkel inloggning för en app

När du har lagt till ett program i Azure AD-klienten visas den direkt på sidan Översikt för det. Om du konfigurerar ett program som redan har lagts till kan du titta på den första snabb starten, så vägleder dig genom att visa de program som har lagts till i din klient. 

Så här konfigurerar du enkel inloggning för ett program:

1. I Azure AD-portalen väljer du **företags program** och sedan söker du efter och väljer det program som du vill konfigurera för enkel inloggning.
2. I avsnittet hantera väljer du **enkel inloggning** för att öppna rutan Egenskaper för redigering.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Visar konfigurations sidan för enkel inloggning i Azure AD-portalen.":::
3. Välj SAML för att öppna sidan SSO-konfiguration. I det här exemplet är det program vi konfigurerar för SSO GitHub. När GitHub har kon figurer ATS kan användarna logga in på GitHub med sina autentiseringsuppgifter från din Azure AD-klient.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Visar konfigurations sidan för enkel inloggning på GitHub.":::
4. Processen att konfigurera ett program för att använda Azure AD för SAML-baserad SSO varierar beroende på vilket program som används. Observera att det finns en länk till vägledningen för GitHub. Du kan hitta guider för andra appar på:https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Följ anvisningarna för att konfigurera SSO för programmet. Många program har särskilda prenumerations krav för SSO-funktioner. Till exempel kräver GitHub en Enterprise-prenumeration.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Visar alternativet för enkel inloggning i Enterprise-prenumerationen på prissättnings sidan för GitHub.":::


## <a name="next-steps"></a>Nästa steg

- [Ta bort en app](delete-application-portal.md)
