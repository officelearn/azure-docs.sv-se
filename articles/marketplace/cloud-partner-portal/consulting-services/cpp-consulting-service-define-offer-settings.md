---
title: Definiera erbjudande inställningar för ett konsult tjänst erbjudande | Azure Marketplace
description: Definiera erbjudande inställningar i ett Azure-eller Dynamics 365 konsult tjänst erbjudande i Cloud Partner Portal för Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 406a37a1ef946b1c3ceb0d7b02ba318f423dcf53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146681"
---
# <a name="offer-settings-tab"></a>Fliken Erbjudandeinställningar

>[!Important]
>Från den 13 april 2020 börjar vi flytta hanteringen av dina konsult tjänst erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Översikt över hur du skapar konsult tjänster](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-consulting-service-offer) för att hantera dina migrerade erbjudanden.

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
-   **Typ av tjänst:** Definieras på fliken **butik information** i redigeraren. Typer av tjänster är `Assessment`, `Briefing` `Implementation` `Proof of concept`,, och `Workshop`.
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
