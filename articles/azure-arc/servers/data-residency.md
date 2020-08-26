---
title: Dataplacering
description: Data placering och information om Azure Arc-aktiverade servrar (för hands version).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 028398c27dde9760192d366b9e8a91dca8e429cf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861222"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Azure Arc-aktiverade servrar (för hands version): data placering

I den här artikeln beskrivs begreppet data placering och hur det gäller för Azure Arc-aktiverade servrar (för hands version).

Azure Arc-aktiverade servrar (för hands version) är **[tillgängliga i för hands versionen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** i **USA, Europa eller Asien och Stillahavsområdet**.

## <a name="data-residency"></a>Dataplacering

Azure Arc-aktiverade servrar (för hands version) lagra konfigurations inställningar för [Azure VM-tillägg](manage-vm-extensions.md) (det vill säga egenskaps värden) tillägget kräver att du anger innan du försöker aktivera på den anslutna datorn. Om du till exempel aktiverar Log Analytics VM-tillägget uppmanas du att Log Analytics **arbetsyte-ID: t** och **primär nyckeln**.

Information om metadata om den anslutna datorn samlas också in. Specifikt:

* Namn och version för operativ system
* Datornamn
* Fullständigt kvalificerat domän namn (FQDN)
* Version av ansluten dator agent

Med ARC-aktiverade servrar (för hands version) kan du ange den region där dina data ska lagras. Microsoft kan replikeras till andra regioner för data återhämtning, men Microsoft replikerar eller flyttar inte data utanför geografien. Dessa data lagras i den region där Azure Arc-datorns resurs har kon figurer ATS. Om datorn till exempel är registrerad med ARC i regionen USA, östra, lagras dessa data i regionen USA.

Mer information om vår regionala stöd för återhämtning och efterlevnad finns i [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att utforma för [Azure-återhämtning](/architecture/reliability/architect).