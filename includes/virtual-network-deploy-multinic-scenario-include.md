---
title: ta med fil
description: ta med fil
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743277"
---
## <a name="scenario"></a>Scenario
Det här dokumentet beskriver en distribution som använder flera nätverkskort i virtuella datorer i ett specifikt scenario. I det här scenariot har du en två skikt IaaS-arbetsbelastning i Azure. Varje nivå har distribuerats i ett eget undernät i ett virtuellt nätverk (VNet). Klientdelsnivån består av flera webbservrar, grupperas tillsammans i en belastningsutjämningsuppsättning för hög tillgänglighet. Backend-nivå består av flera databasservrar. Database-servrar distribueras med två nätverkskort, ett för databasåtkomst för hantering. Scenariot omfattar också Nätverkssäkerhetsgrupper (NSG) till att styra vilken trafik till varje undernät och nätverkskort i distributionen. Följande bild visar grundläggande arkitekturen för det här scenariot:

![MultiNIC scenario](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)