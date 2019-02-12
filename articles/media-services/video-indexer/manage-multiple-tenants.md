---
title: Hantera flera innehavare med Video Indexer - Azure
description: Den här artikeln föreslår olika IR-alternativ för att hantera flera innehavare med Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/10/2019
ms.author: ikbarmen
ms.openlocfilehash: 3b6daa60e8c8945d49170a00da5bd3ffb57b48ac
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000122"
---
# <a name="manage-multiple-tenants"></a>Hantera flera klientorganisationer

Den här artikeln beskrivs olika alternativ för att hantera flera innehavare med Video Indexer. Välj en metod som bäst passar ditt scenario:

* Video Indexer-konto per klient
* Enskild Video Indexer-konto för alla klienter
* Azure-prenumeration per klient

## <a name="video-indexer-account-per-tenant"></a>Video Indexer-konto per klient

När du använder den här arkitekturen, skapas en Video Indexer-konto för varje klient. Klienterna har fullständig isolering i det beständiga och compute lager.  

![Video Indexer-konto per klient](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Överväganden

* Kunderna dela inte storage-konton (om inte manuellt konfigurerad av kunden).
* Kunderna delar inte beräkning (enheter) och påverkar inte jobb bearbetningstider efter varandra.
* Du kan enkelt ta bort en klient från systemet genom att ta bort Video Indexer-konto.
* Det finns ingen möjlighet att dela anpassade modeller mellan klienter.

    Kontrollera att det finns några affärsmässiga skäl att dela anpassade modeller.
* Svårare att hantera på grund av flera Video Indexer (och associerade Media Services)-konton per klient.

> [!TIP]
> Skapa en administratörsanvändare för ditt system i [Utvecklarportalen för Video Indexer](https://api-portal.videoindexer.ai/) och Använd API för auktorisering för att ge klienterna de relevanta [-kontots åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Enskild Video Indexer-konto för alla användare

När du använder den här arkitekturen kan ansvarar kunden för isolering av innehavare. Alla klienter måste använda en enda Video Indexer-konto med ett enda Azure Media Service-konto. När du laddar upp, måste söka eller ta bort innehåll, kunden filtrera rätt resultatet för den klienten.

![Enskild Video Indexer-konto för alla användare](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Med det här alternativet anpassning modeller (Person, språk och varumärken) delas eller isolerade mellan klienter genom att filtrera modeller av klient.

När [ladda upp videor](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), kan du ange en annan partition attributet per klient. På så sätt kan isolering i den [Sök API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Genom att ange attributet partition i Sök-API: T får endast resultatet av den angivna partitionen. 

 ### <a name="considerations"></a>Överväganden

* Möjlighet att dela innehåll och anpassning modeller mellan klienter.
* En klientorganisation påverkar prestandan för andra klienter.
* Kunden måste skapa ett komplext lagringshanteringslager ovanpå Video Indexer.

> [!TIP]
> Du kan använda den [prioritet](upload-index-videos.md) attribut för att prioritera klienter jobb.

## <a name="azure-subscription-per-tenant"></a>Azure-prenumeration per klient 

När du använder den här arkitekturen kan har varje klient sin egen Azure-prenumeration. För varje användare ska du skapa ett nytt Video Indexer-konto i klientprenumeration.

![Azure-prenumeration per klient](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Överväganden

* Det här är det enda alternativet som gör att faktureringen uppdelning.
* Den här integrationen har mer hanteringskostnader än Video Indexer-konto per klient. Om fakturering inte är ett krav, rekommenderas att använda en av de andra alternativen som beskrivs i den här artikeln.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
