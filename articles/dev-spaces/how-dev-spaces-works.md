---
title: Så här fungerar Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver de processer som Power Spaces för Azure dev
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234968"
---
# <a name="how-azure-dev-spaces-works"></a>Så här fungerar Azure Dev Spaces

Det kan vara svårt att utveckla ett Kubernetes-program. Du behöver Docker-och Kubernetes-konfigurationsfiler. Du måste ta reda på hur du testar programmet lokalt och interagerar med andra beroende tjänster. Du kan behöva hantera utveckling och testning av flera tjänster samtidigt och med en grupp utvecklare.

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team. I den här artikeln beskrivs vad Azure dev Spaces kan göra och hur det fungerar.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Iterera snabbt och Felsök ditt Kubernetes-program

Azure dev Spaces minskar arbetet för att utveckla, testa och iterera ditt Kubernetes-program i samband med ditt AKS-kluster. Den här minskningen av ansträngning gör att utvecklare kan fokusera på affärs logiken i sina program och inte konfigurera deras tjänster för att köras i Kubernetes.

### <a name="connect-your-development-machine-to-aks"></a>Ansluta din utvecklings dator till AKS

Med Azure dev Spaces kan du ansluta din utvecklings dator till ditt AKS-kluster, så att du kan köra och felsöka kod på din utvecklings dator som om den kördes i klustret. Azure dev Spaces omdirigerar trafik mellan ditt anslutna AKS-kluster genom att köra en POD på klustret som fungerar som en fjärragent för att dirigera om trafiken mellan din utvecklings dator och klustret. Den här trafiken tillåter kod på din utvecklings dator och tjänster som körs i ditt AKS-kluster för att kommunicera som om de befann sig i samma AKS-kluster. Mer information om hur du ansluter din utvecklings dator till AKS finns i [så här ansluter du din utvecklings dator till ditt AKS-kluster][how-it-works-connect].

### <a name="run-your-code-in-aks"></a>Kör koden i AKS

Förutom att omdirigera trafik mellan din utvecklings dator och ditt AKS-kluster med Azure dev Spaces kan du konfigurera och snabbt köra koden direkt i AKS. Med Visual Studio, Visual Studio Code eller Azure dev Spaces CLI laddar Azure dev Spaces din kod till klustret och skapar och kör den sedan. Azure dev Spaces kan också intelligent synkronisera kod ändringar och starta om tjänsten för att avspegla ändringar vid behov. När du kör din kod strömmas build-loggar och HTTP-spår tillbaka till klienten så att du kan övervaka förloppet och diagnostisera eventuella problem. Du kan också använda Azure dev Spaces för att koppla fel sökaren i Visual Studio och Visual Studio Code till Java, Node. js och .NET Core Services. Mer information finns i [så här förbereder du ett projekt för Azure dev Spaces][how-it-works-prep], [hur du kör din kod med Azure dev Spaces][how-it-works-up]och [hur fjärrfelsökar din kod med Azure dev Spaces fungerar][how-it-works-remote-debugging].

## <a name="team-development"></a>Teamutveckling

Azure dev Spaces hjälper teamen att arbeta effektivt med sitt program i samma AKS-kluster utan att störas.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligent routning mellan dev Spaces

Med Azure dev Spaces kan ett team dela ett enda AKS-kluster som kör ett moln program och skapa isolerade dev-utrymmen där teamet kan utveckla, testa och felsöka utan att störa de andra dev Spaces. En bas linje version av programmet körs i ett rot utvecklings utrymme. Grupp medlemmar skapar sedan oberoende underordnade dev-rymder baserat på rot utrymmet för utveckling, testning och fel sökning av program ändringar. Med hjälp av routningsfunktioner i dev Spaces kan underordnade dev-rymder dirigera förfrågningar mellan tjänster som körs i det underordnade dev-utrymmet och det överordnade dev-utrymmet. Med den här routningen kan utvecklare köra sin egen version av en tjänst samtidigt som de använder beroende tjänster från det överordnade utrymmet. Varje underordnat utrymme har sin egen unika URL, som kan delas och användas av andra för samarbete. Mer information om hur routning fungerar i Azure dev Spaces finns i [så här fungerar routning med Azure dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Live testar en öppen pull-begäran

Du kan också använda GitHub åtgärder med Azure dev Spaces för att testa ändringar i programmet i en pull-begäran direkt i klustret innan sammanfogning. Azure dev Spaces kan automatiskt distribuera en gransknings version av programmet till klustret, så att både författaren och andra team medlemmar kan granska ändringarna i hela programmets sammanhang. Med hjälp av routningsfunktioner i Azure dev Spaces, distribueras den här gransknings versionen av programmet också till klustret utan att andra dev-rymder påverkas. Med alla dessa funktioner kan du på ett säkert sätt godkänna och slå samman pull-begäranden. Om du vill se ett exempel på GitHub-åtgärder och Azure dev Spaces, se [GitHub åtgärder & Azure Kubernetes service][pr-flow].

## <a name="next-steps"></a>Nästa steg

För att komma igång med att ansluta din lokala utvecklings dator till ditt AKS-kluster, se [ansluta din utvecklings dator till ett AKS-kluster][connect].

För att komma igång med Azure dev Spaces för grupp utveckling, se [team utvecklingen i snabb starten för Azure dev Spaces][quickstart-team] .

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development