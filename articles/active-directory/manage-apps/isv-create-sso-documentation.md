---
title: Skapa & publicera en enda inloggningsdokumentation för ditt program
description: Vägledning för oberoende programvaruleverantörer om integrering med Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232272"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Skapa och publicera enkel inloggningsdokumentation för ditt program   

## <a name="documentation-on-your-site"></a>Dokumentation på din webbplats

Enkel användning är en viktig faktor i företagets programvara beslut. Tydlig lättsam dokumentation hjälper dina kunder i deras användningsresa och minskar supportkostnaderna. Arbeta med tusentals programvaruleverantörer, microsoft har sett vad som fungerar.

Vi rekommenderar att din dokumentation på din webbplats åtminstone innehåller följande objekt.

* Introduktion till din SSO-funktionalitet

  * Protokoll som stöds

  * Version och SKU

  * Lista över identitetsleverantörer som stöds med dokumentationslänkar

* Licensinformation för ditt program

* Rollbaserad åtkomstkontroll för att konfigurera SSO

* SSO-konfigurationssteg

  * Gränssnittskonfigurationselement för SAML med förväntade värden från providern

  * Information från tjänsteleverantören som ska skickas till identitetsleverantörer

* Om OIDC/OAuth

  * Lista över behörigheter som krävs för samtycke med affärsmotiveringar

* Testa steg för pilotanvändare

* Felsökningsinformation, inklusive felkoder och meddelanden

* Stödmekanismer för kunder

## <a name="documentation-on-the-microsoft-site"></a>Dokumentation på Microsoft-webbplatsen

När du listar ditt program med Azure Active Directory Application Gallery, som också publicerar ditt program på Azure Marketplace, genererar Microsoft dokumentation för våra gemensamma kunder som förklarar steg-för-steg-processen. Du kan se ett exempel [här](https://aka.ms/appstutorial). Den här dokumentationen skapas baserat på din inlämning till galleriet och du kan enkelt uppdatera den om du gör ändringar i ditt program med ditt GitHub-konto.

## <a name="next-steps"></a>Efterföljande moment

[Lista ditt program i Azure AD Application Gallery](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)