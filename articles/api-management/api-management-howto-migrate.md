---
title: Migrera Azure API Management mellan regioner
description: Lär dig hur du migrerar en API Management-instans från en region till en annan.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 3294a7b2112e9527041ef343f4452aedb7a2a272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073470"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Migrera Azure API Management mellan regioner
Om du vill migrera API Management-instanser från en Azure-region till en annan kan du använda [funktionen för säkerhetskopiering och återställning.](api-management-howto-disaster-recovery-backup-restore.md) Du bör välja samma API Management-prisnivå i käll- och målområdena. 

> [!NOTE]
> Säkerhetskopiering och återställning fungerar inte när du migrerar mellan olika molntyper. För det måste du exportera resursen [som en mall](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Anpassa sedan den exporterade mallen för azure-regionen för målet och återskapa resursen. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Alternativ 1: Använda ett annat API Management-förekomstnamn

1. Skapa en ny API Management-instans med samma prisnivå som käll-API Management-instansen i målregionen. Den nya instansen bör ha ett annat namn. 
1. Säkerhetskopiera befintlig API Management-instans till ett lagringskonto.
1. Återställ säkerhetskopian som skapats i steg 2 till den nya API Management-instansen som skapats i den nya regionen i steg 1.
1. Om du har en anpassad domän som pekar på källregion API Management-instansen uppdaterar du den anpassade domänen CNAME så att den pekar på den nya API Management-instansen. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Alternativ 2: Använd samma API Management-instansnamn

> [!NOTE]
> Det här alternativet resulterar i driftstopp under migreringen.

1. Säkerhetskopiera API Management-instansen i källregionen till ett lagringskonto.
1. Ta bort API-hanteringen i källregionen. 
1. Skapa en ny API Management-instans i målregionen med samma namn som den i källregionen.
1. Återställ säkerhetskopian som skapats i steg 1 till den nya API Management-instansen i målregionen.  


## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg
* Mer information om säkerhetskopierings- och återställningsfunktionen finns i [hur du implementerar haveriberedskap](api-management-howto-disaster-recovery-backup-restore.md).
* Information om migrering Azure-resurser finns i [Azure-vägledning för migrering mellan regioner](https://github.com/Azure/Azure-Migration-Guidance).