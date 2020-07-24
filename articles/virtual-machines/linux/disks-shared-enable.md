---
title: Aktivera delade diskar för Azure Managed disks
description: Konfigurera en Azure-hanterad disk med delade diskar så att du kan dela den över flera virtuella datorer
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/16/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e36c539cc1143490aeb4862c928589db5c502656
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080239"
---
# <a name="enable-shared-disk"></a>Aktivera delad disk

Den här artikeln beskriver hur du aktiverar funktionen delade diskar för Azure Managed disks. Azure delade diskar är en ny funktion för Azure Managed disks som gör att du kan ansluta en hanterad disk till flera virtuella datorer samtidigt. Genom att ansluta en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure. 

Om du vill ha konceptuell information om hanterade diskar som har delade diskar aktiverade, se [Azure delade diskar](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]