---
title: Vad är OPC-valv – Azure | Microsoft Docs
description: Översikt över OPC-valv
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99dfcaeb1ef5b52e6827f1b3ac65d6201557a8fb
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490384"
---
# <a name="what-is-opc-vault"></a>Vad är OPC-valv?

OPC-valv är en mikrotjänst som kan konfigurera, registrera och hantera livscykeln för användarcertifikatet för OPC UA-servern och klientprogram i molnet. Den här artikeln beskriver OPC-valvet enkel användningsfall.

## <a name="certificate-management"></a>Certifikathantering

Till exempel måste ett tillverkningsföretag ansluta sina OPC UA-serverdator till sina nybyggda klientprogram. När tillverkaren gör initial åtkomst för server-datorn, visas ett felmeddelande visas direkt på OPC UA-serverprogram som visar att klientprogrammet inte är säker. Den här mekanismen är inbyggd i OPC UA-serverdator kan förhindra att obehöriga programåtkomst, vilket förhindrar hårda kodar verkstadsgolvet.

## <a name="application-security-management"></a>Säkerhet för programhantering
En säkerhets-och professional använder OPC Vault mikrotjänster för att enkelt aktivera OPC UA-servern kan kommunicera med valfritt program, eftersom OPC-valvet har alla funktioner för certifikatet registret, lagring och livscykelhantering. OPC UA-servern är nu ansluten, kan den kommunicera till det nya inbyggda klientprogrammet

## <a name="the-complete-opc-vault-architecture"></a>Fullständig OPC Vault-arkitektur
Följande diagram illustrerar fullständig OPC Vault-arkitektur.

![Arkitektur för OPC-valv](media/overview-opc-vault-architecture/opc-vault.png)