---
title: Video Indexer redundans och haveriberedskap
titleSuffix: Azure Media Services
description: Lär dig hur du redundans till ett sekundärt videoindexerkonto om ett regionalt datacenterfel eller en katastrof inträffar.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499618"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Video Indexer redundans och haveriberedskap

Azure Media Services Video Indexer tillhandahåller inte omedelbar redundans av tjänsten om det finns ett regionalt datacenteravbrott eller ett regionalt datacenteravbrott eller ett haveri. I den här artikeln beskrivs hur du konfigurerar miljön för en redundans för att säkerställa optimal tillgänglighet för appar och minimerad återställningstid om en katastrof inträffar.

Vi rekommenderar att du konfigurerar återställning av affärskontinuitetskatastrofer (BCDR) över regionala par för att dra nytta av Azures principer för isolering och tillgänglighet. Mer information finns i [Azure-parade regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Krav

En Azure-prenumeration. Om du inte har en Azure-prenumeration ännu registrerar du dig för [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Redundans till ett sekundärt konto

Om du vill implementera BCDR måste du ha två videoindexererkonton för att hantera redundans.

1. Skapa två videoindexerkonton som är anslutna till Azure (se [Skapa ett videoindexerkonto](connect-to-azure.md)). Skapa ett konto för din primära region och den andra till den parade azure-regionen.
1. Om det finns ett fel i din primära region växlar du till indexering med det sekundära kontot.

> [!TIP]
> Du kan automatisera BCDR genom att ställa in aktivitetsloggaviseringar för tjänsthälsomeddelanden enligt [Skapa aktivitetsloggaviseringar på tjänstmeddelanden](../../service-health/alerts-activity-log-service-notifications.md).

Information om hur du använder flera klienter finns i [Hantera flera klienter](manage-multiple-tenants.md). Om du vill implementera BCDR väljer du ett av följande två alternativ: [VideoIndexer-konto per klient eller](manage-multiple-tenants.md#video-indexer-account-per-tenant) [Azure-prenumeration per klient .](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Nästa steg

[Hantera ett videoindexerkonto som är anslutet till Azure](manage-account-connected-to-azure.md).
