---
title: Autentisering med hjälp av Azure Active Directory i nationella moln
description: Läs mer om app-registrering och autentisering slutpunkter för nationella moln.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d4586df23548854f4acbfefd32081a36906097
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067908"
---
# <a name="national-clouds"></a>Nationella moln

Nationella moln är fysiskt isolerat instanser av Azure. Dessa områden av Azure är utformade för att se till att dataplacering och landsbaserad placering efterlevnad datakrav respekteras inom geografiska gränser.

Azure Active Directory har distribuerats i följande nationella moln, inklusive globala molnet:  

- Azure för amerikanska myndigheter
- Azure Tyskland
- Azure Kina 21Vianet

Nationella moln är unikt och annan miljö än Azure-global. Det är därför viktigt att känna till några huvudsakliga skillnader när du utvecklar ditt program för dessa miljöer som registrerar program, hämta token och konfigurera slutpunkter.

## <a name="app-registration-endpoints"></a>App-slutpunkter för registrering

Det finns en separat Azure-portalen för var och en av de nationella moln. Om du vill integrera med Microsoft Identity-plattformen i ett nationella moln, behöver du registrera ditt program separat i var och en av Azure portal som är specifika för miljön.

I följande tabell visas de grundläggande URL: er för Azure Active Directory (Azure AD)-slutpunkter som används för att registrera ett program för varje nationella moln.

| Nationella moln | Azure AD-portalen slutpunkt |
|----------------|--------------------------|
| Azure AD för amerikanska myndigheter | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| Azure AD-Kina som drivs av 21Vianet | `https://portal.azure.cn` |
| Azure AD (tjänsten för global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-autentisering-slutpunkter

Nationella moln autentisera användare separat i varje miljö och har separat autentiseringsslutpunkter.

I följande tabell visas de grundläggande URL: er för Azure Active Directory (Azure AD)-slutpunkter som används för att hämta token för varje nationella moln.

| Nationella moln | Azure AD-autentisering-slutpunkt |
|----------------|-------------------------|
| Azure AD för amerikanska myndigheter | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| Azure AD-Kina som drivs av 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (tjänsten för global)| `https://login.microsoftonline.com` |

- Begäranden till Azure AD-slutpunkter för auktorisering eller token kan skapas med hjälp av lämplig regionspecifika grundläggande Webbadress. Till exempel för Azure Germany:

  - Vanliga auktoriseringsslutpunkt är `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Vanliga tokenslutpunkten är `https://login.microsoftonline.de/common/oauth2/token`.

- För enstaka klientorganisationer program, ersätter vanligt i de föregående URL: er med din klient-ID eller namn, till exempel `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Den [Azure AD v2.0-auktorisering]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) och token slutpunkter är bara tillgängliga för tjänsten för global. Det stöds inte ännu för distribution i nationella moln.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Lär dig hur du anropar Microsoft Graph API: er i nationell molnmiljö genom att gå till [Microsoft Graph i nationella moln](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Vissa tjänster och funktioner som finns i vissa områden av tjänsten för global kanske inte tillgänglig i alla nationella moln. Att ta reda på vilka tjänster som är tillgängliga går du till [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Följ den här [Microsoft Authentication Library (MSAL) självstudien](msal-national-cloud.md) att lära dig hur du skapar ett program med Microsoft identity-plattformen. Mer specifikt den här appen ska logga in en användare få en åtkomsttoken att anropa Microsoft Graph API.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Grunder för Azure AD](authentication-scenarios.md)
