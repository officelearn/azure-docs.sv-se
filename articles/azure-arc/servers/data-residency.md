---
title: Dataplacering
description: Data placering och information om Azure Arc-aktiverade servrar.
ms.topic: reference
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 73acb07b8caa3c1368c936463f04969c29d37985
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327773"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc-aktiverade servrar: data placering

I den här artikeln beskrivs begreppet data placering och hur det gäller Azure Arc-aktiverade servrar.

Azure Arc-aktiverade servrar **[finns i](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** **USA, Europa, Storbritannien eller Asien och Stillahavsområdet**.

## <a name="data-residency"></a>Dataplacering

Azure Arc-aktiverade servrar lagrar konfigurations inställningar för [Azure VM-tillägg](manage-vm-extensions.md) (det vill säga egenskaps värden) tillägget kräver att du anger innan du försöker aktivera på den anslutna datorn. Om du till exempel aktiverar Log Analytics VM-tillägget uppmanas du att Log Analytics **arbetsyte-ID: t** och **primär nyckeln**.

Information om metadata om den anslutna datorn samlas också in. Specifikt:

* Namn och version för operativ system
* Datornamn
* Fullständigt kvalificerat domän namn (FQDN)
* Version av ansluten dator agent

Med ARC-aktiverade servrar kan du ange den region där dina data ska lagras. Microsoft kan replikeras till andra regioner för data återhämtning, men Microsoft replikerar eller flyttar inte data utanför geografien. Dessa data lagras i den region där Azure Arc-datorns resurs har kon figurer ATS. Om datorn till exempel är registrerad med ARC i regionen USA, östra, lagras dessa data i regionen USA.

Mer information om vår regionala stöd för återhämtning och efterlevnad finns i [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att utforma för [Azure-återhämtning](/azure/architecture/reliability/architect).