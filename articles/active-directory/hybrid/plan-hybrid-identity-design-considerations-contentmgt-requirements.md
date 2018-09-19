---
title: Hybrid identity design - krav för innehållshantering Azure | Microsoft Docs
description: Ger information om hur du fastställer kraven för innehållshantering i din verksamhet. Vanligtvis när en användare har sina egna enheter, kan de också ha flera autentiseringsuppgifter som ska alternerande beroende på program som de använder. Det är viktigt att skilja vilket innehåll skapades med personliga autentiseringsuppgifter från sådana som skapats med företagets autentiseringsuppgifter. ID-lösning bör kunna interagera med cloud services för att ge en sömlös upplevelse för slutanvändaren samtidigt se till att deras integritet och öka skyddet mot dataläckage.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a3e5ae61fcefcb65d08288a2ddc2dd239e68b211
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46312258"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Fastställa krav för innehållshantering för din hybrididentitetslösning
Förstå kraven för innehållshantering för ditt företag kan direkt påverka ditt beslut om vilken hybrididentitetslösning att använda. Genom spridningen av flera enheter och göra att användare kan ha sina egna enheter ([BYOD](https://aka.ms/byodcg)), företaget måste skydda sina egna data men den måste behålla användarens integritet intakta. Vanligtvis när en användare har sina egna enheter, kan de också ha flera autentiseringsuppgifter som ska alternerande beroende på program som de använder. Det är viktigt att skilja vilket innehåll skapades med personliga autentiseringsuppgifter från sådana som skapats med företagets autentiseringsuppgifter. ID-lösning bör kunna interagera med cloud services för att ge en sömlös upplevelse för slutanvändaren samtidigt se till att deras integritet och öka skyddet mot dataläckage. 

ID-lösning kan utnyttjas av olika tekniska kontroller för att tillhandahålla innehållshantering, enligt bilden nedan:

![](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Säkerhetskontroller som kommer att utnyttja dina identitetshanteringssystem**

I allmänhet att innehållshantering krav utnyttja dina identitetshanteringssystem inom följande områden:

* Sekretess: identifiera användaren som äger en resurs och tillämpa lämpliga kontroller för att upprätthålla integriteten.
* Dataklassificering: identifiera användaren eller gruppen och åtkomstnivå till ett objekt enligt dess klassificering. 
* Data Leakage Protection: säkerhetskontroller som ansvarar för att skydda data för att undvika läckage måste interagera med ID-system för att verifiera användarens identitet. Detta är också viktigt för revision för spårning.

> [!NOTE]
> Läs [dataklassificering för användning i molnet](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) för mer information om metodtips och riktlinjer för klassificering av data.
> 
> 

När planera din hybrididentitetslösning kontrollerar du att följande frågor besvaras enligt organisationens krav:

* Har företaget säkerhetskontroller för att framtvinga datasekretess?
  * Om Ja, kommer säkerhetskontrollerna som att kunna integrera med hybrididentitetslösning som du ska använda?
* Använder företaget dataklassificering?
  * Om Ja, kan den aktuella lösningen integreras med hybrididentitetslösning som du ska använda?
* Har företaget för närvarande någon lösning för dataläckage? 
  * Om Ja, kan den aktuella lösningen integreras med hybrididentitetslösning som du ska använda?
* Behöver företaget att granska åtkomst till resurser?
  * Om Ja, vilken typ av resurser?
  * Om Ja, vilken nivå av information som krävs?
  * Om Ja, där granskningsloggen måste finnas? Den lokala eller i molnet?
* Behöver företaget att kryptera alla e-postmeddelanden som innehåller känsliga data (personnummer, kreditkortsnummer, osv.)
* Behöver företaget att kryptera alla dokument/innehållet delas med externa affärspartner?
* Behöver företaget att framtvinga företagets principer för vissa typer av e-postmeddelanden (gör inga svara alla, vidarebefordra inte)?

> [!NOTE]
> Se till att ner varje svar och försök förstå anledningen till svaret. [Definiera en strategi för Data Protection](plan-hybrid-identity-design-considerations-data-protection-strategy.md) tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Har besvarat frågor väljer du vilket alternativ som bäst passar din verksamhet behöver du.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav på åtkomstkontroll](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

