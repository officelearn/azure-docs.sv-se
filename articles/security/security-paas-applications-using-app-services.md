---
title: Skydda PaaS webb- och mobila program med Azure App Service | Microsoft Docs
description: " Läs mer om Azure App Service Säkerhet Metodtips för att skydda din PaaS webb- och mobilprogram. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d2e606fe23a3a6eb9d2310b0932ccec8fcfc518c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415084"
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Att säkra PaaS-webb- och mobila program med Azure App Service

I den här artikeln tar vi upp en samling [Azure App Service](https://azure.microsoft.com/services/app-service/) säkerhetsmetoder för att skydda din PaaS webb- och mobilprogram. Följande rekommendationer härleds från våra erfarenhet av Azure och upplevelser för kunder som själv.

## <a name="azure-app-service"></a>Azure App Service
[Azure Apptjänst](../app-service/app-service-web-overview.md) är en PaaS erbjudandet kan du skapa webb- och mobilappar för alla plattformar och enheter och ansluta till data var som helst, i molnet eller lokalt. Apptjänst innehåller webb- och mobilfunktioner som har tidigare levererat separat som Azure Websites och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. Som en enda integrerad tjänst gör ger Apptjänst en omfattande uppsättning funktioner för webb, mobil- och integrationsscenarier.

## <a name="best-practices"></a>Bästa praxis

När du använder App Service kan du följa dessa rekommendationer:

- [Autentisera via Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). Apptjänst innehåller en OAuth 2.0 för din identitetsleverantör. OAuth 2.0 fokuserar på klienten developer enkelhet samtidigt som man tillstånd flöden för webbprogram, program och mobiltelefoner. Azure AD använder OAuth 2.0 att auktorisera åtkomst till mobil- och webbprogram.
- Begränsa åtkomst baserat på behöver känna till och minsta privilegium säkerhetsprinciper. Begränsa åtkomst är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Rollbaserad åtkomstkontroll (RBAC) kan användas för att tilldela behörigheter till användare, grupper och program för ett visst område. Läs mer om du vill bevilja användare åtkomst till program i [Kom igång med åtkomsthantering](../role-based-access-control/overview.md).
- Skydda dina nycklar. Det spelar ingen roll hur bra säkerheten är om du tappar bort din prenumeration nycklar. Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Med Key Vault kan du kryptera nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Se [Azure Key Vault](../key-vault/key-vault-whatis.md) vill veta mer. Du kan också använda Nyckelvalv för att hantera TLS-certifikat med automatisk förnyelse.
- Begränsa inkommande käll-IP-adresser. [Apptjänstmiljö](../app-service/environment/intro.md) har ett virtuellt nätverk integrationsfunktionen som hjälper dig att begränsa inkommande käll-IP-adresser via nätverkssäkerhetsgrupper (NSG: er). Om du inte känner till virtuella Azure-nätverk (Vnet) är en funktion som gör att du kan placera många av dina Azure-resurser i ett icke-internet, dirigerbara nätverk som du styr åtkomst till. Läs mer i [integrera din app med ett Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Nästa steg
Den här artikeln introduceras du till en samling av Apptjänst säkerhetsmetoder för att skydda din PaaS webb- och mobilprogram. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Att säkra PaaS-webb- och mobila program med Azure SQL Database och SQL Data Warehouse](security-paas-applications-using-sql.md)
