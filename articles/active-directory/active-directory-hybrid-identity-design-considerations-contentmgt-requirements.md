---
title: "Identitet hybridutformning - krav för innehållshantering Azure | Microsoft Docs"
description: "Ger kunskaper om hur du fastställer kraven för innehållshantering i ditt företag. Vanligtvis kanske när en användare har sin egen enhet han också flera autentiseringsuppgifter som ska alternerande enligt det program som han använder. Det är viktigt att skilja mellan vilka innehållet har skapats med hjälp av personliga autentiseringsuppgifter jämfört med de som skapas med företagets autentiseringsuppgifter. Identitetslösning bör kunna interagera med cloud services och ger en sömlös upplevelse för slutanvändaren när säkerställa sina sekretess och ökar skyddet mot dataläckage."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 71e33ec82c3db6fb7efa52dd12315e309658aab9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Ange krav för innehållshantering för din hybrididentitetslösning
Förstå kraven för innehållshantering för ditt företag kan direkt påverka ditt beslut om vilken hybrididentitetslösning som ska användas. Med den ökande mängden av flera enheter och göra att användare kan ha sina egna enheter ([BYOD](https://aka.ms/byodcg)), måste företaget skydda egna data men den måste behålla användarens integritet intakta. Vanligtvis kanske när en användare har sin egen enhet han också flera autentiseringsuppgifter som ska alternerande enligt det program som han använder. Det är viktigt att skilja mellan vilka innehållet har skapats med hjälp av personliga autentiseringsuppgifter jämfört med de som skapas med företagets autentiseringsuppgifter. Identitetslösning bör kunna interagera med cloud services och ger en sömlös upplevelse för slutanvändaren när säkerställa sina sekretess och ökar skyddet mot dataläckage. 

Identity-lösningen kommer utnyttjas av olika tekniska kontroller för att ge innehållshantering som visas i bilden nedan:

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Säkerhetsåtgärder som kommer kunna utnyttja systemet identity management**

I allmänhet använder innehållshantering krav din identitet hanteringssystemet inom följande områden:

* Sekretess: identifiera användaren som äger en resurs och tillämpa lämpliga kontroller för att bibehålla dataintegritet.
* Dataklassificering: identifiera användaren eller gruppen och åtkomst till ett objekt enligt sin klassificering. 
* Läckage av dataskydd: säkerhetsåtgärder som är ansvarig för att skydda data för att undvika läckage behöver interagera med identitetssystem för att verifiera användarens identitet. Detta är även viktigt för spår revision.

> [!NOTE]
> Läs [dataklassificering moln beredskap för](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) mer information om metodtips och riktlinjer för dataklassificering.
> 
> 

När planera din hybrididentitetslösning för att säkerställa att följande frågor besvaras enligt organisationens behov:

* Har företaget säkerhetsåtgärder för att genomdriva datasekretess?
  * Om Ja, kommer dessa säkerhetsåtgärder att kunna integreras med hybrididentitetslösning som du kommer att anta?
* Använder företaget dataklassificering?
  * Om Ja, kan den aktuella lösningen integreras med hybrididentitetslösning som du kommer att anta?
* Har ditt företag för närvarande någon lösning för dataläckage? 
  * Om Ja, kan den aktuella lösningen integreras med hybrididentitetslösning som du kommer att anta?
* Behöver ditt företag kunna granska åtkomsten till resurser?
  * Om Ja, vilken typ av resurser?
  * Om Ja, hur mycket information som krävs?
  * Om Ja, där granskningsloggen måste finnas? Lokalt eller i molnet?
* Behöver ditt företag att kryptera alla e-postmeddelanden som innehåller känsliga data (personnummer, kreditkortsnummer o.s.v.)?
* Behöver ditt företag att kryptera alla dokument/innehållet delas med affärspartners?
* Behöver företaget att genomdriva företagsprinciper på vissa typer av e-postmeddelanden (gör något alla svar, vidarebefordra inte)?

> [!NOTE]
> Se till att varje svar och försök förstå anledningen till svaret. [Definiera en strategi för Data Protection](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att överskrida det tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Har besvarat frågorna väljer du vilket alternativ som bäst passar dina behöver.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav på åtkomstkontroll](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

