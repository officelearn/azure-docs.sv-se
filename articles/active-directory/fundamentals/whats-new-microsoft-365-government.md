---
title: Nyheter för Azure AD i Microsoft 365 Government? | Microsoft Docs
description: Lär dig mer om några ändringar i Azure Active Directory (Azure AD) i Microsoft 365 Government-molninstansen, vilket kan påverka dig.
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425552"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Nyheter för Azure Active Directory i Microsoft 365 Government

Vi har gjort vissa ändringar i Azure Active Directory (Azure AD) i Microsoft 365 Government-molninstansen, som gäller för kunder som använder följande tjänster:

- Microsoft Azure-regering

- Microsoft 365 Regeringen – GCC Hög

- Microsoft 365 Regeringen – DoD

Den här artikeln gäller inte Microsoft 365 Government – GCC-kunder.

## <a name="changes-to-the-initial-domain-name"></a>Ändringar i det ursprungliga domännamnet

Under organisationens första registrering av en Microsoft 365 Government online-tjänst ombads du att `<your-domain-name>.onmicrosoft.com`välja organisationens domännamn. Om du redan har ett domännamn med suffixet .com ändras ingenting.

Men om du registrerar dig för en ny Microsoft 365 Government-tjänst blir du `.us` ombedd att välja ett domännamn med suffixet. Så kommer det `<your-domain-name>.onmicrosoft.us`att bli .

>[!Note]
>Den här ändringen gäller inte för alla kunder som hanteras av molntjänstleverantörer.

## <a name="changes-to-portal-access"></a>Ändringar i portalåtkomst

Vi har uppdaterat portalslutpunkterna för Microsoft Azure Government, Microsoft 365 Government – GCC High och Microsoft 365 Government – DoD, som visas i [mappningstabellen för slutpunkter](#endpoint-mapping).

Tidigare kunder kunde logga in med hjälp av de globala Azure-portalerna (portal.azure.com) och Office 365 (portal.office.com). Med den här uppdateringen måste kunder nu logga in med de specifika Microsoft Azure Government-, Microsoft 365 Government - GCC High och Microsoft 365 Government - DoD-portalerna.

## <a name="endpoint-mapping"></a>Mappning av slutpunkt

I följande tabell visas slutpunkterna för alla kunder:

| Namn | Information om slutpunkt |
|------|------------------|
| Portaler |Microsoft Azure-myndighet:https://portal.azure.us<p>Microsoft 365 Regeringen – GCC Hög:https://portal.office365.us<p>Microsoft 365 Regeringen – DoD:https://portal.apps.mil |
| Slutpunkt för Azure Active Directory Authority | https://login.microsoftonline.us |
| Microsoft Graph API för Microsoft 365 Government - GCC High | https://graph.microsoft.us |
| Microsoft Graph API för Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Slutpunkter för Azure Government-tjänster | Mer information finns i [utvecklarhandboken för Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government - GCC Höga slutpunkter | Mer information finns i [Office 365 U.S. Government GCC High endpoints](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Regeringen - DoD | Mer information finns i [Office 365 U.S. Government DoD-slutpunkter](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Nästa steg

Mer information finns i dessa artiklar:

- [Vad är Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Slutpunktsuppdatering för Azure Government AAD-myndighet](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph-slutpunkter i moln för amerikanska myndigheter](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 AMERIKANSKA regeringen GCC Hög och DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
