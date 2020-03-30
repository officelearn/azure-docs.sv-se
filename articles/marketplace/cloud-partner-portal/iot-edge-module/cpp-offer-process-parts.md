---
title: Översikt över Azure IoT Edge Module erbjuder publicering | Azure Marketplace
description: Översikt över processen för att publicera ett IoT Edge-modulerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: dsindona
ms.openlocfilehash: 6b44d9fd0bdd6e4b41013373472b5882a7ca1434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286599"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>IoT Edge-modul erbjuder publiceringsöversikt

<table> <tr> <td>I det här avsnittet beskrivs hur du publicerar ett nytt Azure IoT Edge-modulerbjudande till Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>. En IoT Edge-modul är en Docker-kompatibel behållare som är gjord för att köras på en IoT Edge-enhet. Azure IoT Edge-moduler är den minsta beräkningsenheten som hanteras av IoT Edge och kan innehålla Azure-tjänster eller anpassad lösningskod. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Publiceringsprocess

De stora stegen för att publicera ett IoT Edge-modulerbjudande är:

1. Skapa erbjudandet<br> Ge detaljerad information om erbjudandet. Den här informationen omfattar: erbjudandebeskrivning, marknadsföringsmaterial, supportinformation och tillgångsspecifikationer.

2. Skapa affärs- och tekniska tillgångar<br> Skapa affärstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen (IoT Edge-modulavbildningarna som finns i ett Azure-behållarregister.

3. Skapa SKU<br> Skapa de SKU:er som är associerade med erbjudandet. En unik SKU krävs för varje bild som du planerar att publicera.

4. Certifiera och publicera erbjudandet <br>När erbjudandet och de tekniska tillgångarna har slutförts kan du skicka erbjudandet. Den här inlämningen startar publiceringsprocessen. Under den här processen testas lösningen, valideras, certifieras och "publiceras" på Azure Marketplace.

## <a name="parts-of-an-offer"></a>Delar av ett erbjudande

Följande artiklar täcker de viktigaste delarna av ett IoT Edge-modulerbjudande.

- [Krav](./cpp-prerequisites.md) <br>I den här artikeln visas tekniska krav och affärskrav innan du kan skapa eller publicera ett IoT Edge-modulerbjudande.
- [Förbereda tekniska resurser för IoT Edge-modul](./cpp-create-technical-assets.md) <br>I den här artikeln beskrivs hur du förbereder de tekniska tillgångarna för en IoT Edge-modul. Dessa tillgångar måste uppfylla alla nödvändiga tekniska kriterier innan IoT Edge-modulen kan publiceras på Azure Marketplace.
- [Skapa ett erbjudande för IoT Edge-moduler](./cpp-create-offer.md) <br>I den här artikeln visas de steg som krävs för att skapa en ny IoT Edge-modulerbjudandepost med [Cloud Partner Portal](https://cloudpartner.azure.com).
- [Publicera erbjudande för IoT Edge-moduler](./cpp-publish-offer.md)<br> I den här artikeln beskrivs hur du skickar erbjudandet för publicering på Azure Marketplace.

## <a name="next-steps"></a>Nästa steg

Granska de [tekniska kraven och affärskraven](./cpp-prerequisites.md) för att publicera en IoT Edge-modul till Microsoft Azure Marketplace.
