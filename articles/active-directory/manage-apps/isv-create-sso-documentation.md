---
title: Skapa och publicera enkel inloggning-dokumentationen för ditt program
description: Vägledning för oberoende programleverantörer för integrering med Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53221343ac606b6076cc9cc3cff6e0f96c1a3ac3
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659598"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Skapa och publicera enkel inloggning-dokumentationen för ditt program   

## <a name="documentation-on-your-site"></a>Dokumentation om din webbplats

Enkel införande är en viktig faktor i enterprise software beslut. Rensa enkel-till-Följ dokumentationen har stöd för dina kunder i sina övergångsprocessen och minskar kostnaderna för support. Arbeta med tusentals programvaruleverantörer, har Microsoft sett vad som fungerar.

Vi rekommenderar att dokumentationen på webbplatsen minst innehåller följande objekt.

* Introduktion till SSO-funktioner

  * Protokoll som stöds

  * SKU: er och version

  * Identitetsproviders-lista som stöds med länkar för dokumentation

* Mer information om licenser för ditt program

* Rollbaserad åtkomstkontroll för att konfigurera enkel inloggning

* Konfigurationssteg för enkel inloggning

  * UI-konfigurationselement för SAML med förväntade värden från providern

  * Information om som ska skickas till identitetsleverantörer

* Om OIDC/OAuth

  * Lista över behörigheter som krävs för godkännande med företag berättiganden

* Testa stegen för pilotanvändare

* Felsökning av information, inklusive felkoder och meddelanden

* Stöder mekanismer för kunder

## <a name="documentation-on-the-microsoft-site"></a>Dokumentation om Microsoft-webbplats

När du listar ditt program med Azure Active Directory Application galleriet, som publicerar även ditt program på Azure Marketplace, skapar Microsoft dokumentation för våra gemensamma kunder som förklarar steg. Du kan se ett exempel [här](https://aka.ms/appstutorial). Den här dokumentationen skapas baserat på ditt bidrag i galleriet och är enkelt att uppdatera den om du gör ändringar i ditt program med ditt GitHub-konto.

## <a name="next-steps"></a>Nästa steg

[Lista ditt program i Azure AD-Programgalleriet](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)