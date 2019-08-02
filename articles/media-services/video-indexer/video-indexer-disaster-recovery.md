---
title: Video Indexer affärs kontinuitet och haveri beredskap (BCDR) – Azure | Microsoft Docs
description: Lär dig hur du växlar till ett sekundärt Video Indexer konto om det uppstår ett avbrott eller fel i ett regionalt Data Center.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ce788b4640f0a6c6f25b3280ce4f52fd018d1699
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668302"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Hantera Video Indexer verksamhets kontinuitet och haveri beredskap

Azure Media Services Video Indexer ger ingen omedelbar redundansväxling av tjänsten om det uppstår ett avbrott i regionala Data Center eller fel. Den här artikeln förklarar hur du konfigurerar din miljö för redundans för att säkerställa optimal tillgänglighet för program och minimerad återställnings tid om en katastrof inträffar.

Vi rekommenderar att du konfigurerar haveri beredskap för affärs kontinuitet (BCDR) i regionala par för att dra nytta av Azures isolerings-och tillgänglighets principer. Mer information finns i [Azure-kopplade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Förutsättningar 

En Azure-prenumeration. Om du ännu inte har en Azure-prenumeration kan du registrera dig för en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Redundansväxla till ett sekundärt konto

Du måste ha två Video Indexer-konton för att kunna hantera redundans för att implementera BCDR.

1. Skapa två Video Indexer konton som är anslutna till Azure (se [skapa konton](connect-to-azure.md)). En för din primära region och den andra till den kopplade Azure-regionen. 
1. Om det uppstår ett problem i din primära region växlar du till indexering med det sekundära kontot.

> [!TIP]
> Du kan automatisera BCDR genom att ställa in aktivitets logg aviseringar för meddelanden om tjänst hälsa enligt [skapa aktivitets logg aviseringar för tjänst meddelanden](../../service-health/alerts-activity-log-service-notifications.md).

Information om hur du använder flera klienter finns i [Hantera flera klienter](manage-multiple-tenants.md). Om du vill implementera BCDR väljer du något av följande två alternativ: [Video Indexer konto per klient](manage-multiple-tenants.md#video-indexer-account-per-tenant) eller [Azure-prenumeration per klient](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Nästa steg

[Hantera ett video Indexer-konto som är anslutet till Azure](manage-account-connected-to-azure.md).
