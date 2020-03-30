---
title: Azure AD-autentisering & nationella moln | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om slutpunkter för appregistrering och autentisering för nationella moln.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263002"
---
# <a name="national-clouds"></a>Nationella moln

Nationella moln är fysiskt isolerade instanser av Azure. Dessa regioner i Azure är utformade för att se till att datahemvist, suveränitet och efterlevnadskrav uppfylls inom geografiska gränser.

Inklusive det globala molnet distribueras Azure Active Directory (Azure AD) i följande nationella moln:  

- Azure Government
- Azure Tyskland
- Azure Kina 21Vianet

Nationella moln är unika och en separat miljö från Azure global. Det är viktigt att vara medveten om viktiga skillnader samtidigt som du utvecklar ditt program för dessa miljöer. Skillnaderna omfattar registrering av program, inanskaffning av token och konfigurera slutpunkter.

## <a name="app-registration-endpoints"></a>Slutpunkter för appregistrering

Det finns en separat Azure-portal för var och en av de nationella molnen. Om du vill integrera program med Microsofts identitetsplattform i ett nationellt moln måste du registrera ditt program separat i varje Azure-portal som är specifik för miljön.

I följande tabell visas bas-URL:er för Azure AD-slutpunkter som används för att registrera ett program för varje nationellt moln.

| Nationellt moln | Slutpunkt för Azure AD-portal |
|----------------|--------------------------|
| Azure AD för amerikanska myndigheter | `https://portal.azure.us` |
| Azure AD Tyskland | `https://portal.microsoftazure.de` |
| Azure AD Kina drivs av 21Vianet | `https://portal.azure.cn` |
| Azure AD (global tjänst) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Slutpunkter för Azure AD-autentisering

Alla nationella moln autentiserar användare separat i varje miljö och har separata autentiseringsslutpunkter.

I följande tabell visas bas-URL:er för Azure AD-slutpunkter som används för att hämta token för varje nationellt moln.

| Nationellt moln | Slutpunkt för Azure AD-autentisering |
|----------------|-------------------------|
| Azure AD för amerikanska myndigheter | `https://login.microsoftonline.us` |
| Azure AD Tyskland| `https://login.microsoftonline.de` |
| Azure AD Kina drivs av 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (global tjänst)| `https://login.microsoftonline.com` |

Du kan skapa begäranden till Azure AD-auktoriserings- eller tokenslutpunkterna med hjälp av lämplig regionspecifik bas-URL. Till exempel för Azure Tyskland:

  - Gemensam slutpunkt `https://login.microsoftonline.de/common/oauth2/authorize`för auktorisering är .
  - Token gemensam slutpunkt är `https://login.microsoftonline.de/common/oauth2/token`.

För program med en klient ska du ersätta "vanliga" i tidigare webbadresser med ditt klient-ID eller namn. Ett exempel är `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Om du vill veta hur du anropar Microsoft Graph API:er i en nationell molnmiljö går du till [Microsoft Graph i nationella molndistributioner](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Vissa tjänster och funktioner som finns i specifika regioner i den globala tjänsten kanske inte är tillgängliga i alla nationella moln. Om du vill ta reda på vilka tjänster som är tillgängliga går du till [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Om du vill veta hur du skapar ett program med hjälp av Microsoft identity-plattformen följer du [självstudien för Microsoft Authentication Library (MSAL).](msal-national-cloud.md) Den här appen loggar in en användare och får en åtkomsttoken för att anropa Microsoft Graph API.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure Kina 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Tyskland](https://docs.microsoft.com/azure/germany/)
- [Grunderna i Azure AD-autentisering](authentication-scenarios.md)
