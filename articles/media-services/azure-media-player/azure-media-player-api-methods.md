---
title: API-metoder för Azure Media Player
description: Med Azure Media Player-API:et kan du interagera med videon via JavaScript, oavsett om webbläsaren spelar upp videon via HTML5-video, Flash, Silverlight eller någon annan uppspelningsteknik som stöds.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727273"
---
# <a name="api"></a>API #

Med Azure Media Player-API:et kan du interagera med videon via JavaScript, oavsett om webbläsaren spelar upp videon via HTML5-video, Flash, Silverlight eller någon annan uppspelningsteknik som stöds.

## <a name="referencing-the-player"></a>Refererar till spelaren ##

Om du vill använda API-funktionerna behöver du åtkomst till spelarobjektet. Lyckligtvis är det lätt att få. Du behöver bara se till att din videotagg har ett ID. Exempelinbäddningskoden har `vid1`ett ID på . Om du har flera videor på en sida kontrollerar du att varje videotagg har ett unikt ID.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Om spelaren inte har initierats ännu via attributet data-setup eller någon annan metod, kommer detta också att initiera spelaren.

## <a name="wait-until-the-player-is-ready"></a>Vänta tills spelaren är redo ##

Hur tid det tar för Azure Media Player att konfigurera videon och API:et varierar beroende på vilken uppspelningsteknik som används. HTML5 kommer ofta att vara mycket snabbare att ladda än Flash eller Silverlight. Av den anledningen bör spelarens "färdiga" funktion användas för att utlösa alla koder som kräver spelarens API.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

ELLER

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API-metoder ##

Nu när du har tillgång till en färdig spelare kan du styra videon, få värden eller svara på videohändelser. Api-funktionsnamnen för Azure Media Player försöker följa [HTML5-medie-API:et](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). Den största skillnaden är att getter/setter-funktioner används för videoegenskaper.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrering för evenemang ##
Händelser bör registreras direkt efter att ha initierat spelaren för första gången för att säkerställa att alla händelser rapporteras på lämpligt sätt till programmet, och bör göras utanför den färdiga händelsen.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Nästa steg ##

<!---Some context for the following links goes here--->
- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)