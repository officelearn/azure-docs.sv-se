---
title: Aktivera och arbeta med Diagnostikloggar för Azure Bastion
description: I den här artikeln kan du lära dig hur du aktiverar och arbetar med Diagnostikloggar för Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 97f0cdb1e93ef2ad06d2daa04b2f4893fd5dfac2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619269"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Aktivera och arbeta med Bastion-diagnostikloggar

När användare ansluter till arbetsbelastningar med Azure Bastion kan Bastion logga diagnostik av fjärrsessioner. Du kan sedan använda diagnostiken för att visa vilka användare som är anslutna till vilka arbetsbelastningar, vid vilken tidpunkt, varifrån och annan sådan relevant loggningsinformation. För att kunna använda diagnostiken måste du aktivera diagnostikloggar på Azure Bastion. Den här artikeln hjälper dig att aktivera diagnostikloggar och sedan visa loggarna.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Aktivera diagnostikloggen

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din Azure Bastion-resurs och väljer **diagnostikinställningar** från sidan Azure Bastion.

   ![inställningar för diagnostik](./media/diagnostic-logs/1diagnostics-settings.png)
2. Välj **Diagnostikinställningar**och välj sedan **+Lägg till diagnostikinställning** för att lägga till ett mål för loggarna.

   ![lägga till diagnostikinställning](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. På sidan Inställningar för diagnostik väljer du vilken typ av **lagringskonto** som ska användas för lagring av diagnostikloggar.

   ![välj lagringsplats](./media/diagnostic-logs/3add-storage-account.png)
4. När du har slutfört inställningarna ser det ut ungefär som det här exemplet:

   ![exempelinställningar](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Visa diagnostiklogg

För att komma åt diagnostikloggarna kan du direkt använda det lagringskonto som du angav när diagnostikinställningarna aktiverades.

1. Navigera till lagringskontoresursen och sedan till **Behållare**. Du ser blob-bloben **insights-logs-bastionauditlogs** som skapats i blob-behållaren för lagringskonto.

   ![inställningar för diagnostik](./media/diagnostic-logs/1-navigate-to-logs.png)
2. När du navigerar till i behållaren visas olika mappar i bloggen. Dessa mappar anger resurshierarkin för din Azure Bastion-resurs.

   ![lägga till diagnostikinställning](./media/diagnostic-logs/2-resource-h.png)
3. Navigera till hela hierarkin för din Azure Bastion-resurs vars diagnostikloggar du vill komma åt/visa. "y=", "m=", "d=", 'h=' och 'm=' anger år, månad, dag, timme respektive minut för diagnostikloggarna.

   ![välj lagringsplats](./media/diagnostic-logs/3-resource-location.png)
4. Leta reda på den json-fil som skapats av Azure Bastion och som innehåller diagnostikloggdata för den tidsperiod som navigeras till.

5. Hämta json-filen från lagringsblobbbehållaren. En exempelpost från json-filen visas nedan som referens:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Nästa steg

Läs [Vanliga frågor om Bastion](bastion-faq.md).
