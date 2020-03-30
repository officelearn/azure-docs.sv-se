---
title: Översikt över Azure Portal | Microsoft Docs
description: Azure-portalen är ett grafiskt användargränssnitt som du kan använda för att hantera dina Azure-tjänster. Lär dig hur du navigerar och hittar resurser i Azure-portalen.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310738"
---
# <a name="azure-portal-overview"></a>Översikt över Azure Portal

Den här artikeln introducerar Azure-portalen, identifierar portalsideselement och hjälper dig att bekanta dig med Azure Portal Management Experience.

## <a name="what-is-the-azure-portal"></a>Vad är Azure-portalen?

Azure-portalen är en webbaserad, enhetlig konsol som ger ett alternativ till kommandoradsverktyg. Med Azure-portalen kan du hantera din Azure-prenumeration med hjälp av ett grafiskt användargränssnitt. Du kan skapa, hantera och övervaka allt från enkla webbappar till komplexa molndistributioner. Skapa anpassade instrumentpaneler för en ordnad vy av resurser. Konfigurera hjälpmedelsalternativ för en optimal upplevelse.

Azure-portalen är utformad för återhämtning och kontinuerlig tillgänglighet. Den har en närvaro i alla Azure-datacenter. Den här konfigurationen gör Azure-portalen motståndskraftig mot enskilda datacenterfel och undviker långsamma nätverksstopp genom att vara nära användare. Azure-portalen uppdateras kontinuerligt och kräver inga driftstopp för underhållsaktiviteter.

## <a name="azure-portal-menu"></a>Azure portal-menyn

Du kan välja standardläge för portalmenyn. Den kan dockas eller fungera som en utfällbara panel.

När portalmenyn är i utfällbart läge döljs den tills du behöver den. Välj menyikonen för att öppna eller stänga menyn.

![Azure portal-menyn i utfällbart läge](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Om du väljer dockat läge för portalmenyn visas det alltid. Du kan komprimera menyn för att ge mer arbetsutrymme.

![Azure portal-menyn i dockat läge](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure Hem

Som ny prenumerant på Azure-tjänster är det första du ser när du [har loggat in på portalen](https://portal.azure.com) **Azure Home**. Den här sidan sammanställer resurser som hjälper dig att få ut det mesta av din Azure-prenumeration. Vi har inkluderat länkar till kostnadsfria onlinekurser, dokumentation, kärntjänster och användbara webbplatser för att hålla dig uppdaterad och hantera förändringar för din organisation. För snabb och enkel åtkomst till pågående arbete visar vi också en lista över dina senast besökta resurser. Du kan inte anpassa den här sidan, men du kan välja om du vill se **Azure Home** eller **Azure Dashboard** som standardvy. Första gången du loggar in finns det en uppmaning högst upp på sidan där du kan spara dina inställningar.

![Skärmbild som visar standardvyväljaren](./media/azure-portal-overview/azure-portal-default-view.png)

Både Azure-portalmenyn och Azure-standardvyn kan ändras i **portalinställningar**. Om du ändrar ditt val tillämpas ändringen omedelbart.

![Skärmbild som visar standardvyväljaren](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure-instrumentpanel

Instrumentpaneler ger en fokuserad vy över de resurser i din prenumeration som betyder mest för dig. Vi har gett dig en standardinstrumentpanel för att komma igång. Du kan anpassa instrumentpanelen så att de resurser du använder ofta hamnar i en enda vy. Alla ändringar du gör i standardvyn påverkar bara upplevelsen. Du kan dock skapa ytterligare instrumentpaneler för eget bruk eller publicera dina anpassade instrumentpaneler och dela dem med andra användare i organisationen. Mer information finns [i Skapa och dela instrumentpaneler i Azure-portalen](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Ta dig runt portalen

Det är bra att förstå den grundläggande portallayouten och hur du interagerar med den. Här kommer vi att presentera komponenterna i användargränssnittet och en del av den terminologi vi använder för att ge instruktioner. En mer detaljerad rundtur i portalen finns i kurslektionen [Navigera i portalen](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Azure portal-menyn och sidhuvudet är globala element som alltid finns. Dessa beständiga funktioner är "shell" för användargränssnittet som är associerat med varje enskild tjänst eller funktion och huvudet ger åtkomst till globala kontroller. Konfigurationssidan (kallas ibland för ett blad) för en resurs kan också ha en resursmeny som hjälper dig att flytta mellan funktioner.

Figuren nedan etiketterar de grundläggande elementen i Azure-portalen, som alla beskrivs i följande tabell.

![Skärmbild som visar portalvyn i helskärmsläge och nyckeln till gränssnittselement](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Skärmbild som visar utökad portalmeny](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Nyckel|Beskrivning
|:---:|---|
|1|Sidhuvud. Visas högst upp på varje portalsida och innehåller globala element.|
|2| Global sökning. Använd sökfältet för att snabbt hitta en viss resurs, en tjänst eller dokumentation.|
|3|Globala kontroller. Liksom alla globala element finns dessa funktioner kvar på portalen och inkluderar: Cloud Shell, prenumerationsfilter, meddelanden, portalinställningar, hjälp och support och skicka feedback till oss.|
|4|Ditt konto. Visa information om ditt konto, byta kataloger, logga ut eller logga in med ett annat konto.|
|5|Portal-menyn. Portalmenyn är ett globalt element som hjälper dig att navigera mellan tjänster. Ibland kallas sidofältet, portalmenyläget kan ändras i **Portal inställningar**.|
|6|Resursmeny. Många tjänster innehåller en resursmeny som hjälper dig att hantera tjänsten. Det här elementet kan kallas den vänstra rutan.|
|7|Kommandofältet. Kontrollerna i kommandofältet är kontextuella till ditt nuvarande fokus.|
|8|Arbetsfönster.  Visar information om resursen som för närvarande är i fokus.|
|9|Breadcrumb. Du kan använda länkarna i sökvägen för att flytta tillbaka en nivå i arbetsflödet.|
|10|Huvudkontroll för att skapa en ny resurs i den aktuella prenumerationen. Expandera eller öppna portalmenyn för att hitta **+ Skapa en resurs**. Sök eller bläddra i Azure Marketplace efter den resurstyp som du vill skapa.|
|11|Din favoritlista. Se [Lägga till, ta bort och sortera favoriter](../azure-portal/azure-portal-add-remove-sort-favorites.md) för att lära dig hur du anpassar listan.|

## <a name="get-started-with-services"></a>Komma igång med tjänster

Om du är ny prenumerant måste du skapa en resurs innan det finns något att hantera. Välj **+ Skapa en resurs** för att visa de tjänster som är tillgängliga på Azure Marketplace. Du hittar program och tjänster från hundratals leverantörer här, alla certifierade för att köras på Azure.

Vi har fyllt dina favoriter i sidofältet med länkar till vanliga tjänster.  Om du vill visa alla tillgängliga tjänster väljer du **Alla tjänster** i sidofältet.

> [!TIP]
> Det snabbaste sättet att hitta en resurs, tjänst eller dokumentation är att använda *Sök* i det globala huvudet. Använd länkarna i ströbröd för att gå tillbaka till föregående sidor.
>
Titta på det här videoklippet om hur du använder global sökning i Azure-portalen.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Så här använder du global sökning i Azure-portalen](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Nästa steg

* Läs mer om var du kan köra Azure Portal i [webbläsare och enheter som stöds](../azure-portal/azure-portal-supported-browsers-devices.md)
* Håll kontakten när du är på språng med [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Ombord och konfigurera din molnmiljö med [Azure Quickstart Center](../azure-portal/azure-portal-quickstart-center.md)
