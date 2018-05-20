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
ms.openlocfilehash: 96a749c0cb59759e9294f52bd4f631d7fdc2275f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
Så länge har du kört din programkod som om du bara utvecklare som arbetar på appen. I det här avsnittet lär du dig hur Azure Dev blanksteg förenklar utveckling i grupp:
* Aktivera ett team för utvecklare att arbeta i samma utvecklingsmiljö.
* Har stöd för varje iteration av koden i isolering utan fruktan för bryta andra utvecklare.
* Testa koden slutpunkt till slutpunkt, innan koden commit, utan att behöva skapa mocks eller simulera beroenden.

## <a name="challenges-with-developing-microservices"></a>Problem med att utveckla mikrotjänster
Exempelprogrammet är mycket komplex inte för tillfället. Men i verkliga development snart utmaningar framkommer när du lägger till fler tjänster och Utvecklingsteamet växer.

Bild för ditt konto arbetar på en tjänst som interagerar med mängder av andra tjänster.

- Det kan bli orealistiskt att köra allt lokalt för utveckling. Dev-datorn kanske inte har tillräckligt med resurser för att köra hela appen. Eller, kanske din app har slutpunkter som måste vara offentligt nås (till exempel din app svarar på en webhook från en SaaS-app).

- Du kan försöka att bara köra tjänster som du beroende, men det innebär att du behöver känner fullständig stängningen av beroenden (till exempel beroenden av beroenden). Eller så är några inte enkelt att veta hur du skapar och kör dina beroenden eftersom du inte arbeta med dem.
- Vissa utvecklare använda simulera eller mocking in många av deras tjänstberoenden. Den här metoden kan ibland, men hantera dessa mocks snart kan ta över sin egen utvecklingsarbetet. Dessutom har den här metoden leder till din utvecklingsmiljö söker mycket olika till produktion och diskret buggar kan krypa.
- Följer att göra någon typ av slutpunkt till slutpunkt testning svårt. Testning kan endast normalt inträffa efter commit, vilket innebär att du ser problem senare i utvecklingscykeln.

![](../media/common/microservices-challenges.png)


## <a name="work-in-a-shared-development-environment"></a>Arbeta i en delad utvecklingsmiljö
Azure Dev blanksteg, kan du ställa in en *delade* utvecklingsmiljö i Azure. Varje utvecklare kan fokusera på bara sin del av programmet och upprepade gånger kan utveckla *före kopplar kod* i en miljö som redan innehåller alla andra tjänster och molnresurser som deras scenarier är beroende av. Beroenden är alltid uppdaterade och utvecklare som arbetar på ett sätt som speglar produktion.

## <a name="work-in-your-own-space"></a>Fungera med din egen sida
När du utvecklar koden för tjänsten och innan du är redo att checka in kod ofta inte finns i ett fungerande tillstånd. Du fortfarande upprepade gånger shaping, testa den och experimenterar med lösningar. Azure Dev blanksteg tillhandahåller begreppet en **utrymme**, vilket tillåter dig att arbeta i isolering utan fruktan för att dela dina gruppmedlemmar.

> [!Note]
> Innan du fortsätter, Stäng alla fönster för VS-kod för båda tjänsterna och kör sedan `azds up -d` i varje tjänstens rotmappar. (Detta är en begränsning för förhandsgranskning.)

Låt oss ta en närmare titt på var tjänsterna som körs. Kör den `azds list` , och du kommer att se utdata som liknar följande:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     https://webfrontend-contosodev.1234abcdef.westeurope.aksapp.io
```

Kolumnen utrymme visar att båda tjänsterna körs i ett utrymme med namnet `default`. Alla som öppnar offentlig URL och navigerar till webbprogrammet ska anropa kodsökvägen nämnt tidigare som körs via båda tjänsterna. Nu anta att du vill fortsätta utveckla `mywebapi`. Hur kan du göra kodändringar och testa dem och inte avbryter andra utvecklare som använder dev-miljö? För att göra det, ska du ställa in din egen sida.

## <a name="create-a-space"></a>Skapa ett utrymme
Att köra en egen version av `mywebapi` i ett utrymme än `default`, du kan skapa din egen sida med hjälp av följande kommando:

``` 
azds space create --name scott
```

Jag har använt mitt namn för det nya utrymmet så att den kan identifieras mina datorer som det är området jag arbetar i i exemplet ovan, men du kan anropa den något du tycker och vara flexibla om vad det innebär, som 'sprint4' eller 'demo.'

Kör den `azds space list` kommandot för att se en lista över alla blanksteg i dev-miljö. En asterisk (*) visas bredvid det markerade utrymmet. I ditt fall valdes automatiskt området med namnet 'scott' när den skapades. Du kan välja en annan utrymme när som helst med den `azds space select` kommando.
