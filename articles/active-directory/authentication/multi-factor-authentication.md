---
title: Lär dig mer om tvåstegsverifiering i Azure MFA | Microsoft Docs
description: Vad är Azure Multi-Factor Authentication, varför ska jag använda MFA, och olika metoder och versioner som är tillgängliga.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 2bf2cbefb2b60ad5278bdee7bd97aff1ed6212a4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-azure-multi-factor-authentication"></a>Vad är Azure Multi-Factor Authentication?

Tvåstegsverifiering är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager till användarinloggningar och transaktioner. Det fungerar genom att två eller flera av följande verifieringsmetoder för:

* Något du vet (normalt ett lösenord)
* Något du har (betrodda enheter som inte enkelt dubbleras, t.ex. en telefon)
* Något du är (biometrik)

<center>![Användarnamn och lösenord](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![certifikat](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Smart Phone](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![smartkort](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Virtuellt smartkort](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![användarnamn och lösenord](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering via en mängd verifieringsmetoder, inklusive telefonsamtal, textmeddelande eller verifiering av mobilappar.

## <a name="why-use-azure-multi-factor-authentication"></a>Varför ska man använda Azure Multi-Factor Authentication?
I större utsträckning än någonsin, ansluts allt personer. Med smarta telefoner, surfplattor, bärbara datorer och datorer som har personer flera alternativ för att komma åt sina konton och program från var som helst och upprätthålla anslutningen när som helst.

Azure Multi-Factor Authentication är en lättanvänd, skalbara och tillförlitliga lösning som innehåller en annan metod för autentisering för att skydda dina användare.

| ![Lätt att använda](./media/multi-factor-authentication/simple.png) | ![Skalbar](./media/multi-factor-authentication/scalable.png) | ![Alltid skyddad](./media/multi-factor-authentication/protected.png) | ![Tillförlitlig](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Lätt att använda** |**Skalbar** |**Alltid skyddad** |**Tillförlitlig** |

* **Lättanvända** -Azure Multi-Factor Authentication är enkel att konfigurera och använda. Det extra skydd som medföljer Azure Multi-Factor Authentication tillåter användare att hantera sina egna enheter. Bästa av alla i många fall det kan ställas in med ett par enkla klick.
* **Skalbar** -Azure Multi-Factor Authentication använder kraften i molnet och kan integreras med din lokala AD och anpassade appar. Det här skyddet utökas även stora volymer, verksamhetskritiska scenariot.
* **Alltid skyddad** -Azure Multi-Factor Authentication ger stark autentisering med högsta branschstandarder.
* **Tillförlitliga** -Microsoft garanterar 99,9% tillgänglighet för Azure Multi-Factor Authentication. Tjänsten anses inte tillgängligt när det inte går att ta emot eller bearbetar begäranden för verifiering för tvåstegsverifiering.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hur Azure Multi-Factor Authentication fungerar](concept-mfa-howitworks.md)

- Läs mer om de olika [versioner och av förbrukningsmetoder för Azure Multi-Factor Authentication](concept-mfa-licensing.md)
