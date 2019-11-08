---
title: Definiera erbjudande inställningar för ett konsult tjänst erbjudande | Azure Marketplace
description: Definiera erbjudande inställningar i ett Azure-eller Dynamics 365 konsult tjänst erbjudande i Cloud Partner Portal för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: e505f9aa1ec08708b85176d5b05fc5b40ceb295e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818132"
---
# <a name="offer-settings-tab"></a>Fliken Erbjudandeinställningar

På den **nya erbjudande** skärmen är det första steget att skapa erbjudande identiteten. Erbjudande identiteten består av tre delar: **erbjudande-ID**, **utgivar-ID**och **namn**. Var och en av dessa delar beskrivs i följande avsnitt.

![Skapa en ny konsult tjänst inställning fliken Inställningar](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Erbjudande-ID *

Den här identifieraren är ett unikt namn som du skapar när du först skickar erbjudandet. Det får bara bestå av gemena alfanumeriska tecken, bindestreck eller under streck. **Erbjudande-ID** visas i URL: en och påverkar Sök motorns resultat. Ett exempel är *yourcompanyname_exampleservice*.

Som du ser i exemplet läggs **erbjudande-ID: t** till ditt UTGIVAR-ID för att skapa en unik identifierare. Den här unika identifieraren visas som en permanent länk som kan bokas och indexeras av sökmotorer.

>[!Note]
>När ett erbjudande är aktivt går det inte att uppdatera dess identifierare.


### <a name="publisher-id"></a>Utgivar-ID *

Den här identifieraren är relaterad till ditt konto. När du har loggat in med ditt organisations konto visas ditt **utgivar-ID** i den nedrullningsbara menyn.


### <a name="name"></a>Ändrat

Den här strängen visas som erbjudande namn på AppSource eller på Azure Marketplace. **Namn** rutan är begränsad till 50 tecken. Granskaren kan behöva redigera titeln för att lägga till varaktighet och erbjudande typ till namnet på erbjudandet.

I följande exempel visas hur namnet på erbjudandet monteras. 

![Skapa ett nytt konsult tjänst erbjudande](media/cppsampleconsultingoffer.png)

Namnet på erbjudandet består av fyra delar:

-   **Varaktighet:** Definieras på fliken **butik information** i redigeraren. Varaktigheten kan uttryckas i timmar, dagar eller veckor.
-   **Typ av tjänst:** Definieras på fliken **butik information** i redigeraren. Typer av tjänster är `Assessment`, `Briefing`, `Implementation`, `Proof of concept`och `Workshop`.
-   **Förposition:** Infogas av granskaren.
-   **Namn:** Definieras på sidan **erbjudande inställningar** .

>[!Note]
>**Namn** rutan är begränsad till 50 tecken. Granskaren kan behöva redigera titeln för att lägga till varaktighet och erbjudande typ till namnet på erbjudandet.

Följande lista innehåller flera namn på välkända erbjudanden:

-   Grundläggande för professionella tjänster: 1 – personal-kort
-   Molnmigrering plattform: 1 – HR-kort
-   PowerApps och Microsoft Flow: 1 dags workshop
-   Azure Machine Learning: 3 – veckor PoC
-   Bricka och klicka på detalj handels lösning: 1 – personal-kort
-   Ta med dina egna data: 1 – veckor workshop
-   Cloud Analytics: tre dagars workshop
-   Power BI utbildning: 3 dagars workshop
-   Lösning för försäljnings hantering: en veckas implementering
-   Snabb start för CRM: 1 – dags workshop
-   Dynamics 365 för försäljning: 2 dagars utvärdering

När du har fyllt i fliken **erbjudande inställningar** sparar du ditt bidrag. Namnet på erbjudandet visas nu ovanför redigeraren och du hittar det i **alla erbjudanden**.

## <a name="next-steps"></a>Nästa steg

Nu kan du ange [butik information och avgöra om du ska publicera på Azure Marketplace eller på AppSource](./cpp-consulting-service-storefront-details.md).
