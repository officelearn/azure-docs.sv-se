---
title: Referensguide för Azure Active Directory-åtgärder
description: I referensguiden för åtgärder beskrivs de kontroller och åtgärder du bör vidta för att skydda och underhålla identitets- och åtkomsthantering, autentisering, styrning och åtgärder
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535319"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Referensguide för Azure Active Directory-åtgärder

I referensguiden för åtgärder beskrivs de kontroller och åtgärder du bör vidta för att skydda och underhålla följande områden:

- **[Identitets- och åtkomsthantering](active-directory-ops-guide-iam.md)** - förmåga att hantera identiteternas livscykel och deras rättigheter.
- **[Autentiseringshantering](active-directory-ops-guide-auth.md)** - förmåga att hantera autentiseringsuppgifter, definiera autentiseringsupplevelse, delegera tilldelning, mäta användning och definiera åtkomstprinciper baserat på företagets säkerhetsposition.
- **[Styrning](active-directory-ops-guide-govern.md)** - förmåga att bedöma och intyga åtkomst som beviljas icke-privilegierade och privilegierade identiteter, granskning och kontrolländringar i miljön.
- **[Åtgärder](active-directory-ops-guide-ops.md)** - optimera åtgärderna Azure Active Directory (Azure AD).

Vissa rekommendationer här kanske inte är tillämpliga på alla kunders miljö, till exempel kanske ad FS-metodtips inte gäller om din organisation använder synkronisering av lösenordshöskning.

> [!NOTE]
> Dessa rekommendationer är aktuella från och med publiceringsdatumet men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitetsmetoder när Microsofts produkter och tjänster utvecklas med tiden. Rekommendationer kan ändras när organisationer prenumererar på en annan Azure AD Premium-licens. Azure AD Premium P2 innehåller till exempel fler styrningsrekommendationer.

## <a name="stakeholders"></a>Intressenter

Varje avsnitt i den här referensguiden rekommenderar att intressenter tilldelas för att planera och implementera viktiga uppgifter. I följande tabell beskrivs listan över alla intressenter i den här handboken:

| Intressent | Beskrivning |
| :- | :- |
| IAM Operations Team | Det här teamet hanterar den dagliga driften av identitets- och åtkomsthanteringssystemet |
| Produktivitetsteam | Det här teamet äger och hanterar produktivitetsprogram som e-post, fildelning och samarbete, snabbmeddelanden och konferenser. |
| Programägare | Det här teamet äger det specifika programmet från ett företag och vanligtvis ett tekniskt perspektiv i en organisation. |
| InfoSec-arkitekturteam | Det här teamet planerar och utformar en organisations rutiner för informationssäkerhet. |
| InfoSec operationsteam | Det här teamet kör och övervakar de implementerade informationssäkerhetsrutinerna i InfoSec Architecture-teamet. |

## <a name="next-steps"></a>Nästa steg

Kom igång med kontroller och åtgärder för [identitets- och åtkomsthantering](active-directory-ops-guide-iam.md).
