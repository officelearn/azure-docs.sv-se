---
title: ta med fil
description: ta med fil
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678318"
---
### <a name="set-up-pagination"></a>Ställ in sidbrytning

För vissa kopplingar och åtgärder som hämtar flera objekt kan resultatet överskrider kopplingens standardstorlek för sidan. I det här fallet returnerar åtgärden den första sidan av resultaten. Till exempel standard sidstorleken för den **SQL Server - Get rader** åtgärd är 2048, men kan variera beroende på andra inställningar. Om du vill se till att du får alla poster, aktivera den **sidbrytning** för åtgärden. Den här inställningen har logikappen be anslutningen för de återstående posterna, men returnerar alla resultat som ett enda meddelande när åtgärden är klar. 

Här följer några kopplingar där du kan aktivera sidbrytning för specifika åtgärder: 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Här är ett exempel på den **hämta rader** åtgärd:

1. För att hitta om åtgärden stöder sidbrytning, öppnar du åtgärdens **inställningar**. 

   ![På åtgärden, öppna ”inställningar”](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Om åtgärden stöder sidbrytning, ändrar du den **sidbrytning** från **av** till **på**. Se till att åtgärden returnerar en minsta uppsättning resultat genom att ange ett värde för **gränsen**.

   ![Ange att åtgärden ska returnera ett minsta antal resultat](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. När du är klar kan du välja **klar**.