---
title: Integrera lokala appar med Cloud App Security - Azure Active Directory | Microsoft Docs
description: Konfigurera ett lokalt program i Azure Active Directory för att arbeta med Microsoft Cloud App Security (MCAS). Använda MCAS-Appkontroll för villkorsstyrd åtkomst för att övervaka och kontrollera sessioner i realtid baserat på principer för villkorlig åtkomst. Du kan använda dessa principer på den lokala program som använder Application Proxy i Azure Active Directory (AD Azure).
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 33da0b09339460d66650c492ec125461bc0980b0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731620"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurera övervakning av realtidsprogram åtkomst med Microsoft Cloud App Security och Azure Active Directory
Konfigurera ett lokalt program i Azure Active Directory (AD Azure) att använda Microsoft Cloud App Security (MCAS) för övervakning i realtid. MCAS använder Appkontroll för villkorsstyrd åtkomst för att övervaka och kontrollera sessioner i realtid baserat på principer för villkorlig åtkomst. Du kan använda dessa principer på den lokala program som använder Application Proxy i Azure Active Directory (AD Azure).

Här följer några exempel på typer av principer som du kan skapa med MCAS:

- Blockera eller skydda hämtningen av känsliga dokument på ohanterade enheter.
- Övervakare för när med hög risk användare logga in på program och sedan loggar sina åtgärder från inom sessionen. Med den här informationen kan analysera du användarbeteende för att fastställa hur att tillämpa sessionsprinciper för.
- Använda klientcertifikat eller enhetsefterlevnad för att blockera åtkomst till specifika program från ohanterade enheter.
- Begränsa användarsessioner från icke-företagsnätverk. Du kan ge begränsad åtkomst till användare med åtkomst till ett program från utanför företagsnätverket. Till exempel kan begränsad åtkomst blockera användaren från att hämta känsliga dokument.

Mer information finns i [skydda appar med Microsoft Cloud App Security åtkomst till Appkontroll för villkorsstyrd](/cloud-app-security/proxy-intro-aad.md).

## <a name="requirements"></a>Krav

Licens:

- EMS E5-licens, eller 
- Azure Active Directory Premium P1 och MCAS fristående.

Lokalt program:

- Lokalt program måste använda Kerberos-begränsad delegering (KCD)

Konfigurera Application Proxy:

- Konfigurera Azure AD för att använda Application Proxy, inklusive förbereda din miljö och installera anslutningsappen för programproxyn. En självstudiekurs finns i [lägga till ett lokalt program för fjärråtkomst via programproxy i Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Lägg till dina lokala program till Azure AD

Lägg till ett lokalt program till Azure AD. En Snabbstart Se [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). När du lägger till programmet, måste du ange följande två inställningar den **Lägg till ditt lokala program** bladet:

- **Förautentisering**: Ange **Azure Active Directory**.
- **Översätt URL: er i brödtexten för programmet**: Välj **Ja**.

Dessa två inställningar krävs för att programmet ska fungera med MCAS.

## <a name="test-the-on-premises-application"></a>Testa programmet lokalt

När du lägger till ditt program till Azure AD, Följ stegen i [testa programmet](application-proxy-add-on-premises-application.md#test-the-application) att lägga till en användare för testning och testa inloggning. 

## <a name="deploy-conditional-access-app-control"></a>Distribuera Appkontroll för villkorsstyrd åtkomst

Om du vill konfigurera ditt program med programkontroll för villkorlig åtkomst, följer du anvisningarna i [distribuera villkorlig åtkomst till programkontroll för Azure AD-appar](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Test Appkontroll för villkorsstyrd åtkomst

Om du vill testa distributionen av Azure AD-program med villkorlig åtkomstkontroll för programmet, följer du anvisningarna i [Testa distributionen för Azure AD-appar](/cloud-app-security/proxy-deployment-aad#test-the-deployment).





