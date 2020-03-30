---
title: Lösa säkra LDAP-aviseringar i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du felsöker och löser vanliga aviseringar med säker LDAP för Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258054"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Kända problem: Säkra LDAP-aviseringar i Azure Active Directory Domain Services

Program och tjänster som använder LDAP (Lightweight Directory Access Protocol) för att kommunicera med Azure Active Directory Domain Services (Azure AD DS) kan [konfigureras för att använda säker LDAP](tutorial-configure-ldaps.md). Ett lämpligt certifikat och nödvändiga nätverksportar måste vara öppna för att säker LDAP ska fungera korrekt.

Den här artikeln hjälper dig att förstå och lösa vanliga aviseringar med säker LDAP-åtkomst i Azure AD DS.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Säker LDAP-nätverkskonfiguration

### <a name="alert-message"></a>Varningsmeddelande

*Säker LDAP över internet är aktiverat för den hanterade domänen. Åtkomsten till port 636 är dock inte låst med hjälp av en nätverkssäkerhetsgrupp. Detta kan exponera användarkonton på den hanterade domänen för lösenord brute-force-attacker.*

### <a name="resolution"></a>Lösning

När du aktiverar säker LDAP rekommenderar vi att du skapar ytterligare regler som begränsar inkommande LDAPS-åtkomst till specifika IP-adresser. Dessa regler skyddar Azure AD DS-hanterade domänen från brute force-attacker. Så här uppdaterar du nätverksskyddsgruppen för att begränsa åtkomsten till TCP-port 636 för säker LDAP:

1. Sök efter och välj **Nätverkssäkerhetsgrupper**i Azure-portalen .
1. Välj den nätverkssäkerhetsgrupp som är associerad med den hanterade domänen, till exempel *AADDS-contoso.com-NSG*, och välj sedan **säkerhetsregler för inkommande**
1. **+ Lägg till** en regel för TCP-port 636. Om det behövs väljer du **Avancerat** i fönstret för att skapa en regel.
1. För **källan**väljer du *IP-adresser* på den nedrullningsvänliga menyn. Ange de käll-IP-adresser som du vill bevilja åtkomst för säker LDAP-trafik.
1. Välj *Alla* som **mål**och ange sedan *636* för **målportintervall**.
1. Ange **protokollet** som *TCP* och **åtgärden** som *ska tillåtas*.
1. Ange prioritet för regeln och ange sedan ett namn som *RestrictLDAPS*.
1. När du är klar väljer du **Lägg till** för att skapa regeln.

Azure AD DS-hanterade domänens hälsa uppdaterar sig automatiskt inom två timmar och tar bort aviseringen.

> [!TIP]
> TCP-port 636 är inte den enda regeln som behövs för att Azure AD DS ska fungera smidigt. Mer information finns i [säkerhetsgrupperna för Azure AD DS Network och nödvändiga portar](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Säkert LDAP-certifikat löper ut

### <a name="alert-message"></a>Varningsmeddelande

*Det säkra LDAP-certifikatet för den hanterade domänen upphör att gälla [datum]].*

### <a name="resolution"></a>Lösning

Skapa ett nytt säkert LDAP-certifikat genom att följa stegen för att [skapa ett certifikat för säker LDAP](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap). Använd ersättningscertifikatet på Azure AD DS och distribuera certifikatet till alla klienter som ansluter med säker LDAP.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
