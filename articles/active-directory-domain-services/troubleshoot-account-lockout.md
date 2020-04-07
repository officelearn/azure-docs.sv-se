---
title: Felsöka kontoutelåsning i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du felsöker vanliga problem som gör att användarkonton är utelåsta i Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/06/2020
ms.author: iainfou
ms.openlocfilehash: 7d2e22804c06f589c7990bf8f19319b897363a93
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743444"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Felsöka problem med kontoutelåsning med en hanterad Azure AD-domäntjänst

Azure AD DS låser konton efter ett definierat tröskelvärde för upprepade skadliga inloggningsförsök. Den här kontoutelåsningen kan också ske av misstag utan en inst. Om en användare till exempel upprepade gånger anger fel lösenord eller om en tjänst försöker använda ett gammalt lösenord blir kontot utelåst.

I den här felsökningsartikeln beskrivs varför kontoutelåsningar inträffar och hur du kan konfigurera beteendet och hur du granskar säkerhetsgranskningar för att felsöka utelåsningshändelser.

## <a name="what-is-an-account-lockout"></a>Vad är en kontoutelåsning?

Ett användarkonto i Azure AD DS är utelåst när ett definierat tröskelvärde för misslyckade inloggningsförsök har uppfyllts. Det här kontoutelåsningsbeteendet är utformat för att skydda dig från upprepade brute force-inloggningsförsök som kan tyda på en automatiserad digital attack.

**Om det finns 5 felaktiga lösenordsförsök på två minuter är kontot som standard utelåst i 30 minuter.**

Standardgränsen för kontoutelåsning konfigureras med hjälp av principen för detaljerade lösenord. Om du har en specifik uppsättning krav kan du åsidosätta dessa standardtrösklar för kontoutelåsning. Det rekommenderas dock inte att öka tröskelgränserna för att försöka minska antalet kontoutelåsningar. Felsöka källan till kontoutelåsningsbeteendet först.

### <a name="fine-grained-password-policy"></a>Finkornig lösenordsprincip

Med detaljerade lösenordsprinciper kan du tillämpa särskilda begränsningar för principer för lösenords- och kontoutelåsning på olika användare i en domän. FGPP påverkar bara användare inom en Azure AD DS-hanterad domän. Molnanvändare och domänanvändare som synkroniseras till Azure AD DS-hanterad domän från Azure AD påverkas endast av lösenordsprinciperna i Azure AD DS. Deras konton i Azure AD eller en lokal katalog påverkas inte.

Principer distribueras via gruppassociationen i azure AD DS-hanterade domänen och alla ändringar du gör tillämpas vid nästa användarloggning. Om du ändrar principen låses inte upp ett användarkonto som redan är utelåst.

Mer information om detaljerade lösenordsprinciper och skillnaderna mellan användare som skapats direkt i Azure AD DS jämfört med synkroniserade från Azure AD finns i [Konfigurera principer för lösenord och kontoutelåsning][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Vanliga skäl för kontoutelåsning

De vanligaste orsakerna till att ett konto ska låsas ute, utan skadliga avsikter eller faktorer, är följande scenarier:

* **Användaren låste sig ute.**
    * Har användaren, efter en nyligen ändrad lösenord, fortsatt att använda ett tidigare lösenord? Standardprincipen för kontoutelåsning för 5 misslyckade försök på 2 minuter kan orsakas av att användaren av misstag försöker ett gammalt lösenord igen.
* **Det finns ett program eller en tjänst som har ett gammalt lösenord.**
    * Om ett konto används av program eller tjänster kan dessa resurser upprepade gånger försöka logga in med ett gammalt lösenord. Detta medför att kontot är utelåst.
    * Försök att minimera kontoanvändningen i flera olika program eller tjänster och registrera var autentiseringsuppgifter används. Om ett kontolösenord ändras uppdaterar du de associerade programmen eller tjänsterna därefter.
* **Lösenordet har ändrats i en annan miljö och det nya lösenordet har inte synkroniserats ännu.**
    * Om ett kontolösenord ändras utanför Azure AD DS, till exempel i en AD DS-miljö på prem, kan det ta några minuter innan lösenordsändringen synkroniseras via Azure AD och till Azure AD DS.
    * En användare som försöker logga in på en resurs via Azure AD DS innan lösenordssynkroniseringsprocessen har slutförts gör att deras konto är utelåst.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Felsöka kontoutelåsningar med säkerhetsgranskningar

Om du vill felsöka när kontoutelåsningshändelser inträffar och varifrån de kommer [aktiverar du säkerhetsgranskningar för Azure AD DS][security-audit-events]. Granskningshändelser fångas bara in från den tidpunkt då du aktiverar funktionen. Helst bör du aktivera säkerhetsgranskningar *innan* det finns ett problem med kontoutelåsning att felsöka. Om ett användarkonto upprepade gånger har problem med utelåsning kan du aktivera säkerhetsgranskningar som är klara för nästa gång situationen inträffar.

När du har aktiverat säkerhetsgranskningar visar följande exempelfrågor hur du granskar *kontoutelåsningshändelser*, kod *4740*.

Visa alla kontoutelåsningshändelser de senaste sju dagarna:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Visa alla kontoutelåsningshändelser för de senaste sju dagarna för kontot *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Visa alla kontoutelåsningshändelser mellan den 26 juni 2019 klockan 09.00. och 1 juli 2019 midnatt, sorteras stigande efter datum och tid:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Nästa steg

Mer information om detaljerade lösenordsprinciper för att justera tröskelvärden för kontoutelåsning finns i [Konfigurera principer för lösenords- och kontoutelåsning][configure-fgpp].

Om du fortfarande har problem med att ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen [kan du hitta hjälp och öppna en supportbiljett för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
