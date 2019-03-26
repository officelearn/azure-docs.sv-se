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
ms.openlocfilehash: 5c1465f31c8b5eb15b6fe63ed61a946e3b32d550
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440088"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Skapa en anpassad avbildning fabrik i Azure DevTest Labs
Den här artikeln beskriver ställa in en bevarandeprincip rensar fabriken och ta bort gamla avbildningar från alla andra labb i organisationen. 

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har följt de här artiklarna innan du fortsätter:

- [Skapa en avbildning datafabrik](image-factory-create.md)
- [Kör en datafabrik avbildning från Azure DevOps](image-factory-set-up-devops-lab.md)
- [Spara anpassade avbildningar och distribuera till flera labs](image-factory-save-distribute-custom-images.md)

Följande objekt ska redan finnas:

- Ett labb för bild-fabrik i Azure DevTest Labs
- En eller flera mål där fabriken ska distribuera gyllene avbildningar för Azure DevTest Labs
- Azure DevOps-projekt som används för att automatisera bild fabriken.
- Kod källplats som innehåller skript och konfiguration (i vårt exempel, i samma DevOps-projekt som använde ovan)
- En byggesdefinition för att dirigera Azure Powershell-åtgärder
 
## <a name="setting-the-retention-policy"></a>Ange bevarandeprincipen
Innan du konfigurerar Rensa steg bör du definiera hur många historiska avbildningar som du vill ha kvar i DevTest Labs. När du har följt den [kör factory en avbildning från Azure DevOps](image-factory-set-up-devops-lab.md) artikeln har du konfigurerat olika Skapa variabler. En av dem har **ImageRetention**. Du anger den här variabeln till `1`, vilket innebär att DevTest Labs inte bibehåller en historik över anpassade avbildningar. Endast de senaste distribuerade avbildningarna blir tillgänglig. Om du ändrar den här variabeln till `2`, senast distribuerade avbildningen samt de tidigare inställningarna kommer att finnas kvar. Du kan ange ett värde att definiera antalet historiska avbildningar som du vill behålla i DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Rensa fabriken
Det första steget i att rensa fabriken är att ta bort den gyllene avbildningar av virtuella datorer från avbildningen fabriken. Det finns ett skript för att göra detta precis som våra föregående skript. Det första steget är att lägga till en annan **Azure Powershell** uppgift till build-definition som du ser i följande bild:

![Steg för PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

När du har den nya uppgiften i listan, Välj posten och fylla i all information som visas i följande bild:

![Rensa gamla avbildningar PowerShell-aktivitet](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Skriptparametrar är: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Dra tillbaka gamla avbildningar 
Den här uppgiften tar bort eventuella gamla avbildningar, som endast historik matchande den **ImageRetention** skapa variabeln. Lägg till ytterligare **Azure Powershell** Skapa uppgift till vår build-definition. När den har lagts till, väljer du uppgiften och fyll sedan i informationen som visas i följande bild: 

![Dra tillbaka gamla avbildningar PowerShell-aktivitet](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Skriptparametrar är: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Kön versionen
Nu när du har slutfört byggesdefinition kan köa en ny version att se till att allt fungerar. När bygget har slutförts har den nya anpassade avbildningar visas i laboratoriet mål och om du kontrollera att avbildningen factory labbet, visas inga etablerade virtuella datorer. Om du köa ytterligare versioner visas dessutom rensningsuppgifter tas ur bruk ut gamla anpassade avbildningar från DevTest Labs enligt till kvarhållningsvärdet i build-variabler.

> [!NOTE]
> Om du har genomfört build pipelinen i slutet av den senaste artikeln i serien, manuellt ta bort de virtuella datorerna som skapades i avbildningen factory labbet innan en ny version.  Manuell rensning steget behövs bara medan vi konfigurerar allt och kontrollera att det fungerar.



## <a name="summary"></a>Sammanfattning
Nu har du en aktiv avbildning fabrik som kan skapa och distribuera anpassade avbildningar till dina labb på begäran. I den här punkten, det är bara för att få dina avbildningar korrekt konfigurerad och identifiera mål labs. Som vi nämnde i föregående artikel den **Labs.json** finns i din **Configuration** mapp anger vilka bilder ska vara tillgängliga i varje mål labs. När du lägger till andra DevTest Labs för din organisation, behöver du bara lägga till en post i Labs.json för det nya labbet.

Det är också enkelt att lägga till en ny avbildning till din datafabrik. När du vill inkludera en ny avbildning i din datafabrik som du öppnar den [Azure-portalen](https://portal.azure.com), navigera till din datafabrik DevTest Labs, Välj knappen för att lägga till en virtuell dator och väljer önskad marketplace-avbildning och artefakter. Istället för att välja den **skapa** knappen Välj om du vill se den nya Virtuellt **visa Azure Resource Manager-mall**”och spara mallen som en JSON-fil någonstans inom den **GoldenImages** mapp i din lagringsplats. Nästa gång du kör din avbildning factory skapas en anpassad avbildning.


## <a name="next-steps"></a>Nästa steg
1. [Schemalägg din version och utgåva](/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) att köra den avbildning fabriken med jämna mellanrum. Den uppdaterar din factory bilder med jämna mellanrum.
2. Se fler gyllene bilder för din datafabrik. Du kan även försöka [skapa artefakter](devtest-lab-artifact-author.md) skript ytterligare delar av ditt VM-installationsåtgärder och ange artefakter i bilderna factory.
4. Skapa en [avgränsa version och utgåva](/devops/pipelines/overview.md?view=azure-devops-2019) att köra den **DistributeImages** skript separat. Du kan köra det här skriptet när du gör ändringar i Labs.json och hämta bilder som kopieras till målet labs utan att behöva återskapa alla bilder igen.

