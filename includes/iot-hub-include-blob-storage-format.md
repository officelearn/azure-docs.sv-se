---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73612183"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data kan skrivas till blob storage i antingen [Apache Avro-format,](https://avro.apache.org/) som är standard, eller JSON (förhandsgranskning). 
>    
> Möjligheten att koda JSON-format är i förhandsversion i alla regioner där IoT Hub är tillgängligt, utom östra USA, västra USA och Västeuropa. Kodningsformatet kan bara ställas in när blob storage-slutpunkten konfigureras. Formatet kan inte ändras för en slutpunkt som redan har ställts in. När du använder JSON-kodning måste du ange contentType till JSON och contentEncoding till UTF-8 i egenskaperna för meddelandesystemet. 
>
> Mer detaljerad information om hur du använder en blob storage-slutpunkt finns i [vägledningen om routning till lagring](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>