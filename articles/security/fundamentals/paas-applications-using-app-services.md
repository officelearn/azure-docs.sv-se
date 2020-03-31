---
title: Säkra PaaS-webb & mobila applikationer
titleSuffix: Azure App Service
description: 'Lär dig mer om metodtips för azure app-tjänst för att skydda dina PaaS-webb- och mobilappar. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: c3f3c7fbaa043a03b70ab770c06e493716c70daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500282"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Metodtips för att skydda PaaS webb- och mobilappar med Azure App Service

I den här artikeln diskuterar vi en samling av [Azure App Service-säkerhetspraxis](/azure/app-service/overview) för att skydda dina PaaS-webb- och mobilappar. Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som du själv.

Azure App Service är ett PaaS-erbjudande (Platform-as-a-Service) som gör att du kan skapa webb- och mobilappar för alla plattformar eller enheter och ansluta till data var som helst, i molnet eller lokalt. App-tjänsten innehåller de webb- och mobilfunktioner som tidigare levererades separat som Azure-webbplatser och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. Som en enda integrerad tjänst ger App Service en omfattande uppsättning funktioner till webb-, mobil- och integrationsscenarier.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autentisera via Azure Active Directory (AD)
App Service tillhandahåller en OAuth 2.0-tjänst för din identitetsleverantör. OAuth 2.0 fokuserar på enkelhet för klientutvecklare samtidigt som specifika auktoriseringsflöden för webbapplikationer, skrivbordsprogram och mobiltelefoner. Azure AD använder OAuth 2.0 för att du ska kunna auktorisera åtkomst till mobila program och webbprogram. Mer information finns [i Autentisering och auktorisering i Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Begränsa åtkomst baserat på roll
Att begränsa åtkomsten är absolut nödvändigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Du kan använda rollbaserad åtkomstkontroll (RBAC) för att tilldela behörigheter till användare, grupper och program med ett visst omfång, till exempel behovet av att känna till och minst privilegier säkerhetsprinciper. Mer information om hur du ger användare åtkomst till program finns i [Vad är rollbaserad åtkomstkontroll](/azure/role-based-access-control/overview).

## <a name="protect-your-keys"></a>Skydda dina nycklar
Det spelar ingen roll hur bra din säkerhet är om du förlorar dina prenumerationsnycklar. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Med Key Vault kan du kryptera nycklar och hemligheter (t.ex. autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Du kan också använda Key Vault för att hantera dina TLS-certifikat med automatisk förnyelse. Läs [vad är Azure Key Vault](../../key-vault/key-vault-overview.md) om du vill veta mer.

## <a name="restrict-incoming-source-ip-addresses"></a>Begränsa inkommande käll-IP-adresser
App Service-miljöer har en virtuell [nätverksintegreringsfunktion](../../app-service/environment/intro.md) som hjälper dig att begränsa inkommande käll-IP-adresser via nätverkssäkerhetsgrupper (NSG). Om du inte känner till Azure Virtual Networks (VNETs) är detta en funktion som gör att du kan placera många av dina Azure-resurser i ett icke-internet, dirigerbart nätverk som du styr åtkomsten till. Mer information finns i [Integrera din app med ett Virtuellt Azure-nätverk](../../app-service/web-sites-integrate-with-vnet.md).

För App Service i Windows kan du också begränsa IP-adresser dynamiskt genom att konfigurera web.config. Mer information finns i [Dynamisk IP-säkerhet](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Nästa steg
I den här artikeln introducerades en samling metodtips för apptjänstsäkerhet för att skydda dina PaaS-webb- och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns i:

- [Skydda PaaS-distributioner](paas-deployments.md)
- [Skydda PaaS-databaser i Azure](paas-applications-using-sql.md)
