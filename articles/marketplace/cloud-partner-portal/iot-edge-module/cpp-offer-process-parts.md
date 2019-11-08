---
title: Översikt över publicering av Azure IoT Edge-modulen | Azure Marketplace
description: Översikt över processen för att publicera ett IoT Edge-modul erbjudande på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pabutler
ms.openlocfilehash: 97df9a61d15e0d90e81f42cef327aea23873ffa0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814328"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Översikt över publicering av IoT Edge-modulen

<table> <tr> <td>Det här avsnittet beskriver hur du publicerar ett nytt Azure IoT Edge modul-erbjudande till Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>. En IoT Edge modul är en Docker-kompatibel behållare som görs för körning på en IoT Edge enhet. Azure IoT Edge moduler är den minsta beräknings enheten som hanteras av IoT Edge och kan innehålla Azure-tjänster eller anpassad lösnings kod. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Publicerings process

De övergripande stegen för att publicera ett IoT Edge modul-erbjudande är:

1. Skapa erbjudandet<br> Ange detaljerad information om erbjudandet. Den här informationen omfattar: Beskrivning av erbjudandet, marknadsförings material, supportinformation och till gångs uppgifter.

2. Skapa affärs-och tekniska till gångar<br> Skapa företags till gångar (juridiska dokument och marknadsförings material) och tekniska till gångar för den associerade lösningen (IoT Edge modul-avbildningar som finns i en Azure Container Registry.

3. Skapa SKU: n<br> Skapa de SKU: er som är associerade med erbjudandet. En unik SKU krävs för varje avbildning som du planerar att publicera.

4. Certifiera och publicera erbjudandet <br>När erbjudandet och de tekniska till gångarna har slutförts kan du skicka erbjudandet. Den här sändningen startar publicerings processen. Under den här processen testas lösningen, verifieras, certifieras, sedan "går live" på Azure Marketplace.

## <a name="parts-of-an-offer"></a>Delar av ett erbjudande

I följande artiklar beskrivs viktiga delar av ett IoT Edge-modulens erbjudande.

- [Krav](./cpp-prerequisites.md) <br>I den här artikeln anges de tekniska och affärs kraven innan du kan skapa eller publicera ett erbjudande för IoT Edge-modulen.
- [Förbered IoT Edge modul tekniska till gångar](./cpp-create-technical-assets.md) <br>Den här artikeln beskriver hur du förbereder de tekniska till gångarna för en IoT Edge-modul. Dessa till gångar måste uppfylla alla nödvändiga tekniska kriterier innan IoT Edge-modulen kan publiceras på Azure Marketplace.
- [Skapa ett erbjudande för IoT Edge-moduler](./cpp-create-offer.md) <br>Den här artikeln innehåller de steg som krävs för att skapa en ny IoT Edge-modul för erbjudande post med hjälp av [Cloud Partner Portal](https://cloudpartner.azure.com).
- [Publicera erbjudande för IoT Edge-moduler](./cpp-publish-offer.md)<br> Den här artikeln beskriver hur du skickar in erbjudandet om publicering på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Granska de [tekniska och affärs kraven](./cpp-prerequisites.md) för att publicera en IoT Edge-modul till Microsoft Azure Marketplace.
