---
title: Dataplacering
description: Data placering och information om Azure Arc-aktiverade servrar.
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856457"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc-aktiverade servrar: data placering

I den här artikeln beskrivs begreppet data placering och hur det gäller Azure Arc-aktiverade servrar.

Azure Arc-aktiverade servrar **[finns i](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** **USA, Europa, Storbritannien, Australien och Asien och Stillahavsområdet**.

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