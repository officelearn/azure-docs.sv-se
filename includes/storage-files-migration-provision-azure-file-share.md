---
title: Överväganden för etablering av Azure-filresurser.
description: Etablera Azure-filresurser för användning med Azure File Sync. Ett vanligt textblock som delas mellan migreringsdokument.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209433"
---
En Azure-filresurs lagras i molnet i ett Azure-lagringskonto.
Det finns en annan nivå av prestanda överväganden här.

Om du har mycket aktiva resurser – resurser som används av många användare och/eller program kan två Azure-filresurser nå prestandagränsen för ett lagringskonto.

Bästa praxis är att distribuera lagringskonton med en filresurs vardera.
Du kan slå samman flera Azure-filresurser i samma lagringskonto, om du har arkiveringsresurser eller om du förväntar dig låg daglig aktivitet i dem.

Dessa överväganden gäller mer för direkt molnåtkomst (via en Virtuell Azure- än de gäller för Azure File Sync. Om du planerar att bara använda Azure File Sync på dessa resurser är det bra att gruppera flera i ett enda Azure-lagringskonto.

Om du har gjort en lista över dina resurser bör du mappa varje resurs till det lagringskonto som de kommer att finnas i.

I föregående fas har du fastställt lämpligt antal aktier. I det här steget har du skapat en mappning av lagringskonton till filresurser. Distribuera det nu lämpliga antalet Azure-lagringskonton med lämpligt antal Azure-filresurser i dem.

Kontrollera att regionen för vart och ett av dina lagringskonton är densamma och matchar regionen för den lagringssynkroniseringstjänstresurs som du redan har distribuerat.

> [!CAUTION]
> Om du skapar en 100 TiB-gräns Azure-filresurs kan den resursen endast använda lokalt redundanta lagrings- eller zon redundanta lagringsredundansalternativ. Tänk på dina behov av lagringsredundans innan du använder 100 TiB-filresurser.

Azure-filresurser skapas fortfarande med en 5 TiB-gräns som standard. Eftersom du skapar nya lagringskonton, se till att följa [vägledningen för att skapa lagringskonton som tillåter Azure-filresurser med 100 TiB-gränser](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

En annan faktor när du distribuerar ett lagringskonto är redundansen för din Azure-lagring. Se: [Azure Storage redundansalternativ](../articles/storage/common/storage-redundancy.md).

Namnen på dina resurser är också viktiga. Om du till exempel grupperar flera resurser för HR-avdelningen i ett Azure-lagringskonto bör du namnge lagringskontot på lämpligt sätt. På samma sätt bör du när du namnger dina Azure-filresurser använda namn som liknar de som används för deras lokala motsvarigheter.