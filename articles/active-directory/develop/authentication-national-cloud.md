---
title: Autentisering med hjälp av Azure AD i nationella moln
description: Läs mer om app-registrering och autentisering slutpunkter för nationella moln.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.openlocfilehash: dfca40c14ad0da3a3e3a8a32757ec40ace9acf6a
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200572"
---
# <a name="national-clouds"></a>Nationella moln

Nationella moln (även kallat suveräna moln) är fysiskt isolerat instanser av Azure. Dessa områden av Azure är utformade för att se till att dataplacering och landsbaserad placering efterlevnad datakrav respekteras inom geografiska gränser.

Azure Active Directory har distribuerats i följande nationella moln, inklusive globala molnet:  

- Azure för amerikanska myndigheter
- Azure Tyskland
- Azure Kina 21Vianet

Nationella moln är unikt och annan miljö än Azure-global. Det är därför viktigt att känna till några huvudsakliga skillnader när du utvecklar ditt program för dessa miljöer som registrerar program, hämta token och konfigurera slutpunkter.

## <a name="app-registration-endpoints"></a>App-slutpunkter för registrering

Det finns en separat Azure-portalen för var och en av de nationella moln. Om du vill integrera med Microsoft Identity-plattformen i ett nationella moln, behöver du registrera ditt program separat i var och en av Azure portal som är specifika för miljön.

I följande tabell visas de grundläggande URL: er för Azure Active Directory (Azure AD)-slutpunkter som används för att registrera ett program för varje nationella moln.

| Nationella moln | Azure AD-portalen slutpunkt
| --- | --- |
| Azure AD för amerikanska myndigheter |https://portal.azure.us
|Azure AD-Tyskland |https://portal.microsoftazure.de
|Azure AD-Kina som drivs av 21Vianet |https://portal.azure.cn
|Azure AD (tjänsten för global)|https://portal.azure.com 

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-autentisering-slutpunkter

Nationella moln autentisera användare separat i varje miljö och har separat autentiseringsslutpunkter.

I följande tabell visas de grundläggande URL: er för Azure Active Directory (Azure AD)-slutpunkter som används för att hämta token för varje nationella moln.

| Nationella moln | Azure AD-autentisering-slutpunkt
| --- | --- |
| Azure AD för amerikanska myndigheter |`https://login.microsoftonline.us`
|Azure AD-Tyskland| `https://login.microsoftonline.de`
|Azure AD-Kina som drivs av 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (tjänsten för global)|`https://login.microsoftonline.com`

- Begäranden till Azure AD-slutpunkter för auktorisering eller token kan skapas med hjälp av lämplig regionspecifika grundläggande Webbadress. Till exempel för Azure Germany:

  - Vanliga auktoriseringsslutpunkt är `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Vanliga tokenslutpunkten är `https://login.microsoftonline.de/common/oauth2/token`.

- För enstaka klientorganisationer program, ersätter vanligt i de föregående URL: er med din klient-ID eller namn, till exempel `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> Den [Azure AD v2.0-auktorisering]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) och token slutpunkter är bara tillgängliga för tjänsten för global. Det stöds inte ännu för distribution i nationella moln.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Lär dig hur du anropar Microsoft Graph API: er i nationell molnmiljö genom att gå till [Microsoft Graph i nationella moln](https://developer.microsoft.com/graph/docs/concepts/deployments).



>[!IMPORTANT]
Vissa tjänster och funktioner som finns i vissa områden av tjänsten för global kanske inte tillgänglig i alla nationella moln. Att ta reda på vilka tjänster som är tillgängliga går du till [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Government](https://docs.microsoft.com/azure/azure-government/).
- Läs mer om [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/).
- Läs mer om [Azure Germany](https://docs.microsoft.com/azure/germany/).
- Lär dig mer om den [grunder för Azure AD](authentication-scenarios.md).
