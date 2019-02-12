---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 1f6e0a8fd2cc14877b98bc12b0d2c8632edbbbb9
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664422"
---
# <a name="team-development-with-azure-dev-spaces"></a>Teamutveckling med Azure Dev Spaces

I den här självstudien lär du dig hur ett team med utvecklare kan samarbeta samtidigt i samma Kubernetes-kluster med hjälp av Dev Spaces.

## <a name="learn-about-team-development"></a>Lär dig mer om utveckling i team
Hittills har du kört programmets kod som om du var den enda utvecklaren som arbetade med appen. I det här avsnittet lär du dig hur Azure Dev Spaces förenklar utvecklingen i ett team:
* Göra det möjligt för ett utvecklarteam att arbeta i samma miljö, genom att arbeta i en delad utvecklingsmiljö eller i särskilda utvecklingsmiljöer efter behov.
* Varje utvecklare kan arbeta med sin egen kod utan att oroa sig för att skada andras kod.
* Testa koden från slutpunkt till slutpunkt, innan koden checkas in, utan att skapa simulerade objekt eller beroendesimuleringar.

### <a name="challenges-with-developing-microservices"></a>Utmaningar vid utveckling av mikrotjänster
Exempelprogrammet är inte särskilt komplext för tillfället. Men i en riktig utvecklingsprocess börjar utmaningarna snart att hopa sig allt eftersom du lägger till fler tjänster och utvecklingsteamet växer. I detta fall kan det vara orealistiskt att köra allt lokalt.

* Utvecklingsdatorn har kanske inte tillräckligt med resurser för att köra varje tjänst som du behöver på samma gång.
* Vissa tjänster kan behöva vara offentligt nåbara. Till exempel kan en tjänst behöva ha en slutpunkt som svarar på en webhook.
* Om du vill köra en delmängd av tjänster behöver du känna till den fullständiga beroendehierarkin mellan alla dina tjänster. Det kan vara svårt att fastställa det här, särskilt när det antal tjänster som du använder ökar.
* Vissa utvecklare försöker kringgå problemet genom att simulera eller testa många av tjänstberoendena. Den här metoden kan vara till hjälp, men att hantera dessa simuleringar kan snabbt börja påverka utvecklingskostnaden. Dessutom gör den metoden att din utvecklingsmiljö ser väldigt annorlunda ut jämfört med produktionen, vilket gör att svårupptäckta buggar kan uppstå.
* Av detta följer att all slags integreringstestning blir komplicerad. Realistiskt sett kan integrationstestning endast ske när koden checkats in, vilket kan medföra problem senare i utvecklingscykeln.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Arbeta i en delad utvecklarmiljö
Med Azure Dev Spaces kan du konfigurera en *delad* utvecklarmiljö i Azure. Varje utvecklare kan fokusera på sin del av programmet, och kan arbeta med *icke incheckad kod* i en utvecklarmiljö som redan innehåller alla andra tjänster och molnresurser som krävs i det aktuella scenariot. Beroenden är alltid uppdaterade och utvecklarna arbetar på ett sätt som återspeglar produktionsmiljön.

### <a name="work-in-your-own-space"></a>Arbeta i ditt eget utrymme
När du utvecklar kod för en tjänst är koden sällan perfekt förrän du väljer att checka in den. Du arbetar ju fortfarande med den, testar den och experimenterar med lösningar. Azure Dev Spaces införlivar begreppet **utrymme**, som innebär att du kan arbeta isolerat, utan att oroa dig för att skada andra teammedlemmars kod.

## <a name="use-dev-spaces-for-team-development"></a>Använda Dev Spaces för teamutveckling
Nu demonstrerar vi de här idéerna via ett konkret exempel med hjälp av vårt exempelprogram *webfrontend* -> *mywebapi*. Vi tänker oss ett scenario där utvecklaren Scott behöver göra en ändring i tjänsten *mywebapi*, och *enbart* den tjänsten. *webfrontend* behöver inte ändras i Scotts uppdatering.

