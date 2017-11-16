---
title: "Konfigurera Azure MFA NPS-tillägget | Microsoft Docs"
description: "När du installerar NPS-tillägget, Följ dessa steg för avancerad konfiguration som vitlistning av IP- och UPN-ersättning."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 98c29b4124b31868ef118c39941cf9c3829e2b26
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Avancerade konfigurationsalternativ för NPS-tillägg för Multifaktorautentisering

Tillägget Server (NPS) utökar dina molnbaserade Azure Multi-Factor Authentication-funktioner till din lokala infrastruktur. Den här artikeln förutsätter att du redan har installerat tillägget och nu vill du veta hur du anpassar tillägget för dina behov. 

## <a name="alternate-login-id"></a>Alternativt inloggnings-ID

Eftersom tillägget NPS ansluter till både lokalt och molnet kataloger, som kan uppstå ett problem där din lokala användarens huvudnamn (UPN) inte matchar namnen i molnet. Om du vill lösa problemet genom att använda alternativa inloggnings-ID. 

Du kan ange ett Active Directory-attribut som ska användas i stället för UPN för Azure Multi-Factor Authentication i NPS-tillägget. På så sätt kan du skydda dina lokala resurser med tvåstegsverifiering utan att ändra din lokala UPN-namn. 

Om du vill konfigurera alternativa inloggnings-ID, gå till `HKLM\SOFTWARE\Microsoft\AzureMfa` och redigera följande registervärden:

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | Sträng | tom | Ange namnet på Active Directory-attribut som du vill använda i stället för UPN. Det här attributet används som AlternateLoginId-attribut. Om värdet anges till en [giltigt Active Directory-attribut](https://msdn.microsoft.com/library/ms675090.aspx) (till exempel e-post eller displayName), sedan attributets värde används i stället för användarens UPN för autentisering. Om värdet är tomt eller inte konfigurerad, sedan AlternateLoginId är inaktiverat och användarens UPN används för autentisering. |
| LDAP_FORCE_GLOBAL_CATALOG | Booleskt värde | False | Använd den här flaggan för att framtvinga användningen av den globala katalogen för LDAP-sökningar när du söker efter AlternateLoginId. Konfigurera en domänkontrollant som en Global katalog, lägga till attributet AlternateLoginId i den globala katalogen och sedan aktivera den här flaggan. <br><br> Om LDAP_LOOKUP_FORESTS konfigureras (inte tomt) **flaggan tillämpas som SANT**, oavsett värdet för registerinställningen. I det här fallet kräver filnamnstillägget NPS den globala katalogen som ska konfigureras med attributet AlternateLoginId för varje skog. |
| LDAP_LOOKUP_FORESTS | Sträng | tom | Ange en avgränsas med semikolon lista över skogar för att söka efter. Till exempel *contoso.com;foobar.com*. Om det här registervärdet som är konfigurerad, söker NPS-tillägget upprepade gånger alla skogar i den ordning som de visas i listan, och returnerar det första lycka AlternateLoginId värdet. Om det här registervärdet som inte är konfigurerad är AlternateLoginId sökning begränsad till den aktuella domänen.|

Använd rekommenderade åtgärder för felsökning av problem med alternativt inloggnings-ID: N [alternativa inloggnings-ID fel](multi-factor-authentication-nps-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-undantag

Om du behöver övervaka servertillgänglighet som om belastningsutjämnare kontrollera vilka servrar som körs innan du skickar arbetsbelastningar, kan du inte vill kontrollerna genom att verifiera begäranden. I stället skapa en lista med IP-adresser som du vet används av tjänstkonton och inaktivera Multifaktorautentisering krav för listan. 

Om du vill konfigurera en godkänd IP-lista, gå till `HKLM\SOFTWARE\Microsoft\AzureMfa` och konfigurera följande registervärde: 

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | Sträng | tom | Ange en lista över IP-adresser som gäller avgränsas med semikolon. Innehåller IP-adresser för datorer där tjänstbegäranden kommer som NAS/VPN-servern. IP-adressintervall är undernät inte stöds. <br><br> Till exempel *10.0.0.1;10.0.0.2;10.0.0.3*.

När en begäran kommer från en IP-adress som finns i listan, ignoreras tvåstegsverifiering. IP-godkända jämförs IP-adressen som har angetts i den *ratNASIPAddress* attribut för RADIUS-begäranden. Om en RADIUS-begäran kommer utan attributet ratNASIPAddress, loggas följande varning: ”P_WHITE_LIST_WARNING::IP godkända ignoreras som käll-IP saknas i RADIUS-begäranden i NasIpAddress attribut”.

## <a name="next-steps"></a>Nästa steg

[Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](multi-factor-authentication-nps-errors.md)