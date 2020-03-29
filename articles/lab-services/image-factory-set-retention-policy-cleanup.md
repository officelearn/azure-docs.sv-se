---
title: Konfigurera bevarandeprincip i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du konfigurerar en bevarandeprincip, rensar fabriken och drar tillbaka gamla bilder från DevTest Labs.
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
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759422"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Konfigurera bevarandeprincip i Azure DevTest Labs
Den här artikeln beskriver hur du anger en bevarandeprincip, rensar fabriken och drar tillbaka gamla bilder från alla andra DevTest Labs i organisationen. 

## <a name="prerequisites"></a>Krav
Se till att du har följt dessa artiklar innan du fortsätter vidare:

- [Skapa en avbildningsfabrik](image-factory-create.md)
- [Köra en avbildningsfabrik från Azure DevOps](image-factory-set-up-devops-lab.md)
- [Spara anpassade avbildningar och distribuera till flera labb](image-factory-save-distribute-custom-images.md)

Följande punkter bör redan finnas på plats:

- Ett labb för avbildningsfabriken i Azure DevTest Labs
- Ett eller flera mål Azure DevTest Labs där fabriken kommer att distribuera gyllene avbildningar
- Ett Azure DevOps-projekt som används för att automatisera avbildningsfabriken.
- Källkodsplats som innehåller skript och konfiguration (i vårt exempel i samma DevOps-projekt som används ovan)
- En build-definition för att dirigera Azure Powershell-uppgifter
 
## <a name="setting-the-retention-policy"></a>Ställa in bevarandeprincipen
Innan du konfigurerar rensningsstegen definierar du hur många historiska bilder du vill behålla i DevTest Labs. När du följde artikeln [Kör en avbildningsfabrik från Azure DevOps](image-factory-set-up-devops-lab.md) har du konfigurerat olika byggvariabler. En av dem var **ImageRetention**. Du ställer in `1`den här variabeln på , vilket innebär att DevTest Labs inte kommer att behålla en historik över anpassade avbildningar. Endast de senaste distribuerade bilderna kommer att vara tillgängliga. Om du ändrar `2`den här variabeln till kommer den senaste distribuerade bilden plus de tidigare att behållas. Du kan ange det här värdet för att definiera antalet historiska bilder som du vill behålla i dina DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Städa upp i fabriken
Det första steget i rengöringen av fabriken är att ta bort de gyllene virtuella bild-datorerna från bildfabriken. Det finns ett skript för att göra denna uppgift precis som våra tidigare skript. Det första steget är att lägga till ytterligare en **Azure Powershell-uppgift** i byggdefinitionen enligt följande avbildning:

![PowerShell-steg](./media/set-retention-policy-cleanup/powershell-step.png)

När du har den nya uppgiften i listan markerar du objektet och fyller i alla detaljer som visas i följande bild:

![Rensa gamla bilder PowerShell-uppgift](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Skriptparametrarna `-DevTestLabName $(devTestLabName)`är: .

## <a name="retire-old-images"></a>Dra tillbaka gamla bilder 
Den här uppgiften tar bort alla gamla bilder och behåller bara en historik som matchar **ImageRetention-byggvariabeln.** Lägg till ytterligare en **Azure Powershell-bygguppgift** i vår build-definition. När den har lagts till markerar du uppgiften och fyller i informationen enligt följande bild: 

![Dra tillbaka gamla bilder PowerShell-uppgift](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Skriptparametrarna är:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Köa bygget
Nu när du har slutfört byggdefinitionen köar du en ny version för att se till att allt fungerar. När bygget har slutförts visas de nya anpassade avbildningarna i mållabbet och om du kontrollerar bildfabrikslabbet ser du inga etablerade virtuella datorer. Om du dessutom köar ytterligare byggen visas rensningsuppgifterna som drar tillbaka gamla anpassade avbildningar från DevTest Labs i enlighet med bevarandevärdet som angetts i byggvariablerna.

> [!NOTE]
> Om du har kört byggpipelinen i slutet av den sista artikeln i serien tar du manuellt bort de virtuella datorer som skapades i avbildningsfabrikslabbet innan du köar en ny version.  Det manuella rensningssteget behövs bara när vi ställer in allt och verifierar att det fungerar.



## <a name="summary"></a>Sammanfattning
Nu har du en pågående avbildningsfabrik som kan generera och distribuera anpassade avbildningar till dina labb på begäran. Vid denna punkt, det är bara en fråga om att få dina bilder inrättas ordentligt och identifiera målet labs. Som nämns i föregående artikel anger **filen Labs.json** i **konfigurationsmappen** vilka avbildningar som ska göras tillgängliga i vart och ett av mållabben. När du lägger till andra DevTest Labs i din organisation behöver du bara lägga till en post i Labs.json för det nya labbet.

Att lägga till en ny bild i din fabrik är också enkelt. När du vill inkludera en ny avbildning i fabriken öppnar du [Azure-portalen,](https://portal.azure.com)navigerar till din fabriksdestatortestlabb, väljer knappen för att lägga till en virtuell dator och väljer önskad marketplace-avbildning och artefakter. I stället för att välja knappen **Skapa** för att skapa den nya virtuella datorn väljer du **Visa Azure Resource Manager-mall**" och sparar mallen som en .json-fil någonstans i **GoldenImages-mappen** i databasen. Nästa gång du kör din bildfabrik, kommer det att skapa din anpassade bild.


## <a name="next-steps"></a>Nästa steg
1. [Schemalägg din version/utgåva](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) så att den kör avbildningsfabriken med jämna mellanrum. Det uppdaterar dina fabriksgenererade bilder regelbundet.
2. Gör fler gyllene bilder för din fabrik. Du kan också överväga att [skapa artefakter](devtest-lab-artifact-author.md) för att skript ytterligare delar av din VM-inställningsuppgifter och inkludera artefakter i fabriksavbildningarna.
4. Skapa en [separat version/utgåva](/azure/devops/pipelines/overview?view=azure-devops-2019) för att köra **Skriptet DistributeImages** separat. Du kan köra det här skriptet när du gör ändringar i Labs.json och få bilder kopierade till mållabb utan att behöva återskapa alla bilder igen.

