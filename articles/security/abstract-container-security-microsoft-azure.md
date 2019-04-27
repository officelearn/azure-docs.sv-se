---
title: Abstrakt behållarsäkerhet i Microsoft Azure
description: Abstrakt för behållarsäkerhet i Microsoft Azure, faktablad.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590102"
---
# <a name="container-security-in-microsoft-azure"></a>Behållarsäkerhet i Microsoft Azure
## <a name="abstract"></a>Abstrakt

Behållarteknik gör en ändring av strukturella molntjänster över hela världen. Behållare gör det möjligt att köra flera instanser av ett program på en enda instans av ett operativsystem, och därmed använda resurser mer effektivt. Behållare ger organisationer konsekvens och flexibilitet. De kan aktivera kontinuerlig distribution eftersom programmet kan utvecklas på en stationär dator, testas i en virtuell dator och sedan distribueras för produktion i molnet. Behållare ger flexibilitet, strömlinjeformade verksamheter, skalbarhet och minskade kostnader på grund av resursoptimering.

Eftersom behållarteknik är relativt nytt, har många IT-proffs säkerhetsfrågor om bristen på synlighet och användning i en produktionsmiljö. Utvecklingsgrupper brukar vara ovetande om rekommenderade säkerhetsmetoder. Detta white paper hjälper security team och utvecklare väljer metoder att säkra behållare utveckling och distribution på Microsoft Azure-plattformen.

Det här dokumentet beskriver behållare, behållardistribution och hantering och interna plattformstjänster. Här beskrivs också runtime säkerhetsproblem som kan uppstå med hjälp av behållare på Azure-plattformen. Med siffror och exempel fokuserar det här dokumentet på Docker som modellen för behållare och Kubernetes som behållarinitieraren. De flesta av säkerhetsrekommendationerna gäller även för andra behållare modeller från Microsoft-partners på Azure-plattformen.

[Ladda ned dokumentet](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)