---
title: Notification Hubs integration med App Service Mobile Apps
description: Lär dig hur Azure Notification Hubs fungerar med Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e6c4fb767e6237f390cdb467b35c323f637bebf2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76264160"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integrering med Mobilappar i Apptjänst

För att underlätta för en sömlös och enhetlig användarupplevelse av alla Azure-tjänster har [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) inbyggt stöd för push-meddelanden som skickas via Notification Hubs. [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) är en mycket skalbar, globalt tillgänglig plattform för mobilappsutveckling tänkt att användas av utvecklare av företagsprogram och systemintegrerare. Den ger mobilutvecklare tillgång till ett stort utbud av funktioner.

Utvecklare av mobilappar kan utnyttja Notification Hubs med följande arbetsflöde:

1. Hämta PNS-handtag för enheter
2. Registrera enheten med Notification Hub via en praktisk registrering med API för klient-SDK:er i Mobile Apps

    > [!NOTE]
    > Observera att Mobile Apps av säkerhetsskäl tar bort alla taggar vid registreringen. Arbeta med Notification Hubs direkt från din serverdel för att associera taggar med enheter.

3. Skicka meddelanden från din apps serverdel med Notification Hubs

Här är några av de fördelar som utvecklarna får tack vare den här integreringen:

- **Mobile Apps Client-SDK:er**: Dessa SDK:er för flera plattformar ger dig enkla API:er för att registrera och tala med den meddelandehubb som har länkats till mobilappen automatiskt. Utvecklare behöver inte gräva djupt bland autentiseringsuppgifterna för Notification Hubs eller arbeta med en ytterligare tjänst.
  - *Skicka till användare*: SDK:erna taggar en given enhet automatiskt med ett autentiserat användar-ID för Mobile Apps och aktiverar på så vis ett scenario för push-till-användare.
  - *Skicka till enhet*: SDK:erna använder automatiskt installations-ID:t för Mobile Apps som GUID för att registrera med Notification Hubs vilket gör att utvecklarna slipper upprätthålla flera olika GUID-tjänster.
- **Installationsmodell**: Mobile Apps fungerar tillsammans med Notification Hubs senaste push-modell för att representera alla push-egenskaper som är kopplade till en enhet i en JSON-installation. Denna kopplas samman med Push Notification Services och är enkel att använda.
- **Flexibilitet**: Utvecklare kan alltid välja att arbeta direkt med Notification Hubs, även om integrationen redan är på plats.
- **Integrerad upplevelse i [Azure Portal](https://portal.azure.com)**: Push som en funktion är visuellt återgiven i Mobile Apps och utvecklarna kan enkelt arbeta med den associerade meddelandehubben via Mobile Apps.
