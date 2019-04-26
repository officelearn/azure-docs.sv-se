---
title: Konfigurera Azure MFA NPS-tillägget - Azure Active Directory
description: När du installerar NPS-tillägget, Följ dessa steg för avancerad konfiguration som IP-vitlistning och UPN ersättning.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bfae3b3be7812ff50ed90a61d495877141bbc7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414910"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Avancerade konfigurationsalternativ för NPS-tillägget för Multifaktorautentisering

Tillägget nätverksprincipserver (NPS) utökar din molnbaserade Azure Multi-Factor Authentication-funktioner till din lokala infrastruktur. Den här artikeln förutsätter att du redan har installerat tillägget och nu vill du veta hur du anpassar tillägget för dina behov. 

## <a name="alternate-login-id"></a>Alternativa inloggnings-ID

Eftersom NPS-tillägget som ansluter till både lokalt och molnet kataloger, du kan stöta på ett problem där din lokala användarhuvudnamn (UPN) inte matchar namnen i molnet. Lös problemet genom att använda alternativa inloggnings-ID: N. 

Du kan ange ett Active Directory-attribut som ska användas i stället för UPN för Azure Multi-Factor Authentication i NPS-tillägget. På så sätt kan du skydda dina lokala resurser med tvåstegsverifiering utan att ändra din lokala UPN-namn. 

Om du vill konfigurera alternativa inloggnings-ID: N, gå till `HKLM\SOFTWARE\Microsoft\AzureMfa` och redigera följande registervärden:

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Tom | Ange namnet på Active Directory-attribut som du vill använda i stället för UPN. Det här attributet används som AlternateLoginId-attribut. Om det här registervärdet anges till en [giltigt Active Directory-attributet](https://msdn.microsoft.com/library/ms675090.aspx) (till exempel e-post eller displayName), sedan attributets värde används i stället för användarens UPN för autentisering. Om det här registervärdet är tom eller inte konfigurerad, sedan AlternateLoginId inaktiveras och användarens UPN används för autentisering. |
| LDAP_FORCE_GLOBAL_CATALOG | boolesk | False | Använd den här flaggan för att framtvinga användningen av den globala katalogen för LDAP-sökningar när den slår upp AlternateLoginId. Konfigurera en domänkontrollant som en Global katalog, lägga till attributet AlternateLoginId i den globala katalogen och sedan aktivera den här flaggan. <br><br> Om LDAP_LOOKUP_FORESTS konfigureras (inte tomt), **den här flaggan upprätthålls som SANT**, oavsett värdet för registerinställningen. NPS-tillägget kräver i så fall den globala katalogen som ska konfigureras med attributet AlternateLoginId för varje skog. |
| LDAP_LOOKUP_FORESTS | string | Tom | Ange en semikolonavgränsad lista över skogar för att söka. Till exempel *contoso.com;foobar.com*. Om det här registervärdet är konfigurerad, söker NPS-tillägget iterativt alla skogar i den ordning som de visades och returnerar det första lyckade AlternateLoginId värdet. Om det här registervärdet inte är konfigurerad, är AlternateLoginId sökning begränsat till den aktuella domänen.|

Använd rekommenderade åtgärder för felsökning av problem med alternativa inloggnings-ID: N [alternativa ID inloggningsfel](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-undantag

Om du vill övervaka servertillgänglighet, t.ex. Om belastningsutjämnare kontrollera vilka servrar som körs innan du skickar arbetsbelastningar, vill du inte de här kontrollerna blockeras av begäranden. I stället skapa en lista med IP-adresser som du vet som används av tjänstkonton och inaktivera multifaktorautentiseringskrav för listan. 

Om du vill konfigurera en godkänd IP-lista, gå till `HKLM\SOFTWARE\Microsoft\AzureMfa` och konfigurera följande registervärde: 

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Tom | Ange en semikolonavgränsad lista med IP-adresser. Inkludera IP-adresser för datorer där tjänstbegäranden kommer t.ex NAS/VPN-servern. IP-intervall och undernät stöds inte. <br><br> Till exempel *10.0.0.1;10.0.0.2;10.0.0.3*.

När en begäran kommer från en IP-adress som finns på vitlistan, hoppas över tvåstegsverifiering. IP-vitlistan jämförs IP-adressen som har angetts i den *ratNASIPAddress* attribut för RADIUS-begäran. Om en RADIUS-begäran kommer utan attributet ratNASIPAddress, loggas följande varning: ”P_WHITE_LIST_WARNING::IP vitlista ignoreras eftersom käll-IP saknas i RADIUS-begäran i NasIpAddress attribut”.

## <a name="next-steps"></a>Nästa steg

[Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
