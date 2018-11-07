---
title: Azure IoT Edge-modul-erbjudande publicera översikt | Microsoft Docs
description: Översikt över processen för att publicera en IoT Edge-modul erbjuder på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
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
ms.date: 11/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5b4d4471d9c77b5d13dfd5f8c2e9394b1c2d2a87
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242561"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge-modul erbjuder översikt över publicering

<table> <tr> <td>Det här avsnittet beskrivs hur du publicerar ett nytt erbjudande för Azure IoT Edge-modul för Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>. En IoT Edge-modul är en Docker-kompatibla behållare som har gjorts att köra på en IoT Edge-enhet. Azure IoT Edge-moduler är den minsta beräkningsenhet hanteras av IoT Edge och kan innehålla Azure-tjänster eller anpassad lösning kod. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Process för att publicera

Anvisningar för att publicera ett erbjudande för IoT Edge-modulen är:

1. Skapa erbjudandet<br> Innehåller detaljerad information om erbjudandet. Informationen omfattar: erbjudandet beskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.

2. Skapa affärs- och tekniska resurser<br> Skapa företagstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen (IoT Edge-modulen avbildningarna i Azure Container Registry.

3. Skapa SKU: N<br> Skapa den SKU: er som är kopplad till erbjudandet. En unik SKU måste anges för varje bild som du planerar att publicera.

4. Certifiera och publicera erbjudandet <br>När erbjudandet och tekniska resurser har slutförts, kan du skicka erbjudandet. Den här publiceringen börjar publiceringsprocessen. Under den här processen testas lösningen verifierats, certifierade, sedan ”lanseras” på Azure Marketplace.

## <a name="parts-of-an-offer"></a>Delar av ett erbjudande

I följande artiklar beskriver de viktigaste delarna av ett erbjudande för IoT Edge-modulen.

- [Förutsättningar](./cpp-prerequisites.md) <br>Den här artikeln innehåller tekniska och företagets krav innan du kan skapa eller publicera en IoT Edge-modul erbjuder.
- [Förbereda IoT Edge-modulen tekniska resurser](./cpp-create-technical-assets.md) <br>Den här artikeln beskriver hur du förbereder tekniska resurser för en IoT Edge-modul. Dessa resurser måste uppfylla alla nödvändiga tekniska villkor innan IoT Edge-modul kan publiceras på Azure Marketplace.
- [Skapa ett erbjudande för IoT Edge-moduler](./cpp-create-offer.md) <br>Den här artikeln innehåller anvisningar för att skapa en ny IoT Edge modulen erbjudandet posten med den [Cloud Partner Portal](https://cloudpartner.azure.com).
- [Publicera erbjudande för IoT Edge-moduler](./cpp-publish-offer.md)<br> Den här artikeln beskriver hur du skickar in erbjudande för publicering på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Granska den [tekniska krav och affärskrav](./cpp-prerequisites.md) för att publicera en IoT Edge-modul i Microsoft Azure Marketplace.
