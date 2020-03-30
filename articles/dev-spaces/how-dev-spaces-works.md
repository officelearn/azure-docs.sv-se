---
title: Så här fungerar Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver de processer som driver Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234968"
---
# <a name="how-azure-dev-spaces-works"></a>Så här fungerar Azure Dev Spaces

Att utveckla ett Kubernetes-program kan vara en utmaning. Du behöver konfigurationsfiler för Docker och Kubernetes. Du måste ta reda på hur du testar ditt program lokalt och interagerar med andra beroende tjänster. Du kan behöva hantera att utveckla och testa flera tjänster på en gång och med ett team av utvecklare.

Azure Dev Spaces ger dig flera sätt att snabbt iterera och felsöka Kubernetes-program och samarbeta med ditt team. I den här artikeln beskrivs vad Azure Dev Spaces kan göra och hur det fungerar.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Snabbt iterera och felsöka ditt Kubernetes-program

Azure Dev Spaces minskar arbetet med att utveckla, testa och iterera ditt Kubernetes-program i kontexten för AKS-klustret. Denna minskning av ansträngningen gör det möjligt för utvecklare att fokusera på affärslogiken i sina program och inte konfigurera sina tjänster för att köras i Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Anslut din utvecklingsmaskin till AKS

Med Azure Dev Spaces kan du ansluta utvecklingsdatorn till AKS-klustret, så att du kan köra och felsöka kod på utvecklingsdatorn som om den kördes i klustret. Azure Dev Spaces omdirigerar trafik mellan ditt anslutna AKS-kluster genom att köra en pod på klustret som fungerar som en fjärragent för att omdirigera trafik mellan utvecklingsdatorn och klustret. Med den här omdirigeringen av trafiken kan kod på utvecklingsdatorn och tjänster som körs i AKS-klustret kommunicera som om de vore i samma AKS-kluster. Mer information om hur du ansluter utvecklingsmaskinen till AKS finns i [Så här fungerar det att ansluta utvecklingsdatorn till AKS-klustret][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Kör koden i AKS

Förutom att omdirigera trafik mellan utvecklingsdatorn och AKS-klustret kan du med Azure Dev Spaces konfigurera och snabbt köra koden direkt i AKS. Med Visual Studio, Visual Studio Code eller Azure Dev Spaces CLI överför Azure Dev spaces koden till klustret och sedan skapa och köra den. Azure Dev spaces kan också synkronisera kodändringar på ett intelligent sätt och starta om tjänsten för att återspegla ändringar efter behov. När du kör koden strömmas byggloggar och HTTP-spårningar tillbaka till klienten så att du kan övervaka förloppet och diagnostisera eventuella problem. Du kan också använda Azure Dev Spaces för att koppla felsökningen i Visual Studio- och Visual Studio-koden till Java-, Node.js- och .NET Core-tjänster. Mer information finns i Så här [fungerar förberedelserna för ett projekt för Azure Dev Spaces,][how-it-works-prep] [Hur du kör koden med Azure Dev Spaces][how-it-works-up]och Hur [fjärrfelsökning av koden med Azure Dev Spaces fungerar][how-it-works-remote-debugging].

## <a name="team-development"></a>Teamutveckling

Azure Dev Spaces hjälper team att arbeta produktivt med sitt program på samma AKS-kluster utan att vara störande.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligent routning mellan dev spaces

Med Azure Dev Spaces kan ett team dela ett enda AKS-kluster som kör ett molnbaserat program och skapa isolerade utvecklingsutrymmen där teamet kan utveckla, testa och felsöka utan att störa de andra utvecklingsutrymmena. En baslinjeversion av programmet körs i ett rotdelegeringsutrymme. Gruppmedlemmar skapar sedan oberoende underordnade utvecklingsutrymmen baserat på rotutrymmet för utveckling, testning och felsökning av ändringar i programmet. Genom routningsfunktionerna i Dev Spaces kan underordnade utvecklingsutrymmen dirigera begäranden mellan tjänster som körs i det underordnade utvecklingsutrymmet och det överordnade utvecklingsutrymmet. Med den här routningen kan utvecklare köra sin egen version av en tjänst samtidigt som de återanvänder beroende tjänster från det överordnade utrymmet. Varje barnutrymme har sin egen unika webbadress, som kan delas och nås av andra för samarbete. Mer information om hur routning fungerar i Azure Dev Spaces finns i Så här [fungerar routning med Azure Dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Live testar en öppen pull-begäran

Du kan också använda GitHub-åtgärder med Azure Dev Spaces för att testa ändringar i ditt program i en pull-begäran direkt i klustret innan du slår samman. Azure Dev Spaces kan automatiskt distribuera en granskningsversion av programmet till klustret, så att författaren och andra gruppmedlemmar kan granska ändringarna i kontexten för hela programmet. Med hjälp av routningsfunktionerna i Azure Dev Spaces distribueras den här granskningsversionen av programmet också till klustret utan att påverka andra utvecklingsutrymmen. Alla dessa funktioner gör att du tryggt kan godkänna och slå samman pull-begäranden. Information om hur du ser ett exempel på GitHub-åtgärder och Azure Dev Spaces finns i [GitHub-åtgärder & Azure Kubernetes Service][pr-flow].

## <a name="next-steps"></a>Nästa steg

Mer om du vill komma igång med att ansluta din lokala utvecklingsdator till AKS-klustret finns i [Anslut utvecklingsmaskinen till ett AKS-kluster][connect].

Information om hur du kommer igång med Azure Dev Spaces för teamutveckling finns [i teamutvecklingen i snabbstarten för Azure Dev Spaces.][quickstart-team]

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development