---
title: Aktivera delade diskar för Azure Managed disks
description: Konfigurera en Azure-hanterad disk med delade diskar (för hands version) så att du kan dela den över flera virtuella datorer
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970369"
---
# <a name="enable-shared-disk"></a>Aktivera delad disk

Den här artikeln beskriver hur du aktiverar funktionen delade diskar (för hands version) för Azure Managed disks. Azure Shared disks (för hands version) är en ny funktion för Azure Managed disks som gör att du kan ansluta en hanterad disk till flera virtuella datorer samtidigt. Genom att ansluta en hanterad disk till flera virtuella datorer kan du antingen distribuera nya eller migrera befintliga klustrade program till Azure. 

Om du vill ha konceptuell information om hanterade diskar som har delade diskar aktiverade, se [Azure delade diskar](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]