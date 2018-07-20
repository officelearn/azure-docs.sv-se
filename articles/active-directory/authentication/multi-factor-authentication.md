---
title: Lär dig mer om tvåstegsverifiering i Azure MFA | Microsoft Docs
description: Vad är Azure Multi-Factor Authentication, varför ska jag använda MFA, och olika metoder och versioner som är tillgängliga.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d58d81d85dac7e5cd520b8d8a3fb5d91650e0cbe
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161613"
---
# <a name="what-is-azure-multi-factor-authentication"></a>Vad är Azure Multi-Factor Authentication?

Tvåstegsverifiering är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett viktigt andra säkerhetslager till användarinloggningar och transaktioner. Det fungerar genom att kräva två eller flera av följande verifieringsmetoder:

* Något du vet (normalt ett lösenord)
* Något du har (betrodda enheter som inte enkelt dupliceras, t.ex. en telefon)
* Något du är (biometrik)

<center>![Användarnamn och lösenord](./media/multi-factor-authentication/pword.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![certifikat](./media/multi-factor-authentication/phone.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Smart Phone](./media/multi-factor-authentication/hware.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![smartkort](./media/multi-factor-authentication/smart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![Virtuellt smartkort](./media/multi-factor-authentication/vsmart.png) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ![användarnamn och lösenord](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering via en mängd verifieringsmetoder, inklusive telefonsamtal, textmeddelande eller verifiering av mobilappar.

## <a name="why-use-azure-multi-factor-authentication"></a>Varför ska man använda Azure Multi-Factor Authentication?
Idag, mer än någonsin, ansluts alltmer personer. Med Smartphones, surfplattor, bärbara datorer och datorer har personer flera alternativ för att komma åt sina konton och program från var som helst och var uppkopplad när som helst.

Azure Multi-Factor Authentication är en lättanvänd, skalbar och tillförlitlig lösning som ger ett annat sätt för att skydda dina användare.

| ![Lätt att använda](./media/multi-factor-authentication/simple.png) | ![Skalbar](./media/multi-factor-authentication/scalable.png) | ![Alltid skyddat](./media/multi-factor-authentication/protected.png) | ![Tillförlitlig](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Lätt att använda** |**Skalbar** |**Alltid skyddat** |**Tillförlitlig** |

* **Lätt att använda** -Azure Multi-Factor Authentication är enkelt att konfigurera och använda. Det extra skydd som medföljer Azure Multi-Factor Authentication tillåter användare att hantera sina egna enheter. Bästa av allt är i många fall den kan konfigureras med bara några få enkla klick.
* **Skalbar** – Azure Multi-Factor Authentication använder kraften i molnet och integreras med din lokala AD och anpassade appar. Det här skyddet utökas även till dina stora volymer, verksamhetskritiska scenarier.
* **Alltid skyddat** – Azure Multi-Factor Authentication ger stark autentisering med de högsta branschstandarderna.
* **Tillförlitlig** -Microsoft garanterar 99,9% tillgänglighet för Azure Multi-Factor Authentication. Tjänsten betraktas som otillgänglig när det inte går att ta emot eller behandla begäranden för tvåstegsverifieringen.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hur Azure Multi-Factor Authentication fungerar](concept-mfa-howitworks.md)

- Läs om de olika [versioner och förbrukningsmetoder för Azure Multi-Factor Authentication](concept-mfa-licensing.md)
