---
title: Skapa & publicera enkel inloggnings dokumentation för ditt program
description: Vägledning för oberoende program varu leverantörer vid integrering med Azure Active Directory
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
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74232272"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Skapa och publicera enkel inloggnings dokumentation för ditt program   

## <a name="documentation-on-your-site"></a>Dokumentation på din webbplats

Ett enkelt sätt att införa är en betydande faktor i företags program varu beslut. Rensa användarvänlig dokumentation som stöder dina kunder vid deras införande av transporter och minskar support kostnaderna. Genom att arbeta med tusentals program varu leverantörer har Microsoft sett vad som fungerar.

Vi rekommenderar att dokumentationen på din webbplats minst omfattar följande objekt.

* Introduktion till dina SSO-funktioner

  * Protokoll som stöds

  * Version och SKU

  * Lista med identitets leverantörer som stöds med dokumentations länkar

* Licens information för ditt program

* Rollbaserad åtkomst kontroll för att konfigurera SSO

* Konfigurations steg för SSO

  * GRÄNSSNITTs konfigurations element för SAML med förväntade värden från providern

  * Information om service providern som ska skickas till identitets leverantörer

* Om OIDC/OAuth

  * Lista över behörigheter som krävs för godkännande med affärs skäl

* Test steg för pilot användare

* Fel söknings information, inklusive felkoder och meddelanden

* Support metoder för kunder

## <a name="documentation-on-the-microsoft-site"></a>Dokumentation på Microsofts webbplats

När du visar ditt program med Azure Active Directory program galleriet, som även publicerar ditt program på Azure Marketplace, kommer Microsoft att generera dokumentation för våra ömsesidiga kunder som förklarar steg för steg-processen. Du kan se ett exempel [här](https://aka.ms/appstutorial). Den här dokumentationen skapas baserat på ditt bidrag till galleriet och du kan enkelt uppdatera det om du gör ändringar i programmet med ditt GitHub-konto.

## <a name="next-steps"></a>Efterföljande moment

[Lista ditt program i Azure AD-programgalleriet](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)