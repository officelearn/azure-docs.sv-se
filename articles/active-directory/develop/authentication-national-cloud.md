---
title: Autentisering med hjälp av Azure AD i nationella moln
description: Läs mer om app-registrering och autentisering slutpunkter för nationella moln.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982011"
---
# <a name="national-clouds"></a>Nationella moln

Nationella moln (även kallat suveräna moln) är fysiskt isolerat instanser av Azure. Dessa områden av Azure är utformade för att se till att dataplacering och landsbaserad placering efterlevnad datakrav respekteras inom geografiska gränser.

Azure Active Directory har distribuerats i följande nationella moln, inklusive globala molnet:  

- Azure för amerikanska myndigheter
- Azure Tyskland
- Azure Kina 21Vianet

## <a name="app-registration-endpoints"></a>App-slutpunkter för registrering

I följande tabell visas de grundläggande URL: er för Azure Active Directory (Azure AD)-slutpunkter som används för att registrera ett program för varje nationella moln.

| Nationella moln | Azure AD-portalen slutpunkt
| --- | --- |
| Azure AD för amerikanska myndigheter |https://portal.azure.us
|Azure AD-Tyskland |https://portal.microsoftazure.de
|Azure AD-Kina som drivs av 21Vianet |https://portal.azure.cn
|Azure AD (tjänsten för global)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-autentisering-slutpunkter

I följande tabell visas de grundläggande URL: er för Azure Active Directory (Azure AD)-slutpunkter som används för att hämta token för att anropa Microsoft Graph för varje nationella moln.

| Nationella moln | Azure AD-autentisering-slutpunkt
| --- | --- |
| Azure AD för amerikanska myndigheter |`https://login.microsoftonline.us`
|Azure AD-Tyskland| `https://login.microsoftonline.de`
|Azure AD-Kina som drivs av 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (tjänsten för global)|`https://login.microsoftonline.com`

Begäranden till Azure AD-slutpunkter för auktorisering eller token kan skapas med hjälp av lämplig regionspecifika grundläggande Webbadress. Till exempel när det gäller i Tyskland:

- Vanliga auktoriseringsslutpunkt är `https://login.microsoftonline.de/common/oauth2/authorize`
- Vanliga tokenslutpunkten är `https://login.microsoftonline.de/common/oauth2/token` 

För enstaka klientorganisationer program, Ersätt vanligt i ovanstående URL: er med din klient-id eller namn, till exempel `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> Den [Azure AD v2.0-auktorisering]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) och token slutpunkter är bara tillgängliga för tjänsten för global. Det stöds inte ännu för distribution i nationella moln.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- Läs mer om [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/)
- Läs mer om [Azure Tyskland](https://docs.microsoft.com/azure/germany/)
- Lär dig mer om den [grunderna för Azure AD-autentisering](authentication-scenarios.md)
- Läs mer om [Microsoft Graph-distribution i nationella moln](https://developer.microsoft.com/graph/docs/concepts/deployments)