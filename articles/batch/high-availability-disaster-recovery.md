---
title: Hög tillgänglighet och haveri beredskap – Azure Batch
description: Lär dig hur du utformar ditt batch-program för ett regionalt avbrott. Arbets belastningar bör redundansväxla till en annan region eller delas upp mellan två eller flera regioner.
services: batch
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: jushiman
ms.openlocfilehash: 11546a6a664e6fb185a32d8e6cd593a850803fe8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930131"
---
# <a name="design-your-application-for-high-availability"></a>Utforma ditt program för hög tillgänglighet

Azure Batch är en regional tjänst. Batch är tillgängligt i alla Azure-regioner, men när ett batch-konto skapas måste det kopplas till en region. Alla åtgärder för batch-kontot gäller sedan för den regionen. Till exempel skapas pooler och associerade virtuella datorer i samma region som batch-kontot.

När du skapar ett program som använder batch måste du fundera över möjligheten för batch som inte är tillgänglig i en region. Det går att stöta på en sällsynt situation där det är problem med regionen som helhet, hela batch-tjänsten i regionen eller ett problem med ditt specifika batch-konto.

Om programmet eller lösningen som använder batch alltid måste vara tillgängliga, ska den vara utformad för att antingen redundansväxla till en annan region eller alltid ha arbets belastningen delad mellan två eller flera regioner. Båda metoderna kräver minst två batch-konton, där varje konto finns i en annan region.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Flera batch-konton i flera regioner

Genom att använda flera batch-konton i olika regioner kan du fortsätta att köra programmet om ett batch-konto i en annan region blir otillgängligt. Att använda flera konton är särskilt viktigt om ditt program behöver hög tillgänglighet.

I vissa fall kan ett program vara utformat för att alltid använda två eller flera regioner. Om du till exempel behöver en stor mängd kapacitet kan du behöva använda flera regioner för att hantera antingen ett storskaligt program eller för framtida tillväxt.

## <a name="design-considerations-for-providing-failover"></a>Design överväganden för att tillhandahålla redundans

En viktig punkt att tänka på när du ger möjlighet att redundansväxla till en alternativ region är att alla komponenter i en lösning måste beaktas. Det räcker inte bara att ha ett andra batch-konto. I de flesta batch-program krävs till exempel ett Azure Storage-konto, där lagrings kontot och batch-kontot måste finnas i samma region för acceptabla prestanda.

Tänk på följande när du skapar en lösning som kan redundansväxla:

- Skapa alla nödvändiga konton i varje region, till exempel batch-kontot och lagrings kontot. Det finns ofta ingen avgift för att skapa konton, endast när det finns lagrade data eller om kontot används.
- Se till att kvoterna är inställda på kontona i förväg, så att du kan allokera antalet kärnor som krävs med batch-kontot.
- Använd mallar och/eller skript för att automatisera distributionen av programmet i en region.
- Se till att programmets binärfiler och referens data är aktuella i alla regioner. Om du är uppdaterad kommer regionen att bli online snabbt utan att behöva vänta på uppladdning och distribution av filer. Om till exempel ett anpassat program som ska installeras på pooler lagras och refereras till med batch-programpaket, och om en ny version av programmet skapas, ska den överföras till varje batch-konto och refereras till av konfigurationen för poolen (eller gör den nya versionen till standard versionen.
- I det program som anropar batch, Storage och andra tjänster kan du enkelt överföra klienter eller belastningen till den andra regionen.
- En bra idé att se till att redundansväxlingen lyckas är att ofta överföra till en alternativ region som en del av normal drift. Till exempel, med två distributioner i olika regioner, växlar du till den alternativa regionen varje månad.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att skapa batch-konton med [Azure Portal](batch-account-create-portal.md), [Azure CLI](cli-samples.md), [PowerShell](batch-powershell-cmdlets-get-started.md)eller [batch Management-API: et](batch-management-dotnet.md).
- Standard kvoter är associerade med ett batch-konto. [den här artikeln](batch-quota-limit.md) beskriver standardvärdena för kvot och beskriver hur kvoterna kan ökas.
