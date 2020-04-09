---
title: Felsöka TLS/SSL-problem (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Läs om vad du kan göra om olika problem med TLS/SSL-certifikat med MSAL. Objektiv-C-bibliotek.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881085"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Så här felsöker du MSAL för iOS- och macOS TLS/SSL-problem

Den här artikeln innehåller information som hjälper dig att felsöka problem som kan uppstå när du använder [Microsoft Authentication Library (MSAL) för iOS och macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Nätverksproblem

**Fel -1200**: "Ett SSL-fel har inträffat och en säker anslutning till servern kan inte göras."

Det här felet innebär att anslutningen inte är säker. Det inträffar när ett certifikat är ogiltigt. Mer information, inklusive vilken server som inte uppfyller `NSURLErrorFailingURLErrorKey` TLS-kontrollen, finns i `userInfo` ordlistan för felobjektet.

Det här felet kommer från Apples nätverksbibliotek. En fullständig lista över NSURL-felkoder finns i NSURLError.h i macOS- och iOS SDK:er. Mer information om det här felet finns i [URL-inläsning av systemfelkoder](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problem med certifikat

Om url:en som tillhandahåller ett ogiltigt certifikat ansluter till den server som du tänker använda som en del av autentiseringsflödet, är en bra början på att diagnostisera problemet att testa URL:en med en SSL-valideringstjänst som [SSL Server Test](https://www.ssllabs.com/ssltest/analyze.html). Den testar servern mot ett brett spektrum av scenarier och webbläsare och kontrollerar många kända sårbarheter.

Som standard tillämpar Apples nya [ATS-funktion (App Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) strängare säkerhetsprinciper för appar som använder TLS/SSL-certifikat. Vissa operativsystem och webbläsare har börjat tillämpa vissa av dessa principer som standard. Av säkerhetsskäl rekommenderar vi att du inte inaktiverar ATS.

Certifikat som använder SHA-1 hashar har kända säkerhetsproblem. De flesta moderna webbläsare tillåter inte certifikat med SHA-1 hashar.

## <a name="captive-portals"></a>Captiveportaler

En företagsintern portal presenterar en webbsida för en användare när de först kommer åt ett Wi-Fi-nätverk och ännu inte har beviljats åtkomst till det nätverket. Det fångar upp deras internettrafik tills användaren uppfyller kraven i portalen. Nätverksfel eftersom användaren inte kan ansluta till nätverksresurser förväntas förrän användaren ansluter via portalen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [captiveportaler](https://en.wikipedia.org/wiki/Captive_portal) och Apples nya [ATS-funktion (App Transport Security).](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)
