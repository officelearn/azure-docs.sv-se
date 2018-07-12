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
ms.openlocfilehash: 4148370828f4ac2b7e75b49ed13cf1d1dafb8844
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724402"
---
### <a name="retrieve-host-name-ports-and-access-keys-by-using-the-azure-portal"></a>Hämta värdnamn, portar och åtkomstnycklar med hjälp av Azure Portal

När du ansluter till en Azure Redis-Cache-instans behöver cacheklienter värdnamnet, portarna och nycklarna för cacheminnet. Vissa klienter kan hänvisa till dessa objekt med namn som skiljer sig något. Du kan hämta den här informationen i Azure Portal.

#### <a name="to-retrieve-the-access-keys-and-host-name"></a>Hämta åtkomstnycklarna och värdnamnet

1. Om du vill hämta åtkomstnycklarna via [Azure Portal](https://portal.azure.com) bläddrar du till ditt cacheminne och väljer **Åtkomstnycklar**. 

    ![Azure Redis Cache-nycklar](media/redis-cache-access-keys/redis-cache-keys.png)

2. Om du vill hämta värdnamnen och portarna väljer du **Egenskaper**.

    ![Azure Redis Cache-egenskaper](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

