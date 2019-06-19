---
title: Vad är nytt för Azure Active Directory i Microsoft 365 Government - Azure Active Directory | Microsoft Docs
description: Läs mer om några ändringar till Azure Active Directory (AD Azure) i Microsoft 365 Government cloud-instansen, som kan påverka dig.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258901"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Vad är nytt för Azure Active Directory i Microsoft 365 Government

Vi har gjort några ändringar i Azure Active Directory (AD Azure) i Microsoft 365 Government cloud-instansen, som gäller för kunder som använder följande tjänster:

- Microsoft Azure Government

- Microsoft 365 Government – GCC hög

- Microsoft 365 Government – DoD

Den här artikeln gäller inte för Microsoft 365 Government – GCC-kunder.

## <a name="changes-to-the-initial-domain-name"></a>Ändringar av det ursprungliga domännamnet

Under din organisations inledande registrera dig för en onlinetjänst för Microsoft 365 för myndigheter, har du ombedd att välja din organisations domännamn, `<your-domain-name>.onmicrosoft.com`. Om du redan har ett domännamn med suffixet .com ändras ingenting.

Men om du loggar för en ny Microsoft 365 Government-tjänst, får du en fråga att välja en domän namn med hjälp av den `.us` suffix. Därför blir `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Den här ändringen gäller inte för alla kunder som hanteras av molntjänst-providers (CSP).

## <a name="changes-to-portal-access"></a>Ändringar av portalåtkomst

Vi har uppdaterat slutpunkter i portalen för Microsoft Azure Government, Microsoft 365 Government – GCC hög, och Microsoft 365 Government – DoD, enligt den [tabellen med slutpunkter för mappning](#endpoint-mapping).

Tidigare kan kunder logga in med över hela världen Azure (portal.azure.com) och Office 365 (portal.office.com) portaler. Med den här uppdateringen måste kunder nu logga in med specifika Microsoft Azure Government, Microsoft 365 Government - GCC hög, och Microsoft 365 Government - DoD portaler.

## <a name="endpoint-mapping"></a>Mappning av slutpunkt

I följande tabell visas slutpunkterna för alla kunder:

| Namn | information om slutpunkten |
|------|------------------|
| Portaler |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government – GCC hög: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory Authority Endpoint | https://login.microsoftonline.us |
| Azure Active Directory Graph API | https://graph.windows.net |
| Microsoft Graph API för Microsoft 365 Government - GCC hög | https://graph.microsoft.us |
| Microsoft Graph API för Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Slutpunkter för Azure Government-tjänster | Mer information finns i [utvecklarguiden för Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government - GCC hög slutpunkter | Mer information finns i [Office 365-USA Government GCC hög slutpunkter](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Mer information finns i [Office 365-USA Government DoD slutpunkter](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Nästa steg

Mer information finns i dessa artiklar:

- [Vad är Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD-utfärdare Endpoint Update](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph-slutpunkterna i molnet för amerikanska myndigheter](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High och DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)