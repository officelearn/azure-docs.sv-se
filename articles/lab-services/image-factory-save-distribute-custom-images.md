---
title: Spara och distribuera avbildningar i Azure DevTest Labs | Microsoft-dokument
description: Den här artikeln ger dig stegen för att spara anpassade avbildningar från de redan skapade virtuella datorerna (VMs) i Azure DevTest Labs.
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
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759439"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Spara anpassade avbildningar och distribuera till flera labb
Den här artikeln ger dig stegen för att spara anpassade avbildningar från de redan skapade virtuella datorerna (VMs). Den täcker också hur du distribuerar dessa anpassade avbildningar till andra DevTest Labs i organisationen.

## <a name="prerequisites"></a>Krav
Följande punkter bör redan finnas på plats:

- Ett labb för avbildningsfabriken i Azure DevTest Labs.
- Ett Azure DevOps-projekt som används för att automatisera avbildningsfabriken.
- Källkodsplatsen som innehåller skript och konfiguration (i vårt exempel i samma DevOps-projekt som nämns i föregående steg).
- Skapa definition för att dirigera Azure Powershell-uppgifter.

Om det behövs följer du stegen i [kör en avbildningsfabrik från Azure DevOps](image-factory-set-up-devops-lab.md) för att skapa eller konfigurera dessa objekt. 

## <a name="save-vms-as-generalized-vhds"></a>Spara virtuella datorer som generaliserade virtuella hårddiskar
Spara befintliga virtuella datorer som generaliserade virtuella hårddiskar.  Det finns ett exempel på PowerShell-skript för att spara befintliga virtuella datorer som generaliserade virtuella hårddiskar. Om du vill använda den lägger du först till ytterligare en **Azure Powershell-uppgift** i byggdefinitionen enligt följande avbildning:

![Lägg till Azure PowerShell-steg](./media/save-distribute-custom-images/powershell-step.png)

När du har den nya uppgiften i listan väljer du objektet så att vi kan fylla i alla detaljer som visas i följande bild: 

![PowerShell-inställningar](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generaliserade kontra specialiserade anpassade bilder
I [Azure-portalen](https://portal.azure.com)kan du när du skapar en anpassad avbildning från en virtuell dator välja att ha en generaliserad eller en specialiserad anpassad avbildning.

- **Specialiserad anpassad bild:** Sysprep/Deetavision kördes INTE på maskinen. Det innebär att bilden är en exakt kopia av OS-disken på den befintliga virtuella datorn (en ögonblicksbild).  Samma filer, program, användarkonton, datornamn och så vidare, är alla närvarande när vi skapar en ny maskin från den här anpassade avbildningen.
- **Generaliserad anpassad bild:** Sysprep/Deetavision kördes på maskinen.  När den här processen körs tas användarkonton bort, datornamnet tas bort, användarregisterfilerna, etc., med målet att generalisera avbildningen så att den kan anpassas när du skapar en annan virtuell dator.  När du generaliserar en virtuell dator (genom att köra sysprep) förstör processen den aktuella virtuella datorn – den kommer inte längre att fungera.

Skriptet för att fästa anpassade avbildningar i Image Factory sparar virtuella hårddiskar för alla virtuella datorer som skapats i föregående steg (identifieras baserat på en tagg på resursen i Azure).

## <a name="update-configuration-for-distributing-images"></a>Uppdatera konfiguration för distribution av avbildningar
Nästa steg i processen är att driva anpassade bilder från avbildningen fabriken labbet ut till alla andra laboratorier som behöver dem. Den centrala delen av den här processen är **konfigurationsfilen labs.json.** Du hittar den här filen i **konfigurationsmappen** som ingår i avbildningsfabriken.

Det finns två viktiga saker som anges i konfigurationsfilen labs.json:

- Unikt identifiera ett specifikt mållabb med hjälp av prenumerations-ID och labbnamnet.
- Den specifika uppsättningen av bilder som ska skickas till labbet som relativa sökvägar till konfigurationsroten. Du kan också ange hela mappen (för att hämta alla bilder i den mappen).

Här är ett exempel labs.json fil med två laboratorier listade. I det här fallet distribuerar du bilder till två olika labb.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Skapa en versionsuppgift
Med samma steg som du har sett tidigare i den här artikeln lägger du till ytterligare en **Azure Powershell-versionsuppgift** för dig att skapa definition. Fyll i informationen som visas i följande bild: 

![Skapa uppgift för att distribuera bilder](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametrarna är:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Den här uppgiften tar alla anpassade bilder som finns i avbildningsfabriken och skickar ut dem till alla labb som definierats i filen Labs.json.

## <a name="queue-the-build"></a>Köa bygget
När distributionsversionsuppgiften är klar köar du en ny version för att se till att allt fungerar. När versionen har slutförts visas de nya anpassade avbildningarna i mållabbet som angavs i konfigurationsfilen Labs.json.

## <a name="next-steps"></a>Nästa steg
I nästa artikel i serien uppdaterar du avbildningsfabriken med en bevarandeprincip och rensningssteg: [Ange bevarandeprincip och kör rensningsskript](image-factory-set-retention-policy-cleanup.md).
