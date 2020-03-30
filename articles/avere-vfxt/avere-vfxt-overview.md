---
title: Avere vFXT för Azure
description: Introduktion till Avere vFXT för Azure, ett molncachelager för HPC
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: a9dab2ca844feb1c24dfffc48b4f29cd1a311314
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76153199"
---
# <a name="what-is-avere-vfxt-for-azure"></a>Vad är Avere vFXT för Azure?

Avere vFXT för Azure är cachelagringslösning för filsystem för dataintensiva HPC-uppgifter (databehandling med höga prestanda). Du kan dra nytta av molnets skalbarhet för att göra dina data tillgängliga vid den tid och på den plats de behövs – även för data som lagras på din lokala maskinvara.

Avere vFXT stöder följande vanliga databehandlingsscenarier:

* Hybridmolnarkitektur - Avere vFXT för Azure kan arbeta med ett maskinvarulagringssystem, vilket ger fördelen av molnbaserad databehandling utan att behöva flytta filer.

* Cloud bursting - Avere vFXT för Azure kan hjälpa dig att flytta dina data till molnet för ett enda projekt, eller "lyft och flytta" hela arbetsflödet permanent.

![diagram som visar information om Avere vFXT-systemet i en Azure-prenumeration ansluten till bloblagring och till ett lokalt datacenter](media/avere-vfxt-hybrid.png)

Avere vFXT för Azure passar bäst för dessa situationer:

* Läsintensiva åtgärder för HPC-arbetsbelastningar
* Program som använder vanligt NFS-protokoll
* Servergrupper med 1 000 till 40 000 CPU-kärnor
* Integrering med lokal maskinvaru-NAS, Azure Blob Storage eller båda

Mer information finns på <https://azure.microsoft.com/services/storage/avere-vfxt/>

## <a name="who-uses-avere-vfxt-for-azure"></a>Vilka använder Avere vFXT för Azure?

Avere vFXT kan hjälpa till med alla typer av läsintensiva databehandlingsuppgifter:

### <a name="visual-effects-rendering"></a>Rendering av visuella effekter

Inom media och underhållning kan Avere vFXT-klustret påskynda dataåtkomsten för tidskritiska renderingsprojekt. Eftersom du kan lägga till mer cacheutrymme och fler beräkningsnoder i Azure har du flexibiliteten att hantera stora projekt effektivt.

### <a name="life-sciences"></a>Biovetenskap

Med Avere vFXT kan forskare köra sekundära analysarbetsflöden i Azure Compute och komma åt genomiska data oavsett var de befinner sig.

I läkemedelsforskning, Avere vFXT kluster kan påskynda läkemedelsupptäckt genom att hjälpa forskare förutsäga läkemedelsmål interaktioner och analysera forskningsdata.

### <a name="financial-services-analytics"></a>Analyser för ekonomiska tjänster

Ett Avere vFXT-kluster kan hjälpa till med snabbare beräkningar för kvantitativa analyser, vilket ger finansföretag bättre information för att fatta strategiska beslut.

## <a name="features-and-specifications"></a>Funktioner och specifikationer

Avere vFXT-systemet utgörs av minst tre virtuella Edge Filer-noder, konfigurerade i ett kluster. Det kan finnas nära klientdatorerna, som monterar klustret istället för att montera lagringsutrymmet direkt.

Avere vFXT-klustret cachelagrar filerna när de begärs. Upprepade begäranden kan hanteras från cachen i drygt 80 procent av gångerna.

### <a name="compatibility"></a>Kompatibilitet

* Kompatibelt med NAS-maskinvarusystem från NetApp eller Dell EMC Isilon
* Kompatibelt med Azure Blob
* Använder NFSv3- eller SMB2-protokoll

Avere vFXT för Azure använder följande Azure-resurser:

|Azure-komponent|   |
|----------|-----------|
|Virtuella datorer|3 eller fler E32s_v3|
|Premium SSD-lagring|200 GB OS-utrymme plus 1 TB till 4 TB cacheutrymme per nod |
|Lagringskonto (valfritt) |v2|
|Data backend-lagring (valfritt) | En tom LRS Blob-container |

## <a name="next-steps"></a>Nästa steg

Läs dessa artiklar för att planera och skapa din egen Avere vFXT för Azure-distribution.

* [Planera ditt system](avere-vfxt-deploy-plan.md)
* [Distributionsöversikt](avere-vfxt-deploy-overview.md)
* [Skapa vFXT](avere-vfxt-deploy.md)
