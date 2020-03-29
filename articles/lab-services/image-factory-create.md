---
title: Skapa en avbildningsfabrik i Azure DevTest Labs | Microsoft-dokument
description: I den här artikeln visas hur du konfigurerar en anpassad avbildningsfabrik med hjälp av exempelskript som är tillgängliga i Git-databasen (Azure DevTest Labs).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759456"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Skapa en anpassad avbildningsfabrik i Azure DevTest Labs
Den här artikeln visar hur du konfigurerar en anpassad avbildningsfabrik med hjälp av exempelskript som är tillgängliga i [Git-databasen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Vad är en bildfabrik?
En avbildningsfabrik är en konfigurationslösning som bygger och distribuerar avbildningar automatiskt regelbundet med alla önskade konfigurationer. Bilderna i bildfabriken är alltid uppdaterade, och det pågående underhållet är nästan noll när hela processen är automatiserad. Och eftersom alla nödvändiga konfigurationer redan finns i avbildningen, sparar det tid från att manuellt konfigurera systemet efter att en virtuell dator har skapats med basoperativsystemet.

Den viktiga acceleratorn för att få ett utvecklarskrivbord till ett färdigt tillstånd i DevTest Labs använder anpassade avbildningar. Nackdelen med anpassade bilder är att det finns något extra att underhålla i labbet. Testversioner av produkter upphör till exempel att gälla med tiden (eller) nyligen släppta säkerhetsuppdateringar tillämpas inte, vilket tvingar oss att uppdatera den anpassade avbildningen med jämna mellanrum. Med en bildfabrik har du en definition av bilden incheckad till källkodskontroll och har en automatiserad process för att producera anpassade bilder baserat på definitionen.

Lösningen möjliggör hastigheten för att skapa virtuella datorer från anpassade avbildningar samtidigt som ytterligare löpande underhållskostnader elimineras. Med den här lösningen kan du automatiskt skapa anpassade bilder, distribuera dem till andra DevTest Labs och dra tillbaka de gamla bilderna. I följande video får du lära dig mer om bildfabriken och hur den implementeras med DevTest Labs.  Alla Azure Powershell-skript är fritt tillgängliga [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)och finns här: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Hög nivå vy av lösningen
Lösningen möjliggör hastigheten för att skapa virtuella datorer från anpassade avbildningar samtidigt som ytterligare löpande underhållskostnader elimineras. Med den här lösningen kan du automatiskt skapa anpassade avbildningar och distribuera dem till andra DevTest Labs. Du använder Azure DevOps (tidigare Visual Studio Team Services) som orkestreringsmotor för att automatisera alla åtgärder i DevTest Labs.

![Hög nivå vy av lösningen](./media/create-image-factory/high-level-view-of-solution.png)

Det finns ett [VSTS-tillägg för DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) som gör att du kan utföra dessa enskilda steg:

- Skapa anpassad bild
- Skapa en virtuell dator
- Ta bort virtuell dator
- Skapa miljö
- Ta bort miljö
- Fyll i miljö

Att använda DevTest Labs-tillägget är ett enkelt sätt att komma igång med att automatiskt skapa anpassade avbildningar i DevTest Labs.

Det finns en alternativ implementering med PowerShell-skript för ett mer komplext scenario. Med PowerShell kan du helt automatisera en avbildningsfabrik baserat på DevTest Labs som kan användas i din ci/cd-verktygskedja (Continuous Integration and Continuous Delivery). De principer som följer i denna alternativa lösning är:

- Vanliga uppdateringar bör inte kräva några ändringar i avbildningsfabriken. (till exempel lägga till en ny typ av anpassad bild, automatiskt pensionera gamla bilder, lägga till en ny "slutpunkt" DevTest Labs för att ta emot anpassade bilder, och så vidare.)
- Vanliga ändringar backas upp av källkodskontroll (infrastruktur som kod)
- DevTest Labs som tar emot anpassade avbildningar kanske inte finns i samma Azure-prenumeration (labs span prenumerationer)
- PowerShell-skript måste kunna återanvändas så att vi kan snurra upp ytterligare fabriker efter behov

## <a name="next-steps"></a>Nästa steg
Gå vidare till nästa artikel i det här avsnittet: [Kör en avbildningsfabrik från Azure DevOps](image-factory-set-up-devops-lab.md)
