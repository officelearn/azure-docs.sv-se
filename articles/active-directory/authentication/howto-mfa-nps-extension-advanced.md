---
title: Konfigurera Azure MFA NPS-tillägget - Azure Active Directory
description: När du har installerat NPS-tillägget använder du dessa steg för avancerad konfiguration som IP-vitlistning och UPN-ersättning.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34d92af88106151e7efba679c53c5b5bd1c07dcd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653787"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Avancerade konfigurationsalternativ för NPS-tillägget för multifaktorautentisering

Nps-tillägget (Network Policy Server) utökar dina molnbaserade Azure Multi Factor-autentiseringsfunktioner till din lokala infrastruktur. Den här artikeln förutsätter att du redan har tillägget installerat och nu vill veta hur du anpassar tillägget för dig behöver. 

## <a name="alternate-login-id"></a>Alternativt inloggnings-ID

Eftersom NPS-tillägget ansluter till både lokala och molnkataloger kan du stöta på ett problem där dina lokala användarnamn (UPN) inte matchar namnen i molnet. För att lösa detta problem, använd alternativa inloggnings-ID. 

I NPS-tillägget kan du ange ett Active Directory-attribut som ska användas i stället för UPN för Azure Multi Factor-autentisering. På så sätt kan du skydda dina lokala resurser med tvåstegsverifiering utan att ändra dina lokala UPN-nätverk. 

Om du vill konfigurera alternativa `HKLM\SOFTWARE\Microsoft\AzureMfa` inloggnings-ID:n går du till och redigerar följande registervärden:

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | sträng | Tom | Ange namnet på Active Directory-attributet som du vill använda i stället för UPN. Det här attributet används som attributet AlternateLoginId. Om det här registervärdet är inställt på ett [giltigt Active Directory-attribut](https://msdn.microsoft.com/library/ms675090.aspx) (t.ex. e-post eller displayName) används attributets värde i stället för användarens UPN för autentisering. Om det här registervärdet är tomt eller inte konfigurerat inaktiveras AlternateLoginId och användarens UPN används för autentisering. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | Använd den här flaggan för att tvinga användningen av global katalog för LDAP-sökningar när du slår upp AlternateLoginId. Konfigurera en domänkontrollant som en global katalog, lägg till attributet AlternateLoginId i den globala katalogen och aktivera den här flaggan. <br><br> Om LDAP_LOOKUP_FORESTS är konfigurerad (inte tom) **tillämpas den här flaggan som true**, oavsett värdet för registerinställningen. I det här fallet kräver NPS-tillägget att den globala katalogen konfigureras med attributet AlternateLoginId för varje skog. |
| LDAP_LOOKUP_FORESTS | sträng | Tom | Ange en semikolonavskiljad lista över skogar att söka efter. Till exempel *contoso.com;foobar.com*. Om det här registervärdet har konfigurerats söker NPS-tillägget iterativt igenom alla skogar i den ordning de listades och returnerar det första lyckade AlternateLoginId-värdet. Om det här registervärdet inte är konfigurerat begränsas AlternateLoginId-sökningen till den aktuella domänen.|

Om du vill felsöka problem med alternativa inloggnings-ID:n följer du de rekommenderade stegen för [alternativa inloggnings-ID-fel](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>IP-undantag

Om du behöver övervaka serverns tillgänglighet, till exempel om belastningsutjämningstagare verifierar vilka servrar som körs innan arbetsbelastningar skickas, vill du inte att dessa kontroller ska blockeras av verifieringsbegäranden. Skapa i stället en lista över IP-adresser som du vet används av tjänstkonton och inaktivera multifaktorautentiseringskrav för den listan.

Om du vill konfigurera en `HKLM\SOFTWARE\Microsoft\AzureMfa` lista över tillåtna IP-uppgifter går du till och konfigurerar följande registervärde:

| Namn | Typ | Standardvärde | Beskrivning |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | sträng | Tom | Ange en semikolonavgränsad lista över IP-adresser. Inkludera IP-adresserna för datorer där tjänstbegäranden kommer, till exempel NAS/VPN-servern. IP-intervall och undernät stöds inte. <br><br> Till exempel *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Den här registernyckeln skapas inte som standard av installationsprogrammet och ett fel visas i AuthZOptCh-loggen när tjänsten startas om. Det här felet i loggen kan ignoreras, men om den här registernyckeln skapas och lämnas tom om det inte behövs returneras inte felmeddelandet.

När en begäran kommer in från en `IP_WHITELIST`IP-adress som finns i verifieringen i två steg hoppas över. IP-listan jämförs med IP-adressen som finns i *attributet ratNASIPAddress* för RADIUS-begäran. Om en RADIUS-begäran kommer in utan attributet ratNASIPAddress loggas följande varning: "P_WHITE_LIST_WARNING::IP Whitelist ignoreras eftersom käll-IP saknas i RADIUS-begäran i NasIpAddress-attributet."

## <a name="next-steps"></a>Nästa steg

[Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
