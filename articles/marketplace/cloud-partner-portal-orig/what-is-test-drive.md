---
title: Vad är en provkörning? | Microsoft Docs
description: Förklaring av Marketplace Test Drive-funktionen
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0bdff77a4be6a28e2799d4f481492d2c7ba6bda0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811249"
---
<a name="what-is-test-drive"></a>Vad är en provkörning?
===================

Test Drive är ett bra sätt att visa upp ditt erbjudande till potentiella kunder genom att ge dem kan \'testa innan du köper\', vilket resulterar i ökad konvertering och högt kvalificerade leads generation.

När du har angett kontaktinformationen kunder kan komma åt din färdiga Test Drive-upplevelse: en praktisk på egen hand utvärderingsversion av din produkt\'s viktiga funktioner och fördelar som visas i ett verkligt scenario för implementering.

Test Drive kan du placera produkten liv och generera högt kvalificerade leads i processen.

<a name="how-does-a-test-drive-work"></a>Hur fungerar en provkörning?
---------------------------

En potentiell kund identifierar ditt program på Marketplace, loggar in och samtycker till att användningsvillkoren. Kunden får då din miljö som är förkonfigurerade för att prova i ett fast antal timmar, medan du får ett högt kvalificerade lead att följa upp med.

![Steg ett. Marketplace-erbjudande som visas](./media/what-is-test-drive/step1.png)

![Steg två. Marketplace-erbjudande-inloggningsskärmen](./media/what-is-test-drive/step1andahalf.png)

![Steg tre. Marketplace-erbjudande publisher avtal skärmen](./media/what-is-test-drive/step2.png)

![Steg fyra. Test Drive konfiguration av företagsåtkomst](./media/what-is-test-drive/step3.png)

Och här är ett exempel på hur ett erbjudande ser ut när den behöver tid för att distribuera nedan:

![Marketplace-erbjudande som ska distribueras](./media/what-is-test-drive/step4.png)

![Marketplace Test Drive redo skärmen](./media/what-is-test-drive/step5.png)

![Skärmen för Marketplace provkörningen slutförd](./media/what-is-test-drive/step6.png)

Oavsett hur även komplexa ditt program, Microsoft Test Drive hjälper dig att förverkliga din produkt för kunden. Idag erbjuder vi tre olika typer av Test Drives baserat på vilken typ av produkt, scenario och du är på marketplace.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**: en Azure Resource Manager Test Drive är en Distributionsmall som innehåller alla Azure-resurser som utgör en lösning som skapas av utgivaren. Produkter som passar det här scenariot är sådana som använder endast Azure-resurser.
- **[Logic App](./logic-app-test-drive.md)**: A Logic App Test Drive är en Distributionsmall som är avsedd att omfatta alla komplexa lösningsarkitekturer. Alla Dynamics-program eller anpassade produkter bör använda den här typen av Test Drive.
- **Power BI**: A Power BI Test Drive är en inbäddad länk till en egna anpassade instrumentpanel. En produkt som vill bara visa ett interaktiva Power BI-objekt bör använda den här typen av Test Drive.
    Allt du behöver ladda upp här är din inbäddade Power BI-URL.

<a name="what-goes-on-in-the-background"></a>Det här händer i bakgrunden?
-------------------------------

Test Drive-tjänsten är utformad för att kontinuerligt stöd för och hantera dina kunder utan att det krävs några manuella arbete från dig. Din uppgift är att hantera och konfigurera de Test Drive-inställningarna från theCloud partnerportalen som utgivare, och sedan den här inställningen blir direkt tillgänglig för dina kunder.

Det beror på att när du har angett dina konfigurationer för din provkörning varje Test Drive blir en hanterad instans som ska distribueras på begäran för kunden begär den. När en Test Drive-instans är tilldelad Test Drive kan användas för angiven tid och sedan tas den bort för att skapa utrymme för en annan kund.

<a name="next-steps"></a>Nästa steg
----------

Nu när du vet vilka en Test Drive handlar om, gå besök som behövs för den specifika Test Drive-typ som du vill gå publicera för att lära dig allt om de obligatoriska fälten.

- **[Med Azure Resource Manager](./azure-resource-manager-test-drive.md)**
- **[Logikapp](./logic-app-test-drive.md)**

Om du har fler frågor, söker felsökningstips eller vill göra din provkörning lyckas, går du till [vanliga frågor och svar, felsökning och bästa praxis](./marketing-and-best-practices.md).
