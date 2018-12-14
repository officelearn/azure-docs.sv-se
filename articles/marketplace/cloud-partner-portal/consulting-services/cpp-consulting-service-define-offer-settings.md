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
ms.openlocfilehash: 6ce51dc9bbcdc7b46593a4800c4fce07a42f3171
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346479"
---
# <a name="offer-settings-tab"></a>Inställningsfliken för erbjudandet

På den **nytt erbjudande** skärmen det första steget är att skapa erbjudandet identiteten. Erbjudandet identiteten består av tre delar: **ID för erbjudande**, **Publicerings-ID**, och **namn**. De olika delarna beskrivs i följande avsnitt.

![Skapa en ny konsult tjänsterbjudande - fliken för erbjudande-inställningar](media/consultingoffer-settings-tab.png)

*Erbjudande-ID*

Den här identifieraren är ett unikt namn som du skapar när du skickar in erbjudandet. Den måste bestå enbart av gemena alfanumeriska tecken, bindestreck eller understreck. Den **erbjudande-ID** syns i URL-Adressen och påverkan motorn sökresultat. Till exempel *yourcompanyname_exampleservice*

Som visas i exemplet är den **ID för erbjudande** kommer att läggas till Publicerings-ID för att skapa en unik identifierare. Detta visas som en permanent länk som kan belasta och indexeras av de olika sökmotorer.

*När ett erbjudande är aktiv, det går inte att uppdatera sin identifierare*

*Utgivar-ID*

Den här identifieraren är relaterat till ditt konto. När du har loggat in med ditt organisationskonto din **Publisher** ID visas i den nedrullningsbara menyn.

*Namn*

Strängen är vad som visas som erbjudandet namnen på AppSource eller Azure Marketplace. Den *namn* fält är begränsad till 50 tecken.  Granskaren kan behöva redigera din rubrik för att lägga till namnet på erbjudandet med varaktigheten och erbjudandetyp.

>[!Note]
>Viktigt: Ange endast namnet på den faktiska servicen här. Omfattar inte varaktighet och typ av tjänst.

I följande exempel av Edgewater Fullscope visas hur erbjudandenamn är klar. Erbjudandenamn visas som:

![Skapa en ny konsult tjänsterbjudande](media/cppsampleconsultingoffer.png)

Erbjudandenamn består av fyra delar:

-   **Varaktighet:** – definierad i den **Storefront information** fliken i redigeraren. Varaktighet kan uttryckas i timmar, dagar eller veckor.
-   **Typ av tjänst:** – definierad i den **Storefront information** fliken i redigeraren. Typer av tjänster är `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, och `Workshop`.
-   **Preposition:** – infogad av den
-   **Namn:** – definierad i den **erbjuder inställningar** sidan.

>[!Note]
>Namnfältet är begränsad till 50 tecken. Namnet som du skickar kan behöva redigeras av granskaren för varaktighet och erbjudandetyp som ska läggas till i namnet.

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

När du har slutfört den **erbjuder inställningar** fliken kan du spara ditt bidrag. Erbjudandenamn kommer nu att visas ovanför redigeraren och du hittar den i alla erbjuder.

**Nästa steg**

Nu kan du ange [Storefront information och avgöra om du vill publicera på Azure Marketplace eller AppSource](./cpp-consulting-service-storefront-details.md).