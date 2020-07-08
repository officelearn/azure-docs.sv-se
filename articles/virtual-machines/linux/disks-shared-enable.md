---
title: Aktivera delade diskar för Azure Managed disks
description: Konfigurera en Azure-hanterad disk med delade diskar (för hands version) så att du kan dela den över flera virtuella datorer
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3b949905f311b1a8878aa691e32abc3f568e1e6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84657284"
---
# <a name="enable-shared-disk"></a>Aktivera delad disk

Den här artikeln beskriver hur du aktiverar funktionen delade diskar (för hands version) för Azure Managed disks. Azure Shared disks (för hands version) är en ny funktion för Azure Managed disks som gör att du kan ansluta en hanterad disk till flera virtuella datorer samtidigt. Genom att ansluta en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure. 

Om du vill ha konceptuell information om hanterade diskar som har delade diskar aktiverade, se [Azure delade diskar](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]