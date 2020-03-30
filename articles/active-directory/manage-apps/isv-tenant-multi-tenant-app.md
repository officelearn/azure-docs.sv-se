---
title: Skapa en Azure-klient för ett program med flera innehavare
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812609"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Skapa en Azure-klient för ett program med flera innehavare  

För att ge åtkomst till ditt program med flera innehavare måste du skapa en Azure Active Directory-klient för att registrera programmet och aktivera federationen av kundens identiteter. Se [Välja rätt federationsprotokoll för ditt program för flera innehavare](isv-choose-multi-tenant-federation.md). Den här klienten gör att du kan testa ditt program och federationen i en miljö som liknar dina kunder Azure AD-miljöer.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Kostnader för att vara värd för ett program med flera innehavare

Azure Active Directory är tillgängligt i flera versioner. [Se den detaljerade funktionen jämförelse](https://azure.microsoft.com/pricing/details/active-directory/).

Du kan skapa din Azure-prenumeration och Azure active directory gratis och använda grundläggande funktioner.

## <a name="create-your-tenant"></a>Skapa din klient

1. Skapa din klient. Se [Konfigurera en utvecklingsmiljö](../develop/quickstart-create-new-tenant.md).

2. Aktivera och testa enkel inloggning till ditt program,

   a. **För OIDC- eller Oath-program** [registrerar du din ansökan](../develop/quickstart-register-app.md) som ett program med flera innehavare. Välj alternativet Konton i en organisationskatalog och personliga Microsoft-konton i typer av konton som stöds

   b. **För SAML- och WS-Fed-baserade program**konfigurerar du [SAML-baserade enkel inloggningsprogram](configure-single-sign-on-non-gallery-applications.md) med hjälp av en allmän SAML-mall i Azure AD.

Du kan också [konvertera ett program med en klientorganisation till flera innehavare](../develop/howto-convert-app-to-be-multi-tenant.md) om det behövs.

## <a name="next-steps"></a>Efterföljande moment

[Integrera SSO i ditt program](isv-sso-content.md)
