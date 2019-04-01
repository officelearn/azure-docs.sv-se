---
title: Vad är Azure IoT OPC UA-certifikathantering | Microsoft Docs
description: Översikt över OPC-valv
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759316"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Vad är Azure IoT öppen plattform kommunikation (OPC) UA certifikathantering?

Azure IoT OPC UA certifikathantering, även kallade OPC-valv är en mikrotjänst som kan konfigurera registreras, och hantera certifikatets livscykel för OPC UA-servern och klienten program i molnet. Den här artikeln beskriver OPC-valvet enkel användningsfall.

## <a name="certificate-management"></a>Certifikathantering

Till exempel måste ett tillverkningsföretag ansluta sina OPC UA-serverdator till sina nybyggda klientprogram. När tillverkaren gör initial åtkomst för server-datorn, visas ett felmeddelande visas direkt på OPC UA-serverprogram som visar att klientprogrammet inte är säker. Den här mekanismen är inbyggd i OPC UA-serverdator kan förhindra att obehöriga programåtkomst, vilket förhindrar hårda kodar verkstadsgolvet.

## <a name="application-security-management"></a>Säkerhet för programhantering
En säkerhets-och professional använder OPC Vault mikrotjänster för att enkelt aktivera OPC UA-servern kan kommunicera med valfritt program, eftersom OPC-valvet har alla funktioner för certifikatet registret, lagring och livscykelhantering. OPC UA-servern är nu ansluten, kan den kommunicera till det nya inbyggda klientprogrammet

## <a name="the-complete-opc-vault-architecture"></a>Fullständig OPC Vault-arkitektur
Följande diagram illustrerar fullständig OPC Vault-arkitektur.

![Arkitektur för OPC-valv](media/overview-opc-vault-architecture/opc-vault.png)