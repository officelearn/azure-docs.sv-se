---
title: "Azure filer skalbarhets- och prestandamål | Microsoft Docs"
description: "Läs mer om skalbarhets- och prestandamål för Azure-filer, inklusive kapacitet, förfrågningar och gränser för inkommande och utgående bandbredd."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 381e96a0a777415b916e4093fe55aa0d355782a1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhets- och prestandamål i Azure filer
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB standardprotokoll. Den här artikeln beskrivs skalbarhets- och prestandamål för Azure-filer och Azure filen Sync (förhandsversion).

Skalbarhets- och prestandamål som listas här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan genomströmning för en fil också begränsas av den tillgängliga nätverksbandbredden inte bara de servrar som är värd för tjänsten för Azure-filer. Vi rekommenderar starkt att testa din användningsmönstret och ta reda på om skalbarhet och prestanda för Azure-filer uppfyller dina krav. Vi arbetar också öka gränserna över tid. Du gärna ge oss feedback, antingen i kommentarerna nedan eller på den [Azure filer UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), om vilket begränsar du skulle vilja se oss öka.

## <a name="azure-storage-account-scale-targets"></a>Azure storage-konto skala mål
Den överordnade resursen till en filresurs i Azure är ett Azure storage-konto. Ett lagringskonto representerar lagringspoolen i Azure som kan användas av flera lagringstjänster, inklusive Azure-filer för att lagra data. Andra tjänster som lagrar data i storage-konton är Azure Blob storage, Azure Queue storage och Azure Table storage. Följande mål gäller alla storage-tjänster som lagrar data i ett lagringskonto:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Lagringsanvändningen konto från andra lagringstjänster påverkar din Azure-filresurser på ditt lagringskonto. Till exempel om du når den maximala lagringskapaciteten konto med Azure Blob storage kan kommer du inte att skapa nya filer på din Azure-filresurs, även om Azure-filresurs som är lägre än den maximala delade storleken.

## <a name="azure-files-scale-targets"></a>Azure filer skala mål
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure filsynkronisering skala mål
Med Azure filsynkronisering har vi försökt så mycket som möjligt att utforma för obegränsad användning, men detta inte är alltid möjligt. I tabellen nedan visar gränserna i våra tester och vilka mål är faktiskt gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Se även
- [Planera för distribution av en Azure-filer](storage-files-planning.md)
- [Planera för distribution av en Azure filsynkronisering](storage-sync-files-planning.md)
- [Mål för skalbarhet och prestanda för andra storage-tjänster](../common/storage-scalability-targets.md)