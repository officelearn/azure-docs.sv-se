---
title: Storlek för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Storlek av SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616898"
---
# <a name="sizing"></a>Storlekar

Storlek för HANA Large Instance skiljer sig inte från storlek för HANA i allmänhet. För befintliga och distribuerade system som du vill flytta från andra RDBMS till HANA tillhandahåller SAP ett antal rapporter som körs på dina befintliga SAP-system. Om databasen flyttas till HANA kontrollerar dessa rapporter data och beräknar minneskraven för HANA-instansen. Mer information om hur du kör dessa rapporter och hämtar de senaste korrigeringsfilerna eller versionerna finns i följande SAP Notes:

- [SAP Note #1793345 - Storlek för SAP Suite på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite på HANA och S/4 HANA storleksrapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - Vanliga frågor och svar: SAP BW på HANA storleksrapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Storleksrapport för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Ny storleksrapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

För implementeringar av grönt fält är SAP Quick Sizer tillgänglig för att beräkna minneskrav för implementering av SAP-programvara ovanpå HANA.

Minneskraven för HANA ökar i takt med att datavolymen växer. Var medveten om din nuvarande minnesförbrukning för att hjälpa dig att förutsäga vad det kommer att bli i framtiden. Baserat på minneskrav kan du sedan mappa din efterfrågan till en av DE STORA FÖREKOMSTERNA för stora instanser.

**Nästa steg**
- Se [krav på introduktion](hana-onboarding-requirements.md)