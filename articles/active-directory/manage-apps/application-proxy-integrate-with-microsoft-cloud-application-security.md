---
title: Integrera lokala appar med Cloud App Security – Azure AD
description: Konfigurera ett lokalt program i Azure Active Directory så att det fungerar med MICROSOFT Cloud App Security (MCAS). Använd MCAS-appkontrollen för villkorlig åtkomst för att övervaka och styra sessioner i realtid baserat på principer för villkorlig åtkomst. Du kan tillämpa dessa principer på lokala program som använder programproxy i Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275499"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurera övervakning av programåtkomst i realtid med Microsoft Cloud App Security och Azure Active Directory
Konfigurera ett lokalt program i Azure Active Directory (Azure AD) för att använda MICROSOFT Cloud App Security (MCAS) för övervakning i realtid. MCAS använder Appkontroll för villkorlig åtkomst för att övervaka och styra sessioner i realtid baserat på principer för villkorlig åtkomst. Du kan tillämpa dessa principer på lokala program som använder programproxy i Azure Active Directory (Azure AD).

Här är några exempel på de typer av principer som du kan skapa med MCAS:

- Blockera eller skydda hämtningen av känsliga dokument på ohanterade enheter.
- Övervaka när högriskanvändare loggar in på program och loggar sedan sina åtgärder inifrån sessionen. Med den här informationen kan du analysera användarbeteende för att avgöra hur du tillämpar sessionsprinciper.
- Använd klientcertifikat eller enhetsefterlevnad för att blockera åtkomst till specifika program från ohanterade enheter.
- Begränsa användarsessioner från icke-företagsnätverk. Du kan ge begränsad åtkomst till användare som använder ett program utanför företagets nätverk. Den begränsade åtkomsten kan till exempel blockera användaren från att hämta känsliga dokument.

Mer information finns i [Skydda appar med Microsoft Cloud App Security Conditional Access App Control](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Krav

Licens:

- EMS E5-licens, eller 
- Azure Active Directory Premium P1 och MCAS fristående.

Lokal ansökan:

- Det lokala programmet måste använda Kerberos Constrained Delegation (KCD)

Konfigurera programproxy:

- Konfigurera Azure AD för att använda programproxy, inklusive att förbereda din miljö och installera Application Proxy-anslutningen. En självstudiekurs finns i [Lägga till lokala program för fjärråtkomst via programproxy i Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Lägga till lokalt program i Azure AD

Lägg till ett lokalt program i Azure AD. En snabbstart finns i [Lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). När du lägger till programmet ska du ange följande två inställningar i **bladet Lägg till ditt lokala program:**

- **Före autentisering:** Ange **Azure Active Directory**.
- **Översätt webbadresser i programtexten**: Välj **Ja**.

Dessa två inställningar krävs för att programmet ska fungera med MCAS.

## <a name="test-the-on-premises-application"></a>Testa den lokala applikationen

När du har lagt till ditt program i Azure AD använder du stegen i [Testa programmet](application-proxy-add-on-premises-application.md#test-the-application) för att lägga till en användare för testning och testa inloggningen. 

## <a name="deploy-conditional-access-app-control"></a>Distribuera appkontroll för villkorlig åtkomst

Om du vill konfigurera ditt program med programkontroll för villkorlig åtkomst följer du instruktionerna i [Deploy Conditional Access Application Control för Azure AD-appar](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Programkontroll för testa villkorlig åtkomst

Om du vill testa distributionen av Azure AD-program med Programkontroll för villkorlig åtkomst följer du instruktionerna i [Testa distributionen för Azure AD-appar](/cloud-app-security/proxy-deployment-aad).





