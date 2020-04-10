---
title: Aktivera delade diskar för Azure-hanterade diskar
description: Konfigurera en Azure-hanterad disk med delade diskar (förhandsversion) så att du kan dela den mellan flera virtuella datorer
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1cb581f0914dbafff4a46510924115e82e42b990
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011148"
---
# <a name="enable-shared-disk"></a>Aktivera delad disk

Den här artikeln beskriver hur du aktiverar funktionen delade diskar (förhandsversion) för Azure-hanterade diskar. Azure shared disks (preview) är en ny funktion för Azure-hanterade diskar som gör att du kan koppla en hanterad disk till flera virtuella datorer samtidigt. Genom att koppla en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure. 

Om du letar efter begreppsmässig information om hanterade diskar som har delade diskar aktiverade läser du [delade Azure-diskar](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]