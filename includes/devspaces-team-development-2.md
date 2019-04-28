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
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368249"
---
### <a name="run-the-service"></a>Köra tjänsten

Om du vill köra tjänsten, trycka på F5 (eller skriver `azds up` i fönstret Terminal) att köra tjänsten. Tjänsten körs automatiskt i det valda utrymmet _dev/scott_. Kontrollera att tjänsten körs i sin egen utrymme genom att köra `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Lägg märke till en instans av *mywebapi* körs nu i den _dev/scott_ utrymme. Den version som körs _dev_ körs fortfarande, men det inte visas.

Lista över URL: er för utrymmet genom att köra `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Lägg märke till URL: en för offentlig åtkomst till-punkt för *webfrontend* har prefixet *scott.s*. Den här webbadressen är unik för utrymmet _dev/scott_. Den här URL-prefix talar om för Ingress-kontrollanten för att dirigera begäranden till den _dev/scott_ version av en tjänst. När en begäran med den här URL: en hanteras av Dev blanksteg, Ingress-kontrollant först försöker vidarebefordra begäran till den *webfrontend* tjänsten på den _dev/scott_ utrymme. Om detta misslyckas begäran kommer att dirigeras till den *webfrontend* tjänsten på den _dev_ utrymme som reserv. Observera också att det finns en localhost-URL för att ansluta till tjänsten via localhost med hjälp av Kubernetes *port och tydlig* funktioner. Läs mer om URL-adresser och routning i Azure Dev blanksteg, [hur Azure Dev blanksteg fungerar och är konfigurerad](../articles/dev-spaces/how-dev-spaces-works.md).



![Utrymme Routning](../articles/dev-spaces/media/common/Space-Routing.png)

Med den här inbyggda Azure Dev Spaces-funktionen kan du testa kod i ett delat utrymme utan att varje utvecklare måste återskapa hela tjänstestacken i deras respektive utrymme. Den här typen av routning kräver att appkoden vidarebefordrar spridningshuvuden, vilket visas i föregående steg i den här guiden.

### <a name="test-code-in-a-space"></a>Testa koden i ett utrymme
Du testar den nya versionen av *mywebapi* tillsammans med *webfrontend* genom att öppna webbläsaren på URL:en för offentlig åtkomst för *webfrontend* och gå till sidan Om. Nu bör ditt nya meddelande visas.

Ta nu bort delen ”scott.s.” från webbadressen och uppdatera webbläsaren. Nu bör du se det gamla beteendet (med *mywebapi*-versionen som körs i _dev_).

När du har ett _dev_-utrymme som alltid innehåller dina senaste ändringar, och under förutsättning att ditt program är utformat för att dra nytta av DevSpace utrymmesbaserade routning enligt beskrivningen i den här delen av självstudien, kan du förhoppningsvis se hur mycket enklare det blir med Dev Spaces att testa nya funktioner inom ramen för det större programmet. I stället för att behöva distribuera _alla_ tjänster till ditt privata utrymme kan du skapa ett privat utrymme som härleds från _dev_ och endast ”införa” de tjänster du faktiskt arbetar med. Dev Spaces-routningsinfrastrukturen hanterar resten genom att utnyttja så många tjänster utanför ditt privata adressutrymme som går att hitta, samtidigt som det återgår till den senaste versionen som körs i utrymmet _dev_. Ännu bättre är att _flera_ utvecklare aktivt kan utveckla olika tjänster samtidigt i sina egna utrymmen utan att störa varandra.

### <a name="well-done"></a>Bra gjort!
Du har slutfört guiden för att komma igång! Du har lärt dig att:

> [!div class="checklist"]
> * Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
> * Iterativt utveckla kod i containrar.
> * Oberoende utvecklar två separata tjänster och använder Kubernetes DNS-tjänstidentifiering för att anropa en annan tjänst.
> * Effektivt utvecklar och testar din kod i en teammiljö.
> * Upprätta en baslinje med funktioner med hjälp av Dev Spaces för att enkelt testa isolerade ändringar inom ramen för ett större mikrotjänstprogram

Nu när du har utforskat Azure Dev blanksteg, [dela ditt dev-adressutrymme med en gruppmedlem](../articles/dev-spaces/how-to/share-dev-spaces.md) och börja samarbeta.

## <a name="clean-up"></a>Rensa
Om du vill ta bort en Azure Dev Spaces-instans i ett kluster fullständigt, inklusive alla utvecklarmiljöer och tjänster som körs i den, använder du kommandot `az aks remove-dev-spaces`. Kom ihåg att den här åtgärden inte kan ångras. Du kan lägga till stöd för Azure Dev Spaces på klustret igen, men det blir som om du börjar om på nytt. Dina gamla tjänster och utrymmen kommer inte att återställas.

Följande exempel visar en lista över Azure Dev Spaces-kontrollanter i din aktiva prenumeration och sedan tar det bort den Azure Dev Spaces-kontrollant som är associerad med AKS-klustret ”myaks” i resursgruppen ”myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```