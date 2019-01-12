---
title: Azure och Dynamics 365-konsult tjänsterbjudande - definiera erbjudandeinställningar | Microsoft Docs
description: Guide för att definiera erbjuder inställningar i en Azure- eller Dynamics 365-konsult tjänsteerbjudanden i partnerportalen i molnet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231945"
---
# <a name="offer-settings-tab"></a>Inställningsfliken för erbjudandet

På den **nytt erbjudande** skärmen det första steget är att skapa erbjudandet identiteten. Erbjudandet identiteten består av tre delar: **ID för erbjudande**, **Publicerings-ID**, och **namn**. De olika delarna beskrivs i följande avsnitt.

![Skapa en ny konsult tjänsterbjudande – erbjuder fliken Inställningar](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>ID för erbjudande

Den här identifieraren är ett unikt namn som du skapar när du skickar in erbjudandet. Den måste bestå enbart av gemena alfanumeriska tecken, bindestreck eller understreck. Den **erbjudande-ID** syns i URL: en och påverkar sökresultat. Ett exempel är *yourcompanyname_exampleservice*.

Som visas i exemplet är den **erbjudande-ID** läggs till Publicerings-ID för att skapa en unik identifierare. Den här unika identifieraren visas som en permanent länk som kan belasta och indexeras av de olika sökmotorer.

>[!Note]
>När ett erbjudande är aktiv, kan inte dess identifierare uppdateras.

### <a name="publisher-id"></a>Utgivar-ID

Den här identifieraren är relaterat till ditt konto. När du har loggat in med ditt organisationskonto din **Publicerings-ID** visas i den nedrullningsbara menyn.

### <a name="name"></a>Namn

Den här strängen visas som erbjudandenamn på AppSource eller på Azure Marketplace. Den **namn** rutan är begränsad till 50 tecken. Granskaren kan behöva redigera din rubrik för att lägga till varaktighet och erbjudandetyp till namnet på ditt erbjudande.

I följande exempel visas hur erbjudandenamn är klar. 

![Skapa en ny konsult tjänsterbjudande](media/cppsampleconsultingoffer.png)

Erbjudandenamn består av fyra delar:

-   **Varaktighet:** Definierade på den **Storefront information** fliken i redigeraren. Varaktighet kan uttryckas i timmar, dagar eller veckor.
-   **Typ av tjänst:** Definierade på den **Storefront information** fliken i redigeraren. Typer av tjänster är `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, och `Workshop`.
-   **Preposition:** Infogas av den.
-   **Namn:** Definierade på den **erbjuder inställningar** sidan.

>[!Note]
>Den **namn** rutan är begränsad till 50 tecken. Granskaren kan behöva redigera din rubrik för att lägga till varaktighet och erbjudandetyp till namnet på ditt erbjudande.

Följande lista innehåller flera väl namngivna erbjudandet namn:

-   Essentials för professionella tjänster: Genomgång för 1 tim
-   Molnplattform för migrering: Genomgång för 1 tim
-   PowerApps och Microsoft Flow: 1-dagars Workshop
-   Azure Machine Learning-tjänster: 3 veckor PoC
-   Bricka och klicka på Retail-lösningen: Genomgång för 1 tim
-   Hämta dina egna Data: 1 vecka Workshop
-   Molnet Analytics: 3 dagars Workshop
-   Utbildning för Power BI: 3 dagars Workshop
-   Försäljning hanteringslösning: 1 vecka implementering
-   CRM-Snabbstart: 1-dagars Workshop
-   Dynamics 365 for Sales: 2-dagars utvärdering

När du fyller i **erbjuder inställningar** fliken Spara ditt bidrag. Erbjudandenamn nu visas ovanför redigeraren och du hittar den i **alla erbjuder**.

## <a name="next-steps"></a>Nästa steg

Nu kan du ange [storefront information och avgöra om du vill publicera på Azure Marketplace eller på AppSource](./cpp-consulting-service-storefront-details.md).
