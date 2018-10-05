---
title: Uppgradera kod till den senaste plattformen | Microsoft Docs
description: Det här avsnittet förklarar hur du uppgraderar dina Microsoft Dynamics 365 för åtgärder plattformsversion till den senaste versionen av plattformen
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811613"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Uppgradera kod till den senaste plattformen

Den här artikeln förklarar hur du uppgraderar dina Microsoft Dynamics 365 för åtgärder plattformsversion till den senaste versionen av plattformen.

## <a name="overview"></a>Översikt

Microsoft Dynamics 365 för åtgärder plattform består av följande komponenter:

Dynamics 365 för åtgärder plattform binärfiler, till exempel Server (AOS), data management framework, rapportering och business intelligence (BI) framework, utvecklingsverktyg och tjänster för analys. Följande paket för Application Object trädet (AOT):

1. Plattform för program
2. Programmet Foundation
3. Testa Essentials

**Viktiga**: Flytta till den senaste Dynamics 365 för Operations-plattformen genom din Dynamics 365 för implementering av operations kan inte ha alla eventuella anpassningar (overlayering) av AOT-paket som hör till plattformen. Den här begränsningen introducerades i plattformsuppdatering 3, så att sömlös kontinuerliga uppdateringar kan göras på plattformen. Om du kör på en plattform som är äldre än plattform uppdatering 3, finns i avsnittet Uppgradera plattformen uppdatering 3 från en tidigare version-avsnittet i slutet av den här artikeln.

Mer information om uppgradering av koden finns [här](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).