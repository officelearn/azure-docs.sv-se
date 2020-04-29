---
title: Video Indexer redundans och haveri beredskap
titleSuffix: Azure Media Services
description: Lär dig hur du växlar till ett sekundärt Video Indexer konto om ett fel eller haveri inträffar i ett regionalt Data Center.
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
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499618"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Video Indexer redundans och haveri beredskap

Azure Media Services Video Indexer ger inte direkt redundans för tjänsten om det uppstår ett avbrott i regionala Data Center eller fel. Den här artikeln förklarar hur du konfigurerar din miljö för redundans för att säkerställa optimal tillgänglighet för appar och minimerad återställnings tid om en katastrof inträffar.

Vi rekommenderar att du konfigurerar haveri beredskap för affärs kontinuitet (BCDR) i regionala par för att dra nytta av Azures isolerings-och tillgänglighets principer. Mer information finns i [Azure-kopplade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Krav

En Azure-prenumeration. Om du ännu inte har en Azure-prenumeration kan du registrera dig för en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Redundansväxla till ett sekundärt konto

Om du vill implementera BCDR måste du ha två Video Indexer-konton för att kunna hantera redundans.

1. Skapa två Video Indexer konton som är anslutna till Azure (se [skapa ett video Indexer konto](connect-to-azure.md)). Skapa ett konto för din primära region och det andra till den kopplade Azure-regionen.
1. Om det uppstår ett problem i din primära region växlar du till indexering med det sekundära kontot.

> [!TIP]
> Du kan automatisera BCDR genom att ställa in aktivitets logg aviseringar för meddelanden om tjänst hälsa enligt [skapa aktivitets logg aviseringar för tjänst meddelanden](../../service-health/alerts-activity-log-service-notifications.md).

Information om hur du använder flera klienter finns i [Hantera flera klienter](manage-multiple-tenants.md). Om du vill implementera BCDR väljer du något av följande två alternativ: [video Indexer konto per klient](manage-multiple-tenants.md#video-indexer-account-per-tenant) eller [Azure-prenumeration per klient](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Nästa steg

[Hantera ett video Indexer-konto som är anslutet till Azure](manage-account-connected-to-azure.md).
