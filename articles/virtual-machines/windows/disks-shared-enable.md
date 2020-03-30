---
title: Aktivera delade diskar för Azure-hanterade diskar
description: Konfigurera en Azure-hanterad disk med delade diskar (förhandsversion) så att du kan dela den mellan flera virtuella datorer
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471723"
---
# <a name="enable-shared-disk"></a>Aktivera delad disk

Den här artikeln beskriver hur du aktiverar funktionen delade diskar (förhandsversion) för Azure-hanterade diskar. Azure shared disks (preview) är en ny funktion för Azure-hanterade diskar som gör att du kan koppla en hanterad disk till flera virtuella datorer samtidigt. Genom att koppla en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure. 

Om du letar efter begreppsmässig information om hanterade diskar som har delade diskar aktiverade läser du [delade Azure-diskar](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]