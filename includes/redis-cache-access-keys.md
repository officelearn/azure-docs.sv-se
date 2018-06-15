---
title: ta med fil
description: ta med fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: d1ae8e5dfbb1455d639e3e2119a4606a8c3a0047
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196584"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Hämta värdnamn, portar och åtkomstnycklar via Azure Portal

När du ansluter till en Azure Redis-Cache-instans behöver cacheklienter värdnamnet, portarna och nycklarna för cacheminnet. Vissa klienter kan referera till de här objekten med något annorlunda namn. Du kan hämta den här informationen i Azure Portal.

För att hämta åtkomstnycklar via [Azure Portal](https://portal.azure.com), bläddrar du till ditt cacheminne och klickar på **Åtkomstnycklar**. 

![Redis-cachenycklar](media/redis-cache-access-keys/redis-cache-keys.png)

Om du vill hämta värdnamnet, portarna, klickar du på **Egenskaper**.

![Egenskaper för Redis-cache](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

