---
title: Azure SaaS-program erbjudandet planer | Microsoft Docs
description: Skapa en plan för ett erbjudande för SaaS-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 0394fa759f4b6f6af59e075d9bc67668886d8075
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197290"
---
# <a name="saas-application-plans-tab"></a>Fliken för SaaS application planer

Använd fliken planer för att skapa en ny plan. Minst 1 plan måste läggas till om du använder sälj via Microsoft för din SaaS-app.

![Skapa en ny plan](./media/saas-plans-new-plan.png)

## <a name="create-a-new-plan"></a>Skapa en ny plan

Skapa en ny plan:

1. Under **planer**väljer **+ ny Plan**
2. I den **ny Plan** popup-fönstret anger du ett **Serviceplans-ID:**. Den här maxlängden är 50 tecken. Detta ID måste bestå enbart av gemener, alfanumeriska tecken, bindestreck eller understreck. Du kan inte ändra detta ID när erbjudandet är publicerat.
3. Välj **OK** att spara den som planerar-ID.

   ![Plan-ID för ny plan](./media/saas-plans-plan-ID.png)

### <a name="to-configure-the-plan"></a>Så här konfigurerar du planen:

1. Under **planera information**, ange information för följande fält:

   - Rubrik – ange ett namn på planen. Rubriken är begränsad till 50 tecken.
   - Beskrivning – ange en beskrivning. Beskrivningen är begränsad till upp till 500 tecken.
   - Är detta en privat plan? -Om planen är endast tillgängliga för en viss grupp med kunder kan välja **Ja**.
   - Land/Region tillgänglighet - planen måste vara tillgängliga för minst 1 land eller region. Klicka på **Välj regioner**. Välj ett land/region från den **Välj land/Region tillgänglighet** och sedan välja **OK**. 
   - Äldre priser – ange kostnaden i USD per månad.

2. Under **förenklad valuta priser**, ange följande information:

   - Fakturering termen - pris per månad är valt som standard. Du kan också ange årlig prissättning.
   - Månadspris - ange det månatliga priset som måste matcha äldre priser.

   >[!NOTE] 
   >Om du lägger till årlig pris fakturering termen kommer du frågas om den **årliga pris** i USD per år.

3. Välj **spara** att slutföra konfigurationen av planen.

   >[!NOTE] 
   >När du har sparat ändringarna prisnivå du kan exportera/importera prissättning data.

![Konfigurera information om supportavtal](./media/saas-plans-plan-details.png)

## <a name="next-steps"></a>Nästa steg

[Kanal-informationsfliken](./cpp-channel-info-tab.md)

