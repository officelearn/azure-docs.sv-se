---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219050"
---
Lokalt redundant lagring (LRS) replikerar dina data tre gånger i ett enda data Center. LRS tillhandahåller minst 99,999999999% (11 nio) objekts hållbarhet under ett angivet år. LRS är alternativet för replikering med lägst kostnad och ger den minsta hållbarhet jämfört med andra alternativ.

Om en katastrof på data center nivå (t. ex. Fire eller översvämning) inträffar kan alla repliker i ett lagrings konto med LRS gå förlorade eller inte kan återställas. För att minska den här risken rekommenderar Microsoft att använda zon-redundant lagring (ZRS), Geo-redundant lagring (GRS) eller geo-Zone-redundant lagring (GZRS).

En skrivbegäran till ett LRS-lagrings konto returneras bara efter att data har skrivits till alla tre repliker.

Du kanske vill använda LRS i följande scenarier:

* Om ditt program lagrar data som enkelt kan rekonstrueras om data går förlorade, kan du välja LRS.
* Vissa program är begränsade till att replikera data endast inom ett land/en region på grund av data styrnings krav. I vissa fall kan de kopplade regionerna som data replikeras för GRS-konton finnas i ett annat land/en annan region. Mer information om kopplade regioner finns i Azure- [regioner](https://azure.microsoft.com/regions/).