_Utan_ användning av Dev Spaces skulle Scott vara begränsad till vissa sätt att utveckla och testa sina uppdatering. Inget av dessa lämpar sig särskilt väl:
* Köra ALLA komponenter lokalt. Det här kräver en kraftfullare utvecklingsdator med Docker samt potentiellt MiniKube installerat.
* Köra ALLA komponenter i en isolerad namnrymd på Kubernetes-klustret. Eftersom *webfrontend* inte ändras utgör det här ett slöseri med klusterresurser.
* Köra ENDAST *mywebapi* och göra manuell REST-anrop för att testa. Det här testar inte flödet fullständigt från slutpunkt till slutpunkt.
* Lägga till utvecklingsfokuserad kod i *webfrontend* som gör det möjligt för utvecklaren att skicka begäranden till en annan instans av *mywebapi*. Det här gör tjänsten *webfrontend* mer komplicerad.

### <a name="set-up-your-baseline"></a>Konfigurera din baslinje
Först behöver vi distribuera en baslinje för våra tjänster. Den här distributionen representerar den ”senaste kända välfungerande” så att du enkelt kan jämföra beteendet för din lokala kod jämfört med den incheckade versionen. Sedan skapar vi ett underordnat utrymme baserat på den här baslinjen så att vi kan testa ändringarna i *mywebapi* inom ramen för det större programmet.

1. Klona [Dev Spaces-exempelprogrammet](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Checka ut fjärrgrenen *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Välj utrymmet _dev_: `azds space select --name dev`. När du uppmanas att välja ett överordnat utvecklingsutrymme väljer du _\<none\>_ (inget).
1. Navigera till katalogen _mywebapi_ och kör: `azds up -d`
1. Navigera till katalogen _webfrontend_ och kör: `azds up -d`
1. Kör `azds list-uris` för att se den offentliga slutpunkten för _webfrontend_

> [!TIP]
> Stegen ovan konfigurerar en baslinje manuellt, men vi rekommenderar att team använder CI/CD för att automatiskt hålla baslinjen uppdaterad med incheckad kod.
>
> Läs vår [guide om hur du konfigurerar CI/CD med Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md) om du vill skapa ett arbetsflöde som liknar följande diagram.
>
> ![CI/CD-exempeldiagram](../articles/dev-spaces/media/common/ci-cd-complex.png)

I det här läget bör din baslinje köras. Kör kommandot `azds list-up` så ser du ett resultat som liknar följande:

```
Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

DevSpace-kolumnen visar att båda tjänsterna körs i ett utrymme som heter _dev_. Om någon öppnar den offentliga URL:en och navigerar till webbappen anropas den incheckade kodsökväg som körs via båda tjänsterna. Anta nu att du vill fortsätta utveckla _mywebapi_. Hur kan du göra kodändringar och testa dem utan att störa andra utvecklare som använder utvecklarmiljön? Det gör du genom att konfigurera ett eget utrymme.

### <a name="create-a-dev-space"></a>Skapa en utvecklarmiljö
Om du vill köra din egen version av _mywebapi_ i ett annat utrymme än _dev_ kan du skapa ett eget utrymme med hjälp av följande kommando:

```cmd
azds space select --name scott
```

När du uppmanas väljer du _dev_ som **parent dev space** (överordnad utvecklingsmiljö). Det innebär att det nya utrymmet, _dev/scott_, härleds från utrymmet _dev_. Vi kommer snart att se hur det hjälper oss med testning.

I linje med vårt inledande antagande har vi har använt namnet _scott_ för det nya utrymmet så att andra kan identifiera vem som arbetar i den. Men det kan kallas vad som helst och är flexibelt vad gäller innebörden, till exempel _sprint4_ eller _demo_. Oavsett vilket så fungerar _dev_ som baslinje för alla utvecklare som arbetar med en del av det här programmet:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Kör kommandot `azds space list` för att se en lista över alla utrymmen i utvecklarmiljön. Den _valda_ kolumnen anger vilket utrymme du för närvarande har valt (sant/falskt). I ditt fall valdes det utrymme som heter _dev/scott_ automatiskt när det skapades. Du kan välja ett annat utrymme när som helst med kommandot `azds space select`.

Nu ska vi se hur det fungerar i praktiken.