---
title: Så här migrerar du Azure API Management över flera regioner
description: Lär dig hur du migrerar en API Management instans från en region till en annan.
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
ms.openlocfilehash: 39803ab88e5b252ef119d22a697e135c83f87780
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205641"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Så här migrerar du Azure API Management över flera regioner
Om du vill migrera API Management instanser från en Azure-region till en annan, kan du använda funktionen [säkerhets kopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md) . Du bör välja samma API Management pris nivå i käll-och mål regionerna. 

> [!NOTE]
> Säkerhets kopiering och återställning fungerar inte när du migrerar mellan olika moln typer. För det måste du exportera resursen [som en mall](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal#export-resource-groups-to-templates). Sedan kan du anpassa den exporterade mallen för Azure-regionen och återskapa resursen. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Alternativ 1: Använd ett annat API Management instans namn

1. I mål regionen skapar du en ny API Management-instans med samma pris nivå som käll API Managements instansen. Den nya instansen bör ha ett annat namn. 
1. Säkerhetskopiera befintlig API Management instans till ett lagrings konto.
1. Återställ säkerhets kopian som skapades i steg 2 till den nya API Management-instansen som skapades i den nya regionen i steg 1.
1. Om du har en anpassad domän som pekar på käll regionen API Management instans, uppdaterar du den anpassade domänen CNAME så att den pekar på den nya API Management-instansen. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Alternativ 2: Använd samma API Management instans namn

> [!NOTE]
> Det här alternativet leder till stillestånds tid under migreringen.

1. Säkerhetskopiera API Management-instansen i käll regionen till ett lagrings konto.
1. Ta bort API Management i käll regionen. 
1. Skapa en ny API Management-instans i mål regionen med samma namn som den i käll regionen.
1. Återställ säkerhets kopian som skapades i steg 1 till den nya API Management-instansen i mål regionen.  


## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg
* Mer information om funktionen för säkerhets kopiering och återställning finns i [implementera haveri beredskap](api-management-howto-disaster-recovery-backup-restore.md).
* Information om hur du migrerar Azure-resurser finns i [Azures vägledning för migrering av flera regioner](https://github.com/Azure/Azure-Migration-Guidance).
* [Optimera och Spara på dina moln utgifter](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).