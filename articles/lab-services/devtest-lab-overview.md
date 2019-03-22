---
title: Om Azure DevTest Labs | Microsoft Docs
description: Lär dig hur DevTest Labs kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339337"
---
# <a name="about-azure-devtest-labs"></a>Om Azure DevTest Labs
Azure DevTest Labs är en tjänst som gör att utvecklare på ett team för att effektivt självbetjäning virtuella datorer och/eller PaaS-resurser som de behöver för att utveckla, testa, utbildning och demonstrationer o.s.v. utan att behöva vänta konstant godkännanden för de verktyg de behöver. 

Labbet består redan av förkonfigurerade baser eller Resource Manager-mallar med alla nödvändiga verktyg och program som utvecklare kan använda för att skapa miljöer. Utvecklare kan skapa sina miljöer på några minuter istället för timmar eller dagar. 

Med DevTest Labs kan testa du den senaste versionen av ditt program genom att göra följande uppgifter:

- Snabbt etablera Windows och Linux-miljöer med återanvändbara mallar och artefakter
- Integrera enkelt din distributionspipeline med DevTest Labs och etablera miljöer på begäran
- Skala upp dina belastningstester genom att etablera flera testagenter och skapa företablerade miljöer för utbildning och demonstrationer.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Funktioner
DevTest Labs innehåller följande funktioner för utvecklare som arbetar med virtuella datorer:

- Skapa snabbt en virtuell dator genom att följa mindre än fem enkla steg.
- Välj bland en granskad lista över VM-databaser som är konfigurerad, godkända och auktoriserats av gruppledare eller central IT.
- Skapa virtuella datorer från färdiga anpassade avbildningar som har den programvara och verktyg som installerats i avbildningen. 
- Skapa virtuella datorer från formler som är i stort sett anpassade avbildningar och ett senaste versionen av den programvara som installeras vid tidpunkten för skapandet av den virtuella datorn.
- Installera artefakter som är tillägg distribuerade på den virtuella datorn när den har etablerats.
- Ställ in automatisk avstängning och automatisk start scheman på datorer som har avstängning i slutet av dagen och sedan upp och kör nästa morgon.
- Anspråk helt enkelt en virtuell dator som skapats i förväg utan att behöva gå igenom processen för att skapa en dator. 

DevTest Labs erbjuder följande funktioner för utvecklare som arbetar med PaaS-miljöer:

- Skapa PaaS Azure Resource Manager-miljöer snabbt efter mindre än tre enkla steg.
- Välj bland en granskad lista över Resource Manager-mallar, som är konfigurerad, godkända och auktoriserats av gruppledare eller central IT.
- Skapa en tom resursgrupp (Sandbox) med en Resource Manager-mall för att utforska hela Azure utan att förlora fortfarande inom ramen för labbet.
- DevTest Labs ger följande fördelar i Skapa, konfigurera och hantera developer- och testmiljöer i molnet

Förutom en självbetjäning modell för utvecklare på ett team med tjänsten kan centrala IT-avdelningen att styra avfall och optimera kostnader på resurser stannar inom budgetar genom att göra följande uppgifter: 

- Inställningen för automatisk avstängning och automatiskt starta scheman på virtuella datorer.
- Ange principer för hur många virtuella datorer-användare kan skapa.
- Ange principer på de storlekar på virtuella datorer och galleriet användare kan välja från.
- Spåra kostnader och ange mål för labbet.
- Få ett meddelande på hög beräknade kostnader för övningen så att du kan vidta nödvändiga åtgärder. 

DevTest Labs ger följande fördelar skapa, konfigurera och hantera miljöer i molnet:

## <a name="cost-control-and-governance"></a>Kostnad kontroll och styrning
DevTest Labs gör det lättare att kontrollera kostnaderna genom att du kan utföra följande uppgifter:

- Ange principer för labbet – t.ex antalet virtuella datorer (VM) per användare och antalet virtuella datorer per labb. 
- Skapa principer för att automatiskt stänga av och starta virtuella datorer.
- Kan du spåra kostnader på virtuella datorer och PaaS-resurser som kunde i labbet så att du kan hålla fördefinierade budgeten. 
- Hjälper utvecklare att hålla sig inom kontexten för labbet så att de inte slutar att skapa resurser utanför den antingen i underliggande resursgrupp eller prenumeration.

## <a name="quickly-get-to-ready-to-test"></a>Få snabb tillgång till redo att testa
DevTest Labs kan du skapa företablerade miljöer med allt ditt team behöver för att börja utveckla och testa program. Anspråk helt enkelt miljöer där det senaste framgångsrika bygget i programmet är installerat och börjar arbeta direkt. Eller Använd behållare för ännu snabbare och smidigare miljöframtagning.

## <a name="create-once-use-everywhere"></a>Skapa en gång, använd överallt
Samla in och dela PaaS miljömallar och artefakter inom gruppen eller organisationen – allt i källkontroll – för att skapa developer- och testmiljöer enkelt.

## <a name="worry-free-self-service"></a>Problemfri självbetjäning
DevTest Labs kan dina utvecklare och testare snabbt och skapar enkelt virtuella datorer (IaaS) och PaaS-resurser inom några få klick med hjälp av en uppsättning resurser som redan har konfigurerats för dessa.

## <a name="use-iaas-and-paas-resources"></a>Använda IaaS och PaaS-resurser 
DevTest Labs också ger utvecklare möjlighet att få fram PaaS-resurser som Web Apps, Service Fabric-kluster, SharePoint-grupper, och så vidare. med en Resource Manager-mall i labbet. Använda Resource Manager-mallar från våra offentliga miljö-databasen eller Anslut labbet till din egen Git-lagringsplats för att komma igång med PaaS i labs. Du kan också spåra kostnader på dessa resurser för att hålla sig inom din fördefinierade budgetar. 

## <a name="integrate-with-your-existing-toolchain"></a>Integreras med din befintliga verktygskedja
Använda fördefinierade plugin-program eller vårt API för att etablera miljöer för utveckling/testning direkt från ditt önskade kontinuerlig integrering (CI)-verktyg, integrated development environment (IDE) eller automatiserade releasepipeline. Du kan också använda vårt omfattande kommandoradsverktyg.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- Läs mer om DevTest Labs i [DevTest Labs-koncept](devtest-lab-concepts.md)
- En genomgång med stegvisa instruktioner finns i [självstudien: Konfigurera ett labb med hjälp av Azure DevTest Labs](tutorial-create-custom-lab.md)


