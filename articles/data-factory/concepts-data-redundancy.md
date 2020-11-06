---
title: Dataredundans i Azure Data Factory | Microsoft Docs
description: Lär dig mer om meta-dataredundans i Azure Data Factory
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: f71fdf66624d67939f915f91c2cc1dbe7553cad7
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332119"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory dataredundans**

Azure Data Factory data innehåller metadata (pipeline, data uppsättningar, länkade tjänster, integrations körning och utlösare) och övervaknings data (pipeline, utlösare och aktivitets körningar). 

I alla regioner (förutom södra Brasilien och Sydostasien), lagras och replikeras Azure Data Factory data i den [kopplade regionen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#azure-regional-pairs) för att skydda mot metadata förlust. Under regionala Data Center fel kan Microsoft initiera en regional redundansväxling av Azure Data Factory-instansen. I de flesta fall krävs ingen åtgärd på din sida. När den Microsoft-hanterade redundansväxlingen har slutförts kommer du att kunna komma åt din Azure Data Factory i området för redundans. 

På grund av krav på data placering i Brasilien, och Sydostasien, lagras Azure Data Factory data endast i den [lokala regionen](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage). För Sydostasien lagras alla data i Singapore. För södra Brasilien lagras alla data i Brasilien. När regionen förloras på grund av en betydande katastrof kommer Microsoft inte att kunna återställa dina Azure Data Factory data.  

> [!NOTE]
> Microsoft-hanterad redundans gäller inte för integration runtime med egen värd (SHIR) eftersom den här infrastrukturen vanligt vis hanteras av kund. Om SHIR har kon figurer ATS på den virtuella Azure-datorn är rekommendationen att använda [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) för att hantera den [virtuella Azure-redundansväxlingen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture) till en annan region.



## <a name="using-source-control-in-azure-data-factory"></a>**Använda käll kontroll i Azure Data Factory**

För att säkerställa att du kan spåra och granska ändringar som gjorts i dina Azure Data Factory-metadata, bör du överväga att konfigurera käll kontroll för Azure Data Factory. Du kan också få åtkomst till dina JSON-filer för pipelines, data uppsättningar, länkade tjänster och utlösare. Med Azure Data Factory kan du arbeta med olika git-lagringsplatser (Azure DevOps och GitHub). 

 Lär dig hur du konfigurerar [käll kontroll i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control). 

> [!NOTE]
> I händelse av en katastrof (förlust av region) kan en ny data fabrik tillhandahållas manuellt eller på ett automatiserat sätt. När den nya data fabriken har skapats kan du återställa pipelines, data uppsättningar och länkade tjänster JSON från den befintliga git-lagringsplatsen. 



## <a name="data-stores"></a>**Datalager**

Med Azure Data Factory kan du flytta data mellan data lager som finns lokalt och i molnet. För att säkerställa affärs kontinuiteten med dina data lager bör du läsa rekommendationer för affärs kontinuitet för var och en av dessa data lager. 

 

## <a name="see-also"></a>Se även

- [Regionala Azure-par](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Data placering i Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) 
