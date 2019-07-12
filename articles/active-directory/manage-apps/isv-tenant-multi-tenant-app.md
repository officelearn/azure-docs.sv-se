---
title: Skapa en Azure-klient för ett program med flera innehavare
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
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659585"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Skapa en Azure-klient för ett program med flera innehavare  

Om du vill ge åtkomst till dina program med flera innehavare måste du skapa en Azure Active Directory-klient för att registrera programmet och aktivera federation kundernas identiteter. Se [välja rätt federation-protokollet för ditt program för flera innehavare](isv-choose-multi-tenant-federation.md). Den här klienten kan du testa ditt program och federationen i en miljö som liknar dina kunder Azure AD-miljöer.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Kostnaderna för som är värd för ett program med flera innehavare

Azure Active Directory är tillgängligt i tre SKU: er, kostnadsfri, Basic och Premium. [Se detaljerad funktionsjämförelse](https://azure.microsoft.com/pricing/details/active-directory/).

Du kan skapa din Azure-prenumeration och Azure active directory utan kostnad och använda grundläggande funktioner.

## <a name="create-your-tenant"></a>Skapa din klient

1. Skapa din klient. Se [konfigurera en utvecklingsmiljö](../develop/quickstart-create-new-tenant.md).

2. Aktivera och testa enkel inloggning i ditt program

   a. **För program med OIDC eller Oath**, [registrera ditt program](../develop/quickstart-register-app.md) som ett program för flera innehavare. Välj konton som alla organisationskatalog och personliga Microsoft-konton alternativ i stöds kontotyper

   b. **För SAML - och WS-Fed-baserade program**, du [konfigurera SAML-baserad enkel inloggning](configure-single-sign-on-non-gallery-applications.md) program med hjälp av en allmän SAML-mall i Azure AD.

Du kan också [konvertera en enda klient-programmet till flera innehavare](../develop/howto-convert-app-to-be-multi-tenant.md) om det behövs.

## <a name="next-steps"></a>Nästa steg

[Integrera enkel inloggning i ditt program](isv-sso-content.md)
