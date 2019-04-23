---
title: Skapa en avbildning datafabrik i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skapar en anpassad avbildning fabrik i Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: e51c56f54a4e30b5e9094388ed92aa0a62ba0840
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796318"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Skapa en anpassad avbildning fabrik i Azure DevTest Labs
Den här artikeln visar hur du ställer in en anpassad avbildning fabrik med hjälp av exempelskript som finns tillgängliga i den [Git-lagringsplats](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Vad är en avbildning factory?
En bild factory är en konfiguration som kod-lösning som bygger och distribuerar avbildningar automatiskt med jämna mellanrum med alla önskade konfigurationer. Avbildningarna i avbildningen factory är alltid uppdaterade och löpande underhåll är nästan noll när hela processen sker automatiskt. Och eftersom konfigurationerna som krävs redan finns i avbildningen sparas tiden från att manuellt konfigurera systemet när du har skapat en virtuell dator med grundläggande operativsystem.

Betydande snabbtangenten för att få en utvecklare desktop i tillståndet klar i DevTest Labs använder anpassade avbildningar. Nackdelen med anpassade avbildningar är att det inte finns något extra att underhålla i laboratoriet. Till exempel dessa versioner av produkter upphör att gälla över tid (eller) nyligen utgivna säkerhetsuppdateringar tillämpas inte, vilket tvingar oss att uppdatera den anpassade avbildningen med jämna mellanrum. Med en bild datafabrik har du en definition av avbildningen som checkats in till källkodskontroll och har en automatiserad process att skapa anpassade avbildningar som baseras på definitionen.

Den här lösningen kan hastigheten på virtuella datorer skapas från anpassade avbildningar samtidigt undvika kostnader för ytterligare löpande underhåll. Med den här lösningen kan du automatiskt skapa anpassade avbildningar, distribuera dem till andra labb och dra tillbaka gamla avbildningar. I följande video du lär dig mer om avbildningen factory och hur den har implementerats med DevTest Labs.  Azure Powershell-skript är tillgängliga och fritt finns här: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Översikt över lösningen
Den här lösningen kan hastigheten på virtuella datorer skapas från anpassade avbildningar samtidigt undvika kostnader för ytterligare löpande underhåll. Med den här lösningen kan du automatiskt skapa anpassade avbildningar och distribuera dem till andra DevTest Labs. Du använder Azure DevOps (tidigare Visual Studio Team Services) som dirigeringsmotor för att automatisera de alla åtgärderna i DevTest Labs.

![Översikt över lösningen](./media/create-image-factory/high-level-view-of-solution.png)

Det finns en [VSTS-tillägg för DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) som gör att du kan köra de här stegen: 

- Skapa anpassad bild
- Skapa en virtuell dator
- Ta bort virtuell dator
- Skapa miljö
- Ta bort miljön
- Fyll i miljön

Med DevTest Labs-tillägget är ett enkelt sätt att komma igång med att automatiskt skapa anpassade avbildningar i DevTest Labs.

Det finns ett alternativ implementering av med hjälp av PowerShell-skript för ett mer komplext scenario. Med hjälp av PowerShell, kan du automatisera en bild datafabrik baserat på labb som kan användas i dina kontinuerlig integrering och kontinuerlig leverans (CI/CD) verktygskedja. Principerna följt i den här alternativa lösningen är:

- Vanliga uppdateringar kräver inga ändringar till avbildningen factory. (till exempel att lägga till en ny typ av anpassad avbildning, ta automatiskt bort gamla avbildningar, lägger du till en ny ”slutpunkt' DevTest Labs för att ta emot anpassade avbildningar och så vidare.)
- Vanliga ändringar backas upp av källkodskontroll (infrastruktur som kod)
- Ta emot anpassade avbildningar med labb kanske inte i samma Azure-prenumeration (labs sträcker sig över prenumerationer)
- PowerShell-skript måste vara återanvändbara så vi kan skapa ytterligare fabriker efter behov

## <a name="next-steps"></a>Nästa steg
Gå vidare till nästa artikel i det här avsnittet: [Kör en datafabrik avbildning från Azure DevOps](image-factory-set-up-devops-lab.md)
