---
title: Åtkomst till flera protokoll på Azure Data Lake Storage | Microsoft-dokument
description: Använd Blob API:er och program som använder Blob API:er med Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914866"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Åtkomst till flera protokoll på Azure Data Lake Storage

Blob API:er arbetar nu med konton som har ett hierarkiskt namnområde. Detta låser upp ekosystemet av verktyg, program och tjänster, samt flera Blob-lagringsfunktioner till konton som har ett hierarkiskt namnområde.

Tills nyligen kan du ha varit tvungen att underhålla separata lagringslösningar för lagring och analys av objekt. Det beror på att Azure Data Lake Storage Gen2 hade begränsat ekosystemstöd. Den hade också begränsad åtkomst till Blob-tjänstfunktioner som diagnostikloggning. En fragmenterad lagringslösning är svår att underhålla eftersom du måste flytta data mellan konton för att utföra olika scenarier. Du behöver inte längre göra det.

Med åtkomst till flera protokoll på DataSjölagring kan du arbeta med dina data med hjälp av ekosystemet för verktyg, program och tjänster. Detta inkluderar även verktyg och program från tredje part. Du kan peka dem på konton som har ett hierarkiskt namnområde utan att behöva ändra dem. Dessa program fungerar *som de* anropar Blob API:er, eftersom Blob API:er nu kan fungera på data i konton som har ett hierarkiskt namnområde.

Blob lagringsfunktioner som [diagnostikloggning,](../common/storage-analytics-logging.md) [åtkomstnivåer](storage-blob-storage-tiers.md)och [Blob lagring livscykelhanteringsprinciper](storage-lifecycle-management-concepts.md) fungerar nu med konton som har ett hierarkiskt namnområde. Därför kan du aktivera hierarkiska namnområden på dina blob Storage-konton utan att förlora åtkomsten till dessa viktiga funktioner. 

> [!NOTE]
> Multi-protokoll åtkomst på Data Lake Storage är allmänt tillgänglig och är tillgänglig i alla regioner. Vissa Azure-tjänster eller Blob-lagringsfunktioner som aktiveras av åtkomst till flera protokoll finns kvar i förhandsversionen.  I de här artiklarna sammanfattas det aktuella stödet för Blob-lagringsfunktioner och Azure-tjänstintegrationer. 
>
> [Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Så här fungerar åtkomst med flera protokoll på lagring av datasjöer

Blob API:er och Data Lake Storage Gen2 API:er kan fungera på samma data i lagringskonton som har ett hierarkiskt namnområde. Data Lake Storage Gen2 vägar Blob API:er via det hierarkiska namnområdet så att du kan få fördelarna med förstklassiga katalogåtgärder och POSIX-kompatibla åtkomstkontrollistor (ACL). 

![Flerprotokollsåtkomst på begreppsmässigt datasjölagring](./media/data-lake-storage-interop/interop-concept.png) 

Befintliga verktyg och program som använder Blob API får dessa fördelar automatiskt. Utvecklare behöver inte ändra dem. DataSjölagring Gen2 tillämpar konsekvent ACL:er på katalog- och filnivå oavsett vilket protokoll som verktyg och program använder för att komma åt data. 

## <a name="see-also"></a>Se även

- [Blob Storage-funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Kända problem med Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




