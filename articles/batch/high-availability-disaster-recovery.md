---
title: Hög tillgänglighet och disaster recovery - Azure Batch | Microsoft Docs
description: Lär dig hur du utformar ditt Batch-program för ett regionalt strömavbrott
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549760"
---
# <a name="design-your-application-for-high-availability"></a>Utforma ditt program för hög tillgänglighet

Azure Batch är en regional tjänst. Batch är tillgänglig i alla Azure-regioner, men när ett Batch-konto har skapats måste det vara associerad med en region. Alla åtgärder för Batch-kontot gäller sedan för den regionen. Till exempel skapas pooler och associerade virtuella datorer (VM) i samma region som Batch-kontot.

När du designar ett program som använder Batch, måste du överväga möjligheten att Batch som inte var tillgänglig i en region. Det är möjligt att hända att sällsynta där det finns ett problem med regionen som helhet, hela Batch-tjänsten i regionen eller ett problem med specifika Batch-kontot.

Om programmet eller lösningen med hjälp av Batch-alltid måste vara tillgänglig, kommer dess bör utformas för att växla över till en annan region eller alltid har den arbetsbelastning som delas upp mellan två eller fler regioner. Båda metoderna kräver minst två Batch-konton, med varje konto finns i en annan region.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Flera Batch-konton i flera regioner

Med flera Batch-konton i olika regioner gör möjligheten för dina program fortsätter köra om ett Batch-konto i en annan region blir otillgänglig. Använda flera konton är särskilt viktigt om ditt program måste ha hög tillgänglighet.

I vissa fall kan utformas ett program för att alltid använda två eller fler regioner. Exempelvis när du behöver mycket kapacitet kan med hjälp av flera regioner behövas för att hantera ett storskaligt program eller tillgodose för framtida tillväxt.

## <a name="design-considerations-for-providing-failover"></a>Designöverväganden för att tillhandahålla redundans

En viktig aspekt att tänka på när ger möjligheten att redundansväxla till en annan region är att alla komponenter i en lösning måste beaktas. Det räcker inte att bara ha ett andra Batch-konto. I de flesta Batch-program är till exempel ett Azure storage-konto krävs, med storage-konto och Batch-konto behöver finnas i samma region för acceptabel prestanda.

Tänk på följande när du utformar en lösning som kan redundansväxla:

- Skapa alla nödvändiga konton i varje region som Batch-konto och storage-konto i förväg. Det finns ofta inte någon avgift för med konton som har skapats, endast om det finns data som lagras eller konton som används.
- Kontrollera att kvoter är inställda på konton i tid, så du kan allokera det begärda antalet kärnor med hjälp av Batch-kontot.
- Använd mallar och/eller skript för att automatisera distributionen av programmet i en region.
- Håll programmets binärfiler och referensdata uppdaterad i alla regioner. Uppdaterad säkerställer regionen kan anslutas snabbt utan att behöva vänta på att ladda upp och distribuera filer. Till exempel om ett program att installera på poolnoder lagras och refereras till med hjälp av Batch-programpaket sedan när en ny version av programmet tillverkas bör vara överförs till varje Batch-konto och refereras till av poolkonfigurationen (eller se den nya versionen standardversionen).
- I programmet anropar Batch, lagring, och andra tjänster, enkelt övergången klienter eller belastningen till annan region.
- En bra idé att kontrollera att redundans kommer att lyckas är att vanliga övergången till en alternativ region som en del av normal drift. Till exempel med två distributioner i olika områden, övergång till Alternativ region varje månad.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du skapar Batch-konton med den [Azure-portalen](batch-account-create-portal.md), [Azure CLI](cli-samples.md), [Powershell](batch-powershell-cmdlets-get-started.md), eller [Batch-API för hantering](batch-management-dotnet.md).
- Standardkvoter som är associerade med ett Batch-konto; [i den här artikeln](batch-quota-limit.md) information standardkvoten standardvärden och beskriver hur kvoter kan ökas.
