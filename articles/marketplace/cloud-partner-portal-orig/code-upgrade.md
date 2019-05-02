---
title: Uppgradera kod till den senaste plattformen | Azure Marketplace
description: Det här avsnittet förklarar hur du uppgraderar dina Microsoft Dynamics 365 för åtgärder plattformsversion till den senaste versionen av plattformen
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935289"
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