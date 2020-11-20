---
title: Storlek på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Storlek på SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1750f1d61028cb186b02251b551b7a798e1df9d6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967541"
---
# <a name="sizing"></a>Storlekar

Storlek för den stora HANA-instansen skiljer sig från storlek för HANA i allmänhet. För befintliga och distribuerade system som du vill flytta från andra RDBMS till HANA tillhandahåller SAP ett antal rapporter som körs på dina befintliga SAP-system. Om databasen flyttas till HANA, kontrollerar de här rapporterna data och beräknar minnes kraven för HANA-instansen. Mer information om hur du kör dessa rapporter och skaffar de senaste korrigeringarna eller versionerna finns i följande SAP-anteckningar:

- [SAP-anteckning #1793345-storlek för SAP Suite på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP-anteckning #1872170-svit på HANA-och S/4 HANA-storleks rapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-anteckning #2121330 – vanliga frågor och svar: SAP BW på HANA storleks rapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP-anteckning #1736976 storleks rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP-anteckning #2296290-ny storleks rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

För gröna fält implementeringar kan SAP Quick Sizeer vara tillgängligt för att beräkna minnes kraven för implementeringen av SAP-programvara ovanpå HANA.

Minnes krav för HANA ökar när data volymen växer. Tänk på den aktuella minnes förbrukningen så att du kan förutsäga vad den kommer att vara i framtiden. Utifrån minnes kraven kan du mappa din efter frågan till en av de stora HANA-instanserna.

**Nästa steg**
- Se [onboarding-krav](hana-onboarding-requirements.md)