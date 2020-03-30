---
title: Hantera flera klienter med Video Indexer - Azure
description: Den här artikeln föreslår olika integrationsalternativ för hantering av flera klienter med Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76990512"
---
# <a name="manage-multiple-tenants"></a>Hantera flera klientorganisationer

I den här artikeln beskrivs olika alternativ för att hantera flera klienter med Video Indexer. Välj en metod som passar bäst för ditt scenario:

* Video Indexerkonto per klient
* Single Video Indexer konto för alla klienter
* Azure-prenumeration per klientorganisation

## <a name="video-indexer-account-per-tenant"></a>Video Indexerkonto per klient

När du använder den här arkitekturen skapas ett videoindexerkonto för varje klient. Klienterna har fullständig isolering i det beständiga och beräkningsskiktet.  

![Video Indexerkonto per klient](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Överväganden

* Kunder delar inte lagringskonton (om de inte konfigureras manuellt av kunden).
* Kunder delar inte beräkning (reserverade enheter) och påverkar inte bearbetningsjobbtider för varandra.
* Du kan enkelt ta bort en klient från systemet genom att ta bort videoindexeringskontot.
* Det finns ingen möjlighet att dela anpassade modeller mellan klienter.

    Kontrollera att det inte finns något affärskrav för att dela anpassade modeller.
* Svårare att hantera på grund av flera Video Indexer (och tillhörande Media Services) konton per klient.

> [!TIP]
> Skapa en administratörsanvändare för ditt system i [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/) och använd API:et för auktorisering för att ge dina klienter relevant [kontoåtkomsttoken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Single Video Indexer konto för alla användare

När du använder den här arkitekturen ansvarar kunden för isolering av klienter. Alla klienter måste använda ett enda Video Indexer-konto med ett enda Azure Media Service-konto. När du laddar upp, söker eller tar bort innehåll måste kunden filtrera rätt resultat för den klienten.

![Single Video Indexer konto för alla användare](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Med det här alternativet kan anpassningsmodeller (Person, Språk och Varumärken) delas eller isoleras mellan klienter genom att filtrera modellerna efter klient.

När [du laddar upp videor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)kan du ange ett annat partitionsattribut per klient. Detta tillåter isolering i [sök-API: et](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Genom att ange partitionsattributet i sök-API:et får du bara resultat av den angivna partitionen. 

### <a name="considerations"></a>Överväganden

* Möjlighet att dela innehålls- och anpassningsmodeller mellan klienter.
* En klient påverkar andra klienters prestanda.
* Kunden måste skapa ett komplext hanteringslager ovanpå Video Indexer.

> [!TIP]
> Du kan använda [prioritetsattributet](upload-index-videos.md) för att prioritera klientjobb.

## <a name="azure-subscription-per-tenant"></a>Azure-prenumeration per klientorganisation 

När du använder den här arkitekturen har varje klient en egen Azure-prenumeration. För varje användare skapar du ett nytt videoindexerkonto i klientprenumerationen.

![Azure-prenumeration per klientorganisation](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Överväganden

* Det här är det enda alternativet som möjliggör faktureringsseparation.
* Den här integreringen har fler hanteringskostnader än videoindexerkonto per klient. Om fakturering inte är ett krav rekommenderar vi att du använder något av de andra alternativen som beskrivs i den här artikeln.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
