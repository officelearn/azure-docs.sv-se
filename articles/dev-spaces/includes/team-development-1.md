---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 2a6118bd23c6e8319ad4fa26a266948a4dad1b9f
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939176"
---
Hittills har du kört programmets kod som om du var den enda utvecklaren som arbetade med appen. I det här avsnittet lär du dig hur Azure Dev Spaces förenklar utvecklingen i ett team:
* Göra det möjligt för ett utvecklarteam att arbeta i samma miljö, genom att arbeta i en delad utvecklarmiljö eller i särskilda utvecklarmiljöer efter behov..
* Varje utvecklare kan arbeta med sin egen kod utan att oroa sig för att skada andras kod.
* Testa koden från slutpunkt till slutpunkt, innan koden checkas in, utan att skapa simulerade objekt eller beroendesimuleringar.

### <a name="challenges-with-developing-microservices"></a>Utmaningar vid utveckling av mikrotjänster
Exempelprogrammet är inte särskilt komplext för tillfället. Men i en riktig utvecklingsprocess börjar utmaningarna snart att hopa sig allt eftersom du lägger till fler tjänster och utvecklingsteamet växer.

Föreställ dig att du arbetar med en tjänst som interagerar med en rad andra tjänster.

- I detta fall kan det vara orealistiskt att köra allt lokalt. Utvecklingsdatorn kanske inte har tillräckligt med resurser för att köra hela programmet. Eller så kanske programmet har slutpunkter som måste vara offentligt åtkomliga (till exempel om ditt program svarar på en webhook från ett SaaS-program).

- Du kan försöka att bara köra de tjänster som du är beroende av, men det innebär att du måste känna till hela beroendekedjan (till exempel beroenden av beroenden). Det kan också vara svårt att veta hur du ska skapa och köra beroendena om du inte arbetat med dem.
- Vissa utvecklare försöker kringgå problemet genom att simulera eller testa många av tjänstberoendena. Detta kan hjälpa ibland, men hanteringen av dessa simuleringar kan snabbt förvandlas till ett tidskrävande utvecklingsarbete i sig. Dessutom leder den här metoden till att utvecklarmiljön skiljer sig från produktionen, och små buggar kan smyga sig in.
- Av detta följer att all slags testning från slutpunkt till slutpunkt blir komplicerad. Realistiskt sett kan integrationstestning endast ske när koden checkats in, vilket kan medföra problem senare i utvecklingscykeln.

![](../media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>Arbeta i en delad utvecklarmiljö
Med Azure Dev Spaces kan du konfigurera en *delad* utvecklarmiljö i Azure. Varje utvecklare kan fokusera på sin del av programmet, och kan arbeta med *icke incheckad kod* i en utvecklarmiljö som redan innehåller alla andra tjänster och molnresurser som krävs i det aktuella scenariot. Beroenden är alltid uppdaterade och utvecklarna arbetar på ett sätt som återspeglar produktionsmiljön.

### <a name="work-in-your-own-space"></a>Arbeta i ditt eget utrymme
När du utvecklar kod för en tjänst är koden sällan perfekt förrän du väljer att checka in den. Du arbetar ju fortfarande med den, testar den och experimenterar med lösningar. Azure Dev Spaces införlivar begreppet **utrymme**, som innebär att du kan arbeta isolerat, utan att oroa dig för att skada andra teammedlemmars kod.

> [!Note]
> Innan du fortsätter ska du stänga alla VS Code-fönster för båda tjänsterna och sedan köra `azds up -d` i var och en av tjänstens rotmappar. (Detta är en begränsning för förhandsversion.)

Låt oss ta en närmare titt på var tjänsterna körs. Kör kommandot `azds list` så ser du ett resultat som liknar följande:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     http://webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

Utrymmeskolumnen visar att båda tjänsterna körs i ett utrymme som heter `default`. Om någon öppnar den offentliga URL:en och navigerar till webbprogrammet anropas kodsökvägen som du tidigare skrev som körs via båda tjänsterna. Anta nu att du vill fortsätta utveckla `mywebapi`. Hur kan du göra kodändringar och testa dem utan att störa andra utvecklare som använder utvecklarmiljön? Det gör du genom att konfigurera ett eget utrymme.

### <a name="create-a-dev-space"></a>Skapa en utvecklarmiljö
För att köra din egen version av `mywebapi` i ett annat utrymme än `default` kan du skapa ett eget utrymme med följande kommando:

``` 
azds space select --name scott
```

När du uppmanas väljer du `default` som **parent dev space** (överordnad utvecklarmiljö). Det innebär att det nya utrymmet, `default/scott`, härleds från utrymmet `default`. Vi kommer snart att se hur det hjälper oss med testning. 

I exemplet ovan har jag använt mitt namn för det nya utrymmet så att det kan identifieras av mina kolleger som är i utrymmet jag arbetar i, men du kan kalla det vad du vill och vara flexibel kring vad det betyder, som ”sprint4” eller ”demo”.

Kör kommandot `azds space list` för att se en lista över alla utrymmen i utvecklarmiljön. En asterisk (*) visas bredvid det markerade utrymmet. I ditt fall valdes utrymmet som heter ”default/scott” när det skapades. Du kan välja ett annat utrymme när som helst med kommandot `azds space select`.
