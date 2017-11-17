---
title: "Azure betalning bearbetning modell - översikt"
description: "Azure betalning bearbetning modell - kund ansvar matris (översikt)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>Krav för PCI DSS - översikt

Den Payment Card Industry Data Security Standard (PCI DSS) utvecklades för att uppmuntra och förbättra Kortinnehavarens datasäkerhet och underlätta bred införandet av konsekventa data säkerhetsåtgärder globalt. PCI DSS ger en baslinje för tekniska och operativa krav som utformats för att skydda kontodata. PCI DSS gäller för alla enheter som ingår i payment card bearbetning, inklusive butiker, processorer, förvärvar, utfärdare och leverantörer. PCI DSS gäller även för andra enheter som kan lagra, hantera och skicka kreditkortsinformation (CHD) och/eller känslig autentiseringsdata (SAD). Nedan visas en översikt över 12 PCI DSS kraven.

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

|   |   |
|---|---|
| **Bygga och underhålla en säker<br/>nätverks- och** | 1. [Installera och underhålla en brandväggskonfigurationen för att skydda kreditkortsinformation](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Använd inte från leverantören standardinställningar för system lösenord och andra parametrar för säkerhet](pci-dss-requirement-2-password.md) |  
| **Skydda kreditkortsinformation** | 3. [Skydda lagrade kreditkortsinformation](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Kryptera överföring av kreditkortsinformation över öppna offentliga nätverk](pci-dss-requirement-4-encryption.md) |
| **Underhålla en säkerhetsrisk<br/>hanteringsprogram** | 5. [Alla datorer mot skadlig kod och regelbundet uppdatera antivirusprogram eller program](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Utveckla och underhålla säkra system och program](pci-dss-requirement-6-secure-system.md) |
| **Implementera starka åtkomst<br/>åtgärder kontroll** | 7. [Begränsa åtkomsten till kreditkortsinformation företag behöver känna till](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identifiera och autentisera åtkomsten till systemkomponenter](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Begränsa fysisk åtkomst till kreditkortsinformation](pci-dss-requirement-9-physical-access.md) |
| **Övervaka regelbundet och<br/>testa nätverk** | 10. [Spåra och övervaka all åtkomst till nätverksresurser och kreditkortsinformation](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Regelbundet testa säkerhetssystem och processer](pci-dss-requirement-11-testing.md) |
| **Underhåll en Information<br/>säkerhetsprincip** | 12. [Underhålla en princip som åtgärdar informationssäkerhet för all personal](pci-dss-requirement-12-policy.md) |

