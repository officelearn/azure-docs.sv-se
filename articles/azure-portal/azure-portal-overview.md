---
title: Översikt över Azure Portal | Microsoft Docs
description: Lär dig hur du navigerar i Azure Portal och använder den för att hantera tjänster
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 6e176a8b16129cd35fc011e14fcb36038f7c0144
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000345"
---
# <a name="azure-portal-overview"></a>Översikt över Azure Portal

Den här artikeln beskriver Azure Portal, identifierar portalens sid element och hjälper dig att bekanta dig med Azure Portal hanterings upplevelsen.

## <a name="what-is-the-azure-portal"></a>Vad är Azure Portal?

Azure Portal är en webbaserad, enhetlig konsol som ger ett alternativ till kommando rads verktyg. Med Azure Portal kan du hantera Azure-prenumerationen med hjälp av ett grafiskt användar gränssnitt. Du kan skapa, hantera och övervaka allt från enkla webbappar till komplexa moln distributioner, skapa anpassade instrument paneler för en strukturerad vy över resurser och konfigurera hjälpmedels alternativ för bästa möjliga upplevelse.

Azure Portal är utformad för återhämtning och kontinuerlig tillgänglighet. Den har en förekomst i varje Azure-datacenter och gör det flexibelt för enskilda Data Center-haverier och förhindrar även nätverks långsam genom att ligga nära användare. Azure Portal uppdateras kontinuerligt och kräver ingen stillestånds tid för underhålls aktiviteter.

## <a name="azure-home"></a>Azure Home

Som en ny prenumerant på Azure-tjänster är det första du ser när du har [loggat](https://portal.azure.com) in på portalen **Azure Home**. Den här sidan sammanställer resurser som hjälper dig att få ut mesta möjliga av din Azure-prenumeration. Vi har inkluderat länkar till kostnads fria onlinekurser, dokumentation, kärn tjänster och användbara webbplatser för att hålla aktuella och hantera ändringar i din organisation. För snabb och enkel åtkomst till arbete som pågår, visar vi också en lista över dina senast besökta resurser. Du kan inte anpassa den här sidan, men du kan välja om du vill se **Azures start** -eller **Azure-instrumentpanelen** som standardvy. Första gången du loggar in visas en uppfråga längst upp på sidan där du kan spara dina inställningar.

![Skärm bild som visar standard visnings väljaren](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Azure-instrumentpanel

Instrument paneler ger en fokuserad vy över de resurser i din prenumeration som är viktigast för dig. Vi har fått en standard instrument panel som hjälper dig att komma igång. Du kan anpassa den här instrument panelen för att ta de resurser som du använder mest i en enda vy. Alla ändringar du gör i standardvyn påverkar bara din upplevelse. Du kan dock skapa ytterligare instrument paneler för din egen användning eller publicera dina anpassade instrument paneler och dela dem med andra användare i din organisation. Mer information finns i [skapa och dela instrument paneler i Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Komma runt portalen

Det är bra att förstå den grundläggande portalens layout och hur du interagerar med den. Här presenterar vi komponenterna i användar gränssnittet och några av terminologin som vi använder för att ge instruktioner. En mer detaljerad genom gång av portalen finns i kurs lektionen [navigera i portalen](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal).

Azure Portal sid panelen och sidhuvudet är globala element som alltid är tillgängliga. De här permanenta funktionerna är "Shell" för användar gränssnittet som är kopplat till varje enskild tjänst eller funktion och rubriken ger till gång till globala kontroller. Konfigurations sidan (kallas ibland "blad") för en resurs kan också ha ett vänster fönster för att hjälpa dig att flytta mellan funktioner.

Figuren nedan etiketter de grundläggande elementen i Azure Portal, som beskrivs i följande tabell.

![Skärm bild som visar vyn över hel skärms portalen och nyckel till GRÄNSSNITTs element](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|Nyckel|Beskrivning
|:---:|---|
|1|Sidhuvud. Visas överst på varje Portal sida och innehåller globala element.|
|2| Global sökning. Använd Sök fältet för att snabbt hitta en speciell resurs, tjänst eller dokumentation.|
|3|Globala kontroller. Precis som alla globala element behålls dessa funktioner i portalen och inkluderar: Cloud Shell, prenumerations filter, meddelanden, Portal inställningar, hjälp och support och skicka oss feedback.|
|4|Ditt konto. Visa information om ditt konto, växla kataloger, logga ut eller logga in med ett annat konto.|
|5|Marginal. Sid panelen är ett globalt element som hjälper dig att navigera mellan tjänster. Marginal listen kan minimeras för att ge mer fokus till arbets fönstret.|
|6|Huvud kontroll för att skapa en ny resurs i den aktuella prenumerationen. Sök efter eller bläddra i Azure Marketplace efter den resurs typ som du vill skapa.|
|7|Listan Favoriter. Lägg till eller ta bort favoriter från sidan **alla tjänster** .|
|8|Vänstra fönstret. Många tjänster innehåller en meny till vänster-fönster som hjälper dig att hantera tjänsten.|
|9|Kommando fält. Kontrollerna i kommando fältet är sammanhangsbaserade för ditt aktuella fokus.|
|10|Navigering. Du kan använda länkarna för dynamiska länkar för att flytta tillbaka en nivå i ditt arbets flöde.|
|11|Arbets fönster.  Visar information om den resurs som för närvarande är i fokus.|

## <a name="get-started-with-services"></a>Kom igång med tjänster

Om du är en ny prenumerant måste du skapa en resurs innan det finns något att hantera. Välj **+ skapa en resurs** för att visa de tjänster som är tillgängliga på Azure Marketplace. Du hittar program och tjänster från hundratals leverantörer här, allt certifierat för att köras på Azure.

Vi fyllde i dina favoriter i sido panelen med länkar till vanliga tjänster.  Om du vill visa alla tillgängliga tjänster väljer du **alla tjänster** från sido panelen.

> [!TIP]
> Det snabbaste sättet att hitta en resurs, tjänst eller dokumentation är att använda *Sök* i den globala rubriken. Använd länkarna för dynamiska länkar om du vill gå tillbaka till föregående sidor.
>
Titta på den här videon om du vill ha en demonstration om hur du använder global sökning i Azure Portal.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Så här använder du global sökning i Azure Portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Nästa steg

* Läs mer om var du kan köra Azure Portal i [webbläsare och enheter som stöds](../azure-portal/azure-portal-supported-browsers-devices.md)

* Håll dig uppdaterad när du är igång med [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/)
