---
title: Konfigurera användar medgivande till ett program – Azure Active Directory | Microsoft Docs
description: Lär dig hur du hanterar användar tillstånd för program behörigheter. Du kan förenkla användar upplevelsen genom att bevilja administratörs tillåtelse. Dessa metoder gäller för alla slutanvändare i din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bd746e79bc9d70be23771f97b1757f090f6375f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709275"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Konfigurera hur slutanvändare godkänner ett program i Azure Active Directory
Lär dig hur du konfigurerar hur användare godkänner program behörigheter. Du kan förenkla användar upplevelsen genom att bevilja administratörs tillåtelse. Den här artikeln innehåller olika metoder som du kan använda för att konfigurera användar medgivande. Metoderna gäller för alla slutanvändare i din Azure Active Directory (Azure AD)-klient. 

Mer information om samtycker till program finns i [Azure Active Directory medgivande Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Förutsättningar

Att bevilja administratörs tillåtelse kräver att du loggar in som global administratör, en program administratör eller en moln program administratör.

Om du vill begränsa åtkomsten till program måste du kräva användar tilldelning och sedan tilldela användare eller grupper till programmet.  Mer information finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Bevilja administrativt medgivande till företags program i Azure Portal

Bevilja administrativt medgivande till en företags app:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, program administratör eller en moln program administratör.
2. Klicka på **alla tjänster** överst i den vänstra navigerings menyn. **Azure Active Directory-tillägget** öppnas.
3. Skriv **"Azure Active Directory"** i rutan Filter sökning och välj **Azure Active Directory** objektet.
4. I navigerings menyn klickar du på **företags program**.
5. Välj appen för medgivande.
6. Välj **behörigheter** och klicka sedan på **bevilja administrativt medgivande**. Du uppmanas att logga in för att administrera programmet.
7. Logga in med ett konto som har behörighet att bevilja administratörs medgivande för programmet. 
8. Medgivande till program behörigheter.

Det här alternativet fungerar bara om programmet är: 

- Registrerad i din klient eller
- Registreras i en annan Azure AD-klient och skickas av minst en slutanvändare. När en användare har samtyckt till ett program, visar Azure AD programmet under **företags program** i Azure Portal.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Bevilja administrativt medgivande vid registrering av en app i Azure Portal

Bevilja administrativt medgivande vid registrering av en app: 

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Gå till bladet **program registreringar** .
3. Välj program för medgivande.
4. Välj **API-behörigheter**.
5. Klicka på **bevilja administratörs medgivande**.


## <a name="grant-admin-consent-through-a-url-request"></a>Bevilja administrativt medgivande via en URL-begäran

Bevilja administrativt medgivande via en URL-begäran:

1. Skapa en begäran till *login.microsoftonline.com* med dina appdata och Lägg till på `&prompt=admin_consent`. URL: en kommer att se ut så här: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. När du har loggat in med administratörs behörighet har appen beviljats medgivande för alla användare.


## <a name="force-user-consent-through-a-url-request"></a>Tvinga användar medgivande via en URL-begäran

För att användarna ska kunna godkänna ett program varje gången de autentiserar lägger du till `&prompt=consent` till URL: en för autentiseringsbegäran.
URL: en kommer att se ut så här: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Nästa steg

[Samtycke och integrering av appar till AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Medgivande och behörighet för AzureAD v 2.0 konvergerade appar](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
