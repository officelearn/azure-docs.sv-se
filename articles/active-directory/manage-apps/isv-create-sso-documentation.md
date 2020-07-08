---
title: Skapa & publicera enkel inloggnings dokumentation för ditt program
description: Vägledning för oberoende program varu leverantörer vid integrering med Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c758e90548dd22b5abfb731f674f83dfbde9819
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955989"
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

## <a name="next-steps"></a>Nästa steg

[Lista ditt program i Azure AD-programgalleriet](https://docs.microsoft.com/Azure/active-directory/develop/howto-app-gallery-listing)
