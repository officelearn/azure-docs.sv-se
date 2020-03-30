---
title: Vad är OPC Vault - Azure | Microsoft-dokument
description: Den här artikeln innehåller en översikt över OPC Vault. Den kan konfigurera, registrera och hantera certifikatlivscykeln för OPC UA-program i molnet.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826204"
---
# <a name="what-is-opc-vault"></a>Vad är OPC Vault?

OPC Vault är en mikrotjänst som kan konfigurera, registrera och hantera certifikatlivscykeln för OPC UA-server- och klientprogram i molnet. I den här artikeln beskrivs OPC Vaults enkla användningsfall.

## <a name="certificate-management"></a>Certifikathantering

Ett tillverkningsföretag måste till exempel ansluta sin OPC UA-serverdator till sitt nybyggda klientprogram. När tillverkaren gör den första åtkomsten till serverdatorn visas ett felmeddelande omedelbart på OPC UA-serverprogrammet för att ange att klientprogrammet inte är säkert. Denna mekanism är inbyggd i OPC UA-servermaskinen för att förhindra obehörig åtkomst till program, vilket förhindrar ondhacking på verkstadsgolvet.

## <a name="application-security-management"></a>Hantering av programsäkerhet
En säkerhetsproffs använder OPC Vault microservice för att enkelt aktivera OPC UA-server för att kommunicera med alla klientprogram, eftersom OPC Vault har alla funktioner för certifikatregister-, lagrings- och livscykelhantering. Nu OPC UA-servern är säkert ansluten, kan den kommunicera med den nybyggda klientprogram

## <a name="the-complete-opc-vault-architecture"></a>Den kompletta OPC Vault-arkitekturen
Följande diagram illustrerar den fullständiga OPC Vault-arkitekturen.

![OPC Vault-arkitektur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC Vault och dess användningsområden, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [OPC Vault-arkitektur](overview-opc-vault-architecture.md)
