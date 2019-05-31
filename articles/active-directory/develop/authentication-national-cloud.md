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
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235479"
---
# <a name="national-clouds"></a>Nationella moln

Nationella moln är fysiskt isolerat instanser av Azure. Dessa områden av Azure är utformade för att se till att dataplacering och landsbaserad placering efterlevnad datakrav respekteras inom geografiska gränser.

Azure Active Directory (Azure AD) har distribuerats i följande nationella moln, inklusive det globala molnet:  

- Azure Government
- Azure Tyskland
- Azure Kina 21Vianet

Nationella moln är unika och en separat miljö från Azure-global. Det är viktigt att känna till viktiga skillnader när du utvecklar ditt program för dessa miljöer. Skillnader är registrerar program, hämta token och konfigurera slutpunkter.

## <a name="app-registration-endpoints"></a>App-slutpunkter för registrering

Det finns en separat Azure-portalen för var och en av de nationella moln. Om du vill integrera med Microsoft identity-plattformen i ett nationella moln, måste du registrera ditt program separat på varje Azure-portalen som är specifika för miljön.

I följande tabell visas de grundläggande URL: er för Azure AD-slutpunkter som används för att registrera ett program för varje nationella moln.

| Nationella moln | Azure AD-portalen slutpunkt |
|----------------|--------------------------|
| Azure AD för amerikanska myndigheter | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| Azure AD-Kina som drivs av 21Vianet | `https://portal.azure.cn` |
| Azure AD (tjänsten för global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-autentisering-slutpunkter

Nationella moln autentisera användare separat i varje miljö och har separat autentiseringsslutpunkter.

I följande tabell visas de grundläggande URL: er för Azure AD-slutpunkter som används för att hämta token för varje nationella moln.

| Nationella moln | Azure AD-autentisering-slutpunkt |
|----------------|-------------------------|
| Azure AD för amerikanska myndigheter | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| Azure AD-Kina som drivs av 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (tjänsten för global)| `https://login.microsoftonline.com` |

Med hjälp av lämplig regionspecifika grundläggande URL: en kan du skapa förfrågningar till Azure AD-auktorisering eller token slutpunkter. Till exempel för Azure Germany:

  - Vanliga auktoriseringsslutpunkt är `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Vanliga tokenslutpunkten är `https://login.microsoftonline.de/common/oauth2/token`.

Ersätt ”vanliga” i de föregående URL: er med din klient-ID eller namn för enstaka klientorganisationer program. Ett exempel är `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Den [Azure AD v2.0-auktorisering]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) och token slutpunkter är endast tillgängliga för tjänsten för global. De har inte stöd för distribution i nationella moln.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Om du vill lära dig mer om att anropa Microsoft Graph API: er i en nationell molnmiljö, gå till [Microsoft Graph i nationella molndistributioner](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Vissa tjänster och funktioner som finns i vissa områden av tjänsten för global kanske inte tillgänglig i alla nationella moln. Om du vill ta reda på vilka tjänster som är tillgängliga kan du gå till [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Information om hur du skapar en App med hjälp av Microsoft identity-plattformen, följer du de [Microsoft Authentication Library (MSAL) självstudien](msal-national-cloud.md). Den här appen kommer mer specifikt kan logga in en användare och få en åtkomsttoken att anropa Microsoft Graph API.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)
- [Grunder för Azure AD](authentication-scenarios.md)
