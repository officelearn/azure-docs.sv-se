---
title: Metodtips för bildtagg
description: Metodtips för att tagga och versionsbehöra Docker-behållaravbildningar när du trycker av avbildningar till och hämtar avbildningar från ett Azure-behållarregister
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445743"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Rekommendationer för taggning och versionshantering av behållaravbildningar

När du trycker på distribution av behållaravbildningar till ett behållarregister och sedan distribuerar dem behöver du en strategi för avbildningstaggning och versionshantering. I den här artikeln beskrivs två metoder och var och en passar under behållarens livscykel:

* **Stabila taggar** - Taggar som du återanvänder, till exempel för att ange en större eller delversion som *mycontainerimage:1.0*.
* **Unika taggar** - En annan tagg för varje bild som du skickar till ett register, till exempel *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabila taggar

**Rekommendation**: Använd stabila taggar för att underhålla **basavbildningar** för behållarbyggena. Undvik distributioner med stabila taggar, eftersom dessa taggar fortsätter att ta emot uppdateringar och kan införa inkonsekvenser i produktionsmiljöer.

*Stabila taggar* innebär att en utvecklare, eller ett byggsystem, kan fortsätta att hämta en specifik tagg, som fortsätter att hämta uppdateringar. Stabil betyder inte att innehållet är fryst. Snarare innebär stabil bilden bör vara stabil för avsikten med den versionen. För att förbli "stabil" kan det vara bearbetat att använda säkerhetskorrigeringar eller ramuppdateringar.

### <a name="example"></a>Exempel

Ett ramteam levereras version 1.0. De vet att de kommer att leverera uppdateringar, inklusive mindre uppdateringar. För att stödja stabila taggar för en viss större och mindre version har de två uppsättningar stabila taggar.

* `:1`– en stabil tagg för huvudversionen. `1`representerar den "nyaste" eller "senaste" 1.* versionen.
* `:1.0`- En stabil tagg för version 1.0, så att en utvecklare att binda till uppdateringar av 1.0, och inte rullas fram till 1.1 när den släpps.

Teamet använder också `:latest` taggen, som pekar på den senaste stabila taggen, oavsett vad den aktuella huvudversionen är.

När basavbildningsuppdateringar är tillgängliga, eller någon typ av underhållsutgåva av ramverket, uppdateras avbildningar med stabila taggar till den senaste sammandraget som representerar den senaste stabila versionen av den versionen.

I det här fallet servas kontinuerligt både de större och mindre taggarna. Från ett basavbildningsscenario gör detta det möjligt för avbildningsägaren att tillhandahålla servade avbildningar.

### <a name="delete-untagged-manifests"></a>Ta bort otaggade manifest

Om en bild med en stabil tagg uppdateras är den tidigare taggade bilden otaggad, vilket resulterar i en överbliven bild. Den föregående avbildningens manifest och unika lagerdata finns kvar i registret. Om du vill behålla registerstorleken kan du med jämna mellanrum ta bort otaggade manifest som härrör från stabila avbildningsuppdateringar. Ta till exempel bort otaggade manifest [som](container-registry-auto-purge.md) är äldre än en angiven varaktighet automatiskt eller ange en [bevarandeprincip](container-registry-retention-policy.md) för otaggade manifest.

## <a name="unique-tags"></a>Unika taggar

**Rekommendation**: Använd unika taggar för **distributioner,** särskilt i en miljö som kan skalas på flera noder. Du vill förmodligen ha avsiktliga distributioner av en konsekvent version av komponenter. Om behållaren startas om eller en orchestrator skalar ut fler instanser hämtar inte värdarna av misstag en nyare version, som inte är i förhållande till de andra noderna.

Unik taggning innebär helt enkelt att varje bild som skjuts till ett register har en unik tagg. Taggar återanvänds inte. Det finns flera mönster du kan följa för att generera unika taggar, inklusive:

* **Datum-tid stämpel** - Detta tillvägagångssätt är ganska vanligt, eftersom du tydligt kan berätta när bilden byggdes. Men, hur korrelera tillbaka det till din bygga system? Måste du hitta den bygga som slutfördes samtidigt? Vilken tidszon är du i? Är alla dina byggsystem kalibrerade till UTC?
* **Git commit** – Den här metoden fungerar tills du börjar stödja basavbildningsuppdateringar. Om en basavbildningsuppdatering sker startar ditt byggsystem med samma Git-commit som den tidigare versionen. Basbilden har dock nytt innehåll. I allmänhet ger en Git commit en *semi-stabil*tagg.
* **Manifestsammanfattning** - Varje behållaravbildning som skjuts till ett behållarregister är associerad med ett manifest som identifieras av en unik SHA-256-hash eller sammanfattning. Medan unik, är smälta lång, svår att läsa, och okorrelerad med din bygga miljö.
* **Build ID** - Det här alternativet kan vara bäst eftersom det är sannolikt inkrementellt, och det gör att du kan korrelera tillbaka till den specifika versionen för att hitta alla artefakter och loggar. Men som en uppenbar smälta, kan det vara svårt för en människa att läsa.

  Om din organisation har flera byggsystem är det en variant av det `<build-system>-<build-id>`här alternativet om du förefixar taggen med byggsystemnamnet: . Du kan till exempel skilja byggen från API-teamets Jenkins-byggsystem och webbteamets Azure Pipelines-byggsystem.

### <a name="lock-deployed-image-tags"></a>Låsa distribuerade bildtaggar

Vi rekommenderar att du [låser](container-registry-image-lock.md) alla distribuerade avbildningstaggar genom att ange attributet `write-enabled` på `false`. Den här metoden förhindrar att du oavsiktligt tar bort en avbildning från registret och eventuellt stör dina distributioner. Du kan inkludera låssteget i versionspipelinen.

Om du låser en distribuerad avbildning kan du fortfarande ta bort andra, odeployed avbildningar från registret med hjälp av Azure Container Registry-funktioner för att underhålla registret. Du kan till exempel [rensa](container-registry-auto-purge.md) otaggade manifest eller olåsta bilder som är äldre än en angiven varaktighet automatiskt, eller ange en [bevarandeprincip](container-registry-retention-policy.md) för otaggade manifest.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad diskussion om begreppen i den här artikeln finns i blogginlägget [Docker-taggning: Metodtips för taggning och versionshantering av dockerbilder](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Information om hur du maximerar prestanda och kostnadseffektiv användning av ditt Azure-behållarregister finns [i Metodtips för Azure Container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

