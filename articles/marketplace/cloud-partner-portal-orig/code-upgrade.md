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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776819"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Uppgradera kod till den senaste plattformen

Den här artikeln förklarar hur du uppgraderar dina Microsoft Dynamics 365 för åtgärder plattformsversion till den senaste versionen av plattformen.

## <a name="overview"></a>Översikt

Microsoft Dynamics 365 för åtgärder plattform består av följande komponenter:

Dynamics 365 för åtgärder plattform binärfiler, till exempel Server (AOS), data management framework, rapportering och business intelligence (BI) framework, utvecklingsverktyg och tjänster för analys. Följande paket för Application Object trädet (AOT):

1. Plattform för program
2. Application Foundation
3. Testa Essentials

**Viktiga**: Om du vill flytta till den senaste Dynamics 365 för åtgärder plattform, kan inte din Dynamics 365 för implementering av operations ha alla eventuella anpassningar (överlagring) av AOT-paket som hör till plattformen. Den här begränsningen introducerades i plattformsuppdatering 3, så att sömlös kontinuerliga uppdateringar kan göras på plattformen. Om du kör på en plattform som är äldre än plattform uppdatering 3, finns i avsnittet Uppgradera plattformen uppdatering 3 från en tidigare version-avsnittet i slutet av den här artikeln.

Mer information om uppgradering av koden finns [här](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).