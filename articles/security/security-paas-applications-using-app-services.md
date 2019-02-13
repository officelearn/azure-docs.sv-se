---
title: Skydda PaaS-webb- och mobilprogram med Azure App Service | Microsoft Docs
description: 'Läs mer om Azure App Service-säkerhet metoder för att skydda din PaaS-webbprogram och mobilappar. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 6e5034d0ff8f14a9fc381f6fd1a214a91ad4d1ed
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107981"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Metoder för att skydda PaaS-webbprogram och mobilappar med Azure App Service

I den här artikeln diskuterar vi en samling [Azure App Service](../app-service/overview.md) rekommenderade säkerhetsmetoder för att skydda din PaaS-webbprogram och mobilappar. Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som dig själv.

Azure App Service är en platform-as-a-service (PaaS)-erbjudande som låter dig skapa webb- och mobilappar för valfri plattform eller enhet och ansluta till data överallt, i molnet eller lokalt. Apptjänst innehåller webb- och mobilfunktioner som tidigare har levererat separat som Azure Websites och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. En enda integrerad tjänst innehåller App Service en omfattande uppsättning funktioner för webb, mobil- och integrationsscenarier.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autentisera via Azure Active Directory (AD)
App Service tillhandahåller en OAuth 2.0-tjänst för din identitetsprovider. OAuth 2.0 fokuserar på klienten utvecklare enkelt få tillgång till specifika auktoriseringsflöden för webbprogram, program och mobiltelefoner. Azure AD använder OAuth 2.0 att auktorisera åtkomst till mobila och webbprogram. Mer information finns i [autentisering och auktorisering i Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Begränsa åtkomst baserat på roller 
Begränsa åtkomst är absolut nödvändigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Du kan använda rollbaserad åtkomstkontroll (RBAC) för att tilldela behörigheter till användare, grupper och program för ett visst omfång, till exempel att känna och minsta privilegium säkerhetsprinciper. Läs mer om att bevilja användare åtkomst till program i [vad är rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Skydda dina nycklar
Det spelar ingen roll hur bra din säkerhet är om du förlorar dina prenumerationsnycklar. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Med Key Vault kan kryptera du nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Du kan också använda Key Vault för att hantera din TLS-certifikat med automatisk förnyelse. Se [vad är Azure Key Vault](../key-vault/key-vault-whatis.md) vill veta mer. 

## <a name="restrict-incoming-source-ip-addresses"></a>Begränsa inkommande källans IP-adresser
[App Service-miljöer](../app-service/environment/intro.md) har en funktion för integrering av virtuellt nätverk som hjälper dig att begränsa inkommande källans IP-adresser via nätverkssäkerhetsgrupper (NSG). Om du inte känner till Azure-nätverk (Vnet) är en funktion som gör att du kan placera många av dina Azure-resurser i ett icke-internet, dirigerbara nätverk som du styr åtkomst till. Mer information finns i [integrera din app med Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Nästa steg
Den här artikeln har du introducerats till en samling av App Service rekommenderade säkerhetsmetoder för att skydda din PaaS-webbprogram och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Skydda PaaS-databaser i Azure](security-paas-applications-using-sql.md)
