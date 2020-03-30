---
title: Hög tillgänglighet och haveriberedskap - Azure Batch
description: Lär dig hur du utformar batch-programmet för ett regionalt avbrott. Arbetsbelastningar bör växla över till en annan region eller delas mellan två eller flera regioner.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026089"
---
# <a name="design-your-application-for-high-availability"></a>Utforma ditt program för hög tillgänglighet

Azure Batch är en regional tjänst. Batch är tillgänglig i alla Azure-regioner, men när ett batchkonto skapas måste den associeras med en region. Alla åtgärder för batchkontot gäller sedan för den regionen. Till exempel skapas pooler och associerade virtuella datorer (VMs) i samma region som batch-kontot.

När du utformar ett program som använder Batch måste du överväga möjligheten att Batch inte är tillgänglig i en region. Det är möjligt att stöta på en sällsynt situation där det finns ett problem med regionen som helhet, hela Batch-tjänsten i regionen eller ett problem med ditt specifika Batch-konto.

Om programmet eller lösningen som använder Batch alltid måste vara tillgänglig bör det utformas så att den antingen redundans till en annan region eller alltid har arbetsbelastningen uppdelad mellan två eller flera regioner. Båda metoderna kräver minst två Batch-konton, med varje konto i en annan region.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Flera batchkonton i flera regioner

Om du använder flera batchkonton i olika regioner kan programmet fortsätta att köras om ett batchkonto i en annan region blir otillgängligt. Det är särskilt viktigt att använda flera konton om ditt program måste vara högtillgänge.

I vissa fall kan ett program vara utformat för att alltid använda två eller flera regioner. Till exempel, när du behöver en betydande mängd kapacitet, med hjälp av flera regioner kan behövas för att hantera antingen ett storskaligt program eller tillgodose framtida tillväxt.

## <a name="design-considerations-for-providing-failover"></a>Designöverväganden för att tillhandahålla redundans

En viktig punkt att tänka på när man ger möjlighet att redundans till en alternativ region är att alla komponenter i en lösning måste beaktas. det räcker inte att helt enkelt ha ett andra Batch-konto. I de flesta batch-program krävs till exempel ett Azure-lagringskonto, med lagringskontot och Batch-kontot som måste vara i samma region för acceptabel prestanda.

Tänk på följande punkter när du utformar en lösning som kan redundans:

- Skapa alla nödvändiga konton i varje region, till exempel batchkontot och lagringskontot. Det finns ofta ingen avgift för att ha konton skapade, bara när det finns data lagrade eller kontot används.
- Kontrollera att kvoterna är inställda på kontona i förväg, så att du kan allokera det antal kärnor som krävs med batchkontot.
- Använd mallar och/eller skript för att automatisera distributionen av programmet i en region.
- Håll program binärer och referensdata uppdaterade i alla regioner. Att hålla sig uppdaterad kommer att se till att regionen kan anslutas snabbt utan att behöva vänta på uppladdning och distribution av filer. Om till exempel ett anpassat program som ska installeras på poolnoder lagras och refereras med batchprogrampaket, ska det överföras till varje batchkonto och refereras av poolkonfigurationen (eller göra den nya versionen till standardversion).
- I programmet anropar Batch, lagring och andra tjänster kan du enkelt växla klienter eller belastningen till den olika regionen.
- En bästa praxis för att säkerställa en redundans kommer att lyckas är att ofta övergå till en alternativ region som en del av normal drift. Med två distributioner i separata regioner växlar du till exempel den alternativa regionen varje månad.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skapar batchkonton med [Azure-portalen,](batch-account-create-portal.md) [Azure CLI,](cli-samples.md) [PowerShell](batch-powershell-cmdlets-get-started.md)eller [Batch management API](batch-management-dotnet.md).
- Standardkvoter är associerade med ett batchkonto. [I](batch-quota-limit.md) den här artikeln beskrivs standardkvotvärdena och hur kvoterna kan ökas.
