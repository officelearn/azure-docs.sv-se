---
title: Lösa aviseringar för nätverkssäkerhetsgrupper i Azure AD DS | Microsoft-dokument
description: Lär dig hur du felsöker och löser konfigurationsaviseringar för nätverkssäkerhetsgrupper för Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257991"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Kända problem: Nätverkskonfigurationsaviseringar i Azure Active Directory Domain Services

Om du vill att program och tjänster ska kommunicera korrekt med Azure Active Directory Domain Services (Azure AD DS) måste specifika nätverksportar vara öppna för att trafik ska kunna flöda. I Azure styr du trafikflödet med hjälp av nätverkssäkerhetsgrupper. Hälsostatusen för en Azure AD DS-hanterad domän visar en avisering om de nödvändiga nätverkssäkerhetsgruppreglerna inte finns på plats.

Den här artikeln hjälper dig att förstå och lösa vanliga aviseringar för konfigurationsproblem för nätverkssäkerhetsgrupper.

## <a name="alert-aadds104-network-error"></a>Avisering AADDS104: Nätverksfel

### <a name="alert-message"></a>Varningsmeddelande

*Microsoft kan inte nå domänkontrollanterna för den här hanterade domänen. Detta kan inträffa om en nätverkssäkerhetsgrupp (NSG) som konfigurerats på det virtuella nätverket blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är om det finns en användardefinierad väg som blockerar inkommande trafik från Internet.*

Ogiltiga regler för nätverkssäkerhetsgrupper är den vanligaste orsaken till nätverksfel för Azure AD DS. Nätverkssäkerhetsgruppen för det virtuella nätverket måste tillåta åtkomst till specifika portar och protokoll. Om dessa portar är blockerade kan Azure-plattformen inte övervaka eller uppdatera den hanterade domänen. Synkroniseringen mellan Azure AD-katalogen och Azure AD DS-hanterad domän påverkas också. Se till att du håller standardportarna öppna för att undvika avbrott i drift.

## <a name="default-security-rules"></a>Standardsäkerhetsregler

Följande standardregler för inkommande och utgående säkerhetsregler tillämpas på nätverkssäkerhetsgruppen för en Azure AD DS-hanterad domän. Dessa regler håller Azure AD DS säkert och gör det möjligt för Azure-plattformen att övervaka, hantera och uppdatera den hanterade domänen. Du kan också ha ytterligare en regel som tillåter inkommande trafik om du [konfigurerar säker LDAP][configure-ldaps].

### <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| Prioritet | Namn | Port | Protokoll | Källa | Mål | Åtgärd |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Alla | Tillåt |
| 201      | AllowRD (Tillåt) | 3389 | TCP | CorpNetSaw (2000/90 | Alla | Tillåt |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Alla | Tillåt |
| 65000    | AllVnetInBound | Alla | Alla | VirtualNetwork | VirtualNetwork | Tillåt |
| 65001    | AllowAzureLoadBalancerInBound | Alla | Alla | AzureLoadBalancer | Alla | Tillåt |
| 65500    | DenyAllInBound | Alla | Alla | Alla | Alla | Neka |

### <a name="outbound-security-rules"></a>Säkerhetsregler för utgående trafik

| Prioritet | Namn | Port | Protokoll | Källa | Mål | Åtgärd |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Alla | Alla | VirtualNetwork | VirtualNetwork | Tillåt |
| 65001    | TillåtAzureLoadBalancerOutBound | Alla | Alla |  Alla | Internet | Tillåt |
| 65500    | DenyAllOutBound | Alla | Alla | Alla | Alla | Neka |

>[!NOTE]
> Azure AD DS behöver obegränsad utgående åtkomst från det virtuella nätverket. Vi rekommenderar inte att du skapar några ytterligare regler som begränsar utgående åtkomst för det virtuella nätverket.

## <a name="verify-and-edit-existing-security-rules"></a>Verifiera och redigera befintliga säkerhetsregler

Så här verifierar du de befintliga säkerhetsreglerna och kontrollerar att standardportarna är öppna:

1. Sök efter och välj **Nätverkssäkerhetsgrupper**i Azure-portalen .
1. Välj den nätverkssäkerhetsgrupp som är associerad med den hanterade domänen, till exempel *AADDS-contoso.com-NSG*.
1. På sidan **Översikt** visas de befintliga inkommande och utgående säkerhetsreglerna.

    Granska reglerna för inkommande och utgående och utgående och jämför med listan över obligatoriska regler i föregående avsnitt. Om det behövs markerar du och tar sedan bort alla anpassade regler som blockerar nödvändig trafik. Om någon av de nödvändiga reglerna saknas lägger du till en regel i nästa avsnitt.

    När du har lagt till eller tagit bort regler som tillåter den nödvändiga trafiken uppdateras azure AD DS-hanterade domänens hälsa automatiskt inom två timmar och tar bort aviseringen.

### <a name="add-a-security-rule"></a>Lägg till en säkerhetsregel

Så här lägger du till en säkerhetsregel som saknas:

1. Sök efter och välj **Nätverkssäkerhetsgrupper**i Azure-portalen .
1. Välj den nätverkssäkerhetsgrupp som är associerad med den hanterade domänen, till exempel *AADDS-contoso.com-NSG*.
1. Under **Inställningar** på den vänstra panelen klickar du på *Inkommande säkerhetsregler* eller *Utgående säkerhetsregler* beroende på vilken regel du behöver lägga till.
1. Välj **Lägg till**och skapa sedan den regel som krävs baserat på porten, protokollet, riktningen osv. När du är klar väljer du **OK**.

Det tar en stund innan säkerhetsregeln läggs till och visas i listan.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
