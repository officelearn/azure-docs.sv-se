---
title: Definiera erbjudandeinställningar för ett konsultserviceerbjudande | Azure Marketplace
description: Definiera erbjudandeinställningar i ett Azure- eller Dynamics 365-erbjudande om konsulttjänster i Cloud Partner Portal för Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 7b2c9d136a32e54ce5b5db8f6ce9ba8cd63a4f3c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745642"
---
# <a name="offer-settings-tab"></a>Fliken Erbjudandeinställningar

>[!Important]
>Från och med den 30 mars 2020 börjar vi flytta hanteringen av dina konsulttjänster till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [översikten över att skapa tjänster](https://aka.ms/AzureCreateConsultingService) för att hantera dina migrerade erbjudanden.

På skärmen **Nytt erbjudande** är det första steget att skapa erbjudandeidentiteten. Erbjudandeidentiteten består av tre delar: **Erbjudande-ID,** **Publisher ID**och **Namn**. Var och en av dessa delar behandlas i följande avsnitt.

![Skapa ett nytt konsultserviceerbjudande - fliken Erbjudandeinställningar](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Erbjudande-ID*

Den här identifieraren är ett unikt namn som du skapar när du skickar erbjudandet för första gången. Den får endast bestå av gemener alfanumeriska tecken, streck eller understreck. **Erbjudande-ID:et** visas i webbadressen och påverkar sökmotorresultaten. Ett exempel är *yourcompanyname_exampleservice*.

Som visas i exemplet läggs **erbjudande-ID:t** till i ditt utgivar-ID för att skapa en unik identifierare. Denna unika identifierare exponeras som en permanent länk som kan bokas och indexeras av sökmotorerna.

>[!Note]
>När ett erbjudande har live kan dess identifierare inte uppdateras.


### <a name="publisher-id"></a>Publisher-ID*

Den här identifieraren är relaterad till ditt konto. När du har loggat in med ditt organisationskonto visas **publisher-ID:n** på den nedrullningsliga menyn.


### <a name="name"></a>Namn*

Den här strängen visas som erbjudandenamn på AppSource eller på Azure Marketplace. Rutan **Namn** är begränsad till 50 tecken. Granskaren kan behöva redigera din titel för att lägga till varaktighet och erbjudandetyp till ditt erbjudandenamn.

I följande exempel visas hur erbjudandenamnet är monterat. 

![Skapa ett nytt erbjudande om konsulttjänster](media/cppsampleconsultingoffer.png)

Erbjudandenamnet består av fyra delar:

-   **Varaktighet:** Definieras på fliken **Butiksinformation** i redigeraren. Varaktigheten kan uttryckas i timmar, dagar eller veckor.
-   **Typ av tjänst:** Definieras på fliken **Butiksinformation** i redigeraren. Typer av `Assessment`tjänster `Briefing` `Implementation`är `Proof of concept`, `Workshop`, , och .
-   **Preposition:** Infogad av granskaren.
-   **Namn:** Definierad på sidan **Erbjudandeinställningar.**

>[!Note]
>Rutan **Namn** är begränsad till 50 tecken. Granskaren kan behöva redigera din titel för att lägga till varaktighet och erbjudandetyp till ditt erbjudandenamn.

Följande lista innehåller flera väl namngivna erbjudandenamn:

-   Essentials för professionella tjänster: 1-timmars briefing
-   Molnmigreringsplattform: 1-timmars briefing
-   PowerApps och Microsoft Flow: 1-dagars workshop
-   Azure Machine Learning: 3 Wk PoC
-   Brick och Klicka Retail Lösning: 1-timmars Briefing
-   Ta med egna data: 1-Wk Workshop
-   Cloud Analytics: 3-dagars workshop
-   Power BI-utbildning: 3-dagars workshop
-   Sales Management Lösning: 1-veckors implementering
-   CRM Snabbstart: 1-dagars workshop
-   Dynamics 365 för försäljning: 2-dagars utvärdering

När du har fyllt i fliken **Erbjudandeinställningar** sparar du ditt bidrag. Erbjudandets namn visas nu ovanför redigeraren, och du hittar det i **Alla erbjudanden**.

## <a name="next-steps"></a>Nästa steg

Nu kan du ange information i skyltfönstret och avgöra om du [vill publicera på Azure Marketplace eller på AppSource](./cpp-consulting-service-storefront-details.md).
