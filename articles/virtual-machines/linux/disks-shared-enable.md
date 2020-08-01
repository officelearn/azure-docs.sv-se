---
title: Aktivera delade diskar för Azure Managed disks
description: Konfigurera en Azure-hanterad disk med delade diskar så att du kan dela den över flera virtuella datorer
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cc365f6eef4da61ab7de5113b8f4b2b185360740
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460192"
---
# <a name="enable-shared-disk"></a>Aktivera delad disk

Den här artikeln beskriver hur du aktiverar funktionen delade diskar för Azure Managed disks. Azure delade diskar är en ny funktion för Azure Managed disks som gör att du kan ansluta en hanterad disk till flera virtuella datorer samtidigt. Genom att ansluta en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure. 

Om du vill ha konceptuell information om hanterade diskar som har delade diskar aktiverade, se [Azure delade diskar](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]