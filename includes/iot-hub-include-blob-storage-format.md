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
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630951"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data kan skrivas till blob-lagring antingen den [Apache Avro](https://avro.apache.org/) format, vilket är standard eller JSON (förhandsversion). 
>    
> Möjligheten att koda JSON-format finns i förhandsversion i alla regioner där IoT Hub är tillgänglig, förutom östra USA, västra USA och Västeuropa. Kodningsformatet kan bara anges när blob storage-slutpunkten har konfigurerats. Formatet kan inte ändras för en slutpunkt som redan har ställts in. När du använder JSON-kodning, måste du ange contentType till JSON och contentEncoding till UTF-8 i Systemegenskaper meddelande. 
>
> Mer detaljerad information om hur du använder en slutpunkt för blob-lagring finns [vägledning för routning till blob storage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>