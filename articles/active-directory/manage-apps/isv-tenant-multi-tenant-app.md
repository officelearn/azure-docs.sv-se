---
title: Skapa en Azure-klient för ett program med flera klienter
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812609"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Skapa en Azure-klient för ett program med flera klienter  

För att ge åtkomst till ditt program för flera innehavare måste du skapa en Azure Active Directory-klient för att registrera programmet och aktivera federationen av kundens identiteter. Se [välja rätt Federations protokoll för ditt program med flera klienter](isv-choose-multi-tenant-federation.md). Med den här klienten kan du testa ditt program och federationen i en miljö som liknar dina kunders Azure AD-miljöer.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Kostnader för att vara värd för ett program med flera klienter

Azure Active Directory finns i flera versioner. [Se en detaljerad funktions jämförelse](https://azure.microsoft.com/pricing/details/active-directory/).

Du kan skapa en Azure-prenumeration och Azure Active Directory kostnads fritt och använda grundläggande funktioner.

## <a name="create-your-tenant"></a>Skapa din klient

1. Skapa din klient. Se [Konfigurera en dev-miljö](../develop/quickstart-create-new-tenant.md).

2. Aktivera och testa enkel inloggning till ditt program

   a. [Registrera ditt program](../develop/quickstart-register-app.md) som ett program med flera innehavare **för OIDC-eller Oath-program**. Välj kontona i valfri organisations katalog och alternativet personliga Microsoft-konton i de konto typer som stöds

   b. **För SAML-och WS-utfodras-baserade program**konfigurerar du [SAML-baserade enkla inloggnings](configure-single-sign-on-non-gallery-applications.md) program med hjälp av en allmän SAML-mall i Azure AD.

Du kan också [konvertera ett program med en enda klient till flera klienter](../develop/howto-convert-app-to-be-multi-tenant.md) om det behövs.

## <a name="next-steps"></a>Nästa steg

[Integrera SSO i ditt program](isv-sso-content.md)
