---
title: Hybrididentitetsdesign – innehållshanteringskrav Azure | Microsoft-dokument
description: Ger insikt i hur du fastställer innehållshanteringskraven för ditt företag. Vanligtvis när en användare har sin egen enhet, kan de också ha flera autentiseringsuppgifter som kommer att alternerande enligt det program som de använder. Det är viktigt att skilja vilket innehåll som skapades med hjälp av personliga autentiseringsuppgifter jämfört med de som skapats med företagsautentiseringsuppgifter. Din identitetslösning bör kunna interagera med molntjänster för att ge slutanvändaren en sömlös upplevelse samtidigt som den säkerställer deras integritet och ökar skyddet mot dataläckage.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64918447"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Fastställa innehållshanteringskrav för din hybrididentitetslösning
Om du förstår innehållshanteringskraven för ditt företag kan det direkt påverka ditt beslut om vilken hybrididentitetslösning som ska användas. Med spridningen av flera enheter och användarnas förmåga att ta med sina egna enheter[(BYOD),](https://aka.ms/byodcg)måste företaget skydda sina egna data, men det måste också hålla användarens integritet intakt. Vanligtvis när en användare har sin egen enhet, kan de också ha flera autentiseringsuppgifter som kommer att alternerande enligt det program som de använder. Det är viktigt att skilja vilket innehåll som skapades med hjälp av personliga autentiseringsuppgifter jämfört med de som skapats med företagsautentiseringsuppgifter. Din identitetslösning bör kunna interagera med molntjänster för att ge slutanvändaren en sömlös upplevelse samtidigt som den säkerställer deras integritet och ökar skyddet mot dataläckage. 

Din identitetslösning kommer att utnyttjas av olika tekniska kontroller för att tillhandahålla innehållshantering som visas i figuren nedan:

![säkerhetskontroller](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Säkerhetskontroller som kommer att utnyttja ditt identitetshanteringssystem**

I allmänhet kommer innehållshanteringskrav att utnyttja ditt identitetshanteringssystem inom följande områden:

* Sekretess: identifiera användaren som äger en resurs och tillämpa lämpliga kontroller för att upprätthålla integriteten.
* Dataklassificering: identifiera användaren eller gruppen och åtkomstnivån till ett objekt enligt dess klassificering. 
* Data leakage protection: säkerhetskontroller som ansvarar för att skydda data för att undvika läckage måste interagera med identitetssystemet för att validera användarens identitet. Detta är också viktigt för verifiering spår syfte.

> [!NOTE]
> Läs [dataklassificering för molnberedskap](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) för mer information om metodtips och riktlinjer för dataklassificering.
> 
> 

När du planerar din hybrididentitetslösning ska du se till att följande frågor besvaras enligt organisationens krav:

* Har ditt företag säkerhetskontroller för att upprätthålla datasekretessen?
  * Om ja, kommer säkerhetskontrollerna att kunna integreras med den hybrididentitetslösning som ni kommer att anta?
* Använder ditt företag dataklassificering?
  * Om ja, är den nuvarande lösningen kunna integrera med hybrid identitetslösning som du kommer att anta?
* Har ditt företag för närvarande någon lösning för dataläckage? 
  * Om ja, är den nuvarande lösningen kunna integrera med hybrid identitetslösning som du kommer att anta?
* Behöver ditt företag granska åtkomsten till resurser?
  * Om ja, vilken typ av resurser?
  * Om ja, vilken informationsnivå är nödvändig?
  * Om ja, där granskningsloggen måste finnas? Lokalt eller i molnet?
* Behöver ditt företag kryptera e-postmeddelanden som innehåller känsliga data (SSN, kreditkortsnummer, etc.)?
* Behöver ditt företag kryptera alla dokument/innehåll som delas med externa affärspartners?
* Behöver ditt företag att genomdriva företagets policyer på vissa typer av e-postmeddelanden (inte svara alla, inte vidarebefordra)?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera dataskyddsstrategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att gå igenom de tillgängliga alternativen och fördelar / nackdelar med varje alternativ.  Genom att ha svarat på dessa frågor väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa åtkomstkontrollkrav](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

