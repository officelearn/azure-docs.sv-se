---
title: Tagg och version avbildningar i Azure Container Registry
description: Bästa praxis för taggning och versionshantering Docker-behållaravbildningar
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800398"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Rekommendationer för taggning och versionshantering behållaravbildningar

När du pushar distribuera behållaravbildningar till ett behållarregister och distribuerar dem, behöver du en strategi för avbildning taggning och versionshantering. Den här artikeln beskrivs två metoder och var varje passar under behållaren livscykel:

* **Stabil taggar** -taggar som du återanvända att tyda på en högre eller lägre version som *mycontainerimage:1.0*.
* **Unikt taggar** – en annan tagg för varje bild som du har överfört till ett register som *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabil taggar

**Rekommendationen**: Använd stabila taggar för att underhålla **basera avbildningar** för din behållare bygger. Undvik distributioner med stabila taggar, eftersom taggarna fortsätta att ta emot uppdateringar och kan leda till inkonsekvenser i produktionsmiljöer.

*Stabil taggar* innebära en utvecklare eller ett build-system kan fortsätta att hämta en specifik tagg som fortsätter att hämta uppdateringar. Stabil innebär inte innehållet är låsta. Stabil innebär i stället bör avbildningen vara en stabil för syftet med den här versionen. Om du vill få ”stabil”, kan den betjänas för att tillämpa säkerhetsuppdateringar eller uppdateringar framework.

### <a name="example"></a>Exempel

Ett team med ramverk levereras version 1.0. De redan känner till de skickar uppdateringar, inklusive mindre uppdateringar. För att stödja stabil taggar för en viss högre och den lägre version, har de två uppsättningar med stabila taggar.

* `:1` – en stabil tagg för den huvudsakliga versionen. `1` representerar den ”senaste” eller ”senaste” 1.*-versionen.
* `:1.0`-en stabil tagg för version 1.0, vilket gör att utvecklare för att binda till uppdateringar av 1.0 och inte vara återställts till 1.1 när det har släppts.

Teamet använder också den `:latest` tagg, som pekar mot den senaste stabila taggen oavsett vilken den aktuella högre versionen.

När basavbildningen uppdateringar är tillgängliga eller alla typer av servicing utgivningen av framework bilder med stabila taggar uppdateras till den senaste sammanfattad som representerar den senaste stabila versionen av den här versionen.

I det här fallet servas både de överordnade och underordnade taggarna kontinuerligt. Från ett scenario med basavbildningen kan detta bild ägaren ger underhållna bilder.

## <a name="unique-tags"></a>Unikt taggar

**Rekommendationen**: Använd unika taggar för **distributioner**, särskilt i en miljö som kan skalas på flera noder. Vill du förmodligen avsiktlig distributioner av en konsekvent version av komponenter. Om din behållare startas om eller en initierare skalas ut flera instanser, dina värdar inte av misstag hämta en nyare version är inkonsekvent med de andra noderna.

Unikt taggning bara innebär att varje bild som skickas till ett register har en unik tagg. Taggar är inte återanvändas. Det finns flera mönster som du kan följa för att generera unika taggar, inklusive:

* **Datum-och tidsstämpel** -den här metoden är ganska vanliga eftersom framgår tydligt när avbildningen har skapats. Men hur att korrelera tillbaka till ditt system? Har du hitta den version som har slutförts på samma gång? Vilken tidszon är du med? Alla build-system kalibreras till UTC?
* **Git-incheckning** – den här metoden fungerar förrän du startar stöder basavbildningen uppdateringar. Om en grundläggande uppdateringar händer, build-system som inleder med samma Git-incheckning som den tidigare versionen. Basavbildningen har dock nytt innehåll. I allmänhet en Git-incheckning tillhandahåller en *halvstrukturerade*-stabil taggen.
* **Manifest sammanfattad** -varje behållaravbildning som skickas till ett behållarregister är associerad med ett manifest som identifieras av ett unikt SHA-256-hash eller sammanfattad. Unik, är digest lång svårläst och uncorrelated med build-miljön.
* **Skapa ID** – det här alternativet kan vara det bästa alternativet eftersom det är sannolikt inkrementell, och du kan korrelera tillbaka till den specifika versionen att hitta alla artefakter och loggar. Som ett manifest sammanfattad kan det dock vara svårt för en människa att läsa.

  Om din organisation har flera build-system, skapar prefix taggen med namnet för build-system är en variant på det här alternativet: `<build-system>-<build-id>`. Exempel: du kan skilja mellan versioner från API-teamet Jenkins build-system och webbtjänst teamets Azure Pipelines skapa system.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad beskrivning av begreppen i den här artikeln finns i bloggposten [Docker-märkning: Bästa metoder för taggning och versionshantering docker-avbildningar](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

För att maximera prestandan och den kostnadseffektiva användningen av Azure container registry, se [bästa praxis för Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

