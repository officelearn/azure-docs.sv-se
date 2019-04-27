---
title: Spara och distribuera avbildningar i Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622681"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Spara anpassade avbildningar och distribuera till flera labb
Den här artikeln täcker ger dig stegen för att spara anpassade avbildningar från de redan skapade virtuella datorerna (VM). Den behandlar också hur du distribuerar dessa anpassade avbildningar till andra DevTest Labs i organisationen.

## <a name="prerequisites"></a>Nödvändiga komponenter
Följande objekt ska redan finnas:

- Ett labb för bild-fabrik i Azure DevTest Labs.
- Ett Azure DevOps-projekt som används för att automatisera bild fabriken.
- Källplats kod som innehåller skript och konfiguration (i vårt exempel, i samma DevOps-projekt som nämns i föregående steg).
- Build-definition för att dirigera Azure Powershell-åtgärder.

Om det behövs, Följ stegen i den [kör factory en avbildning från Azure DevOps](image-factory-set-up-devops-lab.md) att skapa eller konfigurera de här objekten. 

## <a name="save-vms-as-generalized-vhds"></a>Spara virtuella datorer som generaliserad virtuella hårddiskar
Spara de befintliga virtuella datorerna som generaliserad virtuella hårddiskar.  Det finns ett PowerShell-exempelskript att spara de befintliga virtuella datorerna som generaliserad virtuella hårddiskar. För att använda den, Lägg först till en annan **Azure Powershell** uppgift till build-definition som du ser i följande bild:

![Lägg till steg i Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

När du har den nya uppgiften i listan, väljer du det så att vi kan fylla i all information som visas i följande bild: 

![PowerShell-inställningar](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generaliserade och specialiserade anpassade avbildningar
I den [Azure-portalen](https://portal.azure.com), när du skapar en anpassad avbildning från en virtuell dator måste du ha en generaliserad eller en särskild anpassad avbildning.

- **Anpassad specialiserad avbildning:** Sysprep/avetablering kördes inte på datorn. Det innebär att avbildningen är en exakt kopia av OS-disken på den befintliga virtuella datorn (en ögonblicksbild).  Samma filer, program, användarkonton, datornamn och så vidare, finns allt när vi skapar en ny dator från den här anpassade avbildningen.
- **Anpassad generaliserad avbildning:** Sysprep/avetablering kördes på datorn.  När den här processen körs tar bort användarkonton den, tar bort datornamnet, tar bort användaren registreringsdatafilerna, osv., med målet att generalisera avbildningen så att den kan anpassas när du skapar en annan virtuell dator.  När du generalisera en virtuell dator (genom att köra sysprep) processen förstör den aktuella virtuella datorn – det är inte längre fungerar.

Skriptet för att fästa anpassade avbildningar i avbildningen Factory sparar virtuella hårddiskar för virtuella datorer som skapats i föregående steg (identifieras baserat på en tagg för resursen i Azure).

## <a name="update-configuration-for-distributing-images"></a>Uppdatera konfigurationen för att distribuera avbildningar
Nästa steg i processen är att skicka anpassade avbildningar från image factory lab ut till andra övningar som behövs. Core-delen av den här processen är den **labs.json** konfigurationsfilen. Du hittar den här filen i den **Configuration** mapp som ingår i avbildningen fabriken.

Det finns två viktiga saker som anges i konfigurationsfilen labs.json:

- Unikt identifierar ett visst mål-labb med hjälp av prenumerations-ID och namnet på lab.
- Specifik uppsättning avbildningar som ska skickas till labbet som relativa sökvägar till rot-konfiguration. Du kan ange hela mappen (om du vill hämta alla avbildningar i den mappen) för.

Här är en exempelfil labs.json med två labs visas. I det här fallet ska du distribuera avbildningar till två olika övningar.

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
Med hjälp av samma steg som du har sett tidigare i den här artikeln, lägga till ytterligare **Azure Powershell** build-aktiviteten för att du build-definition. Fyll i informationen som visas i följande bild: 

![Skapa uppgift för att distribuera avbildningar](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametrarna är: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Den här aktiviteten tar alla anpassade avbildningar som finns i bild fabriken och skickar dem till alla övningar som definieras i filen Labs.json.

## <a name="queue-the-build"></a>Kön versionen
När denna build task distributionen är klar kan köa en ny version att se till att allt fungerar. När bygget har slutförts, visas de nya anpassade avbildningarna i laboratoriet mål som har angetts i konfigurationsfilen Labs.json.

## <a name="next-steps"></a>Nästa steg
I nästa artikel i serien uppdatera du avbildning med en kvarhållning princip och rensa steg: [Ange bevarandeprincip och köra rensningsskript](image-factory-set-retention-policy-cleanup.md).
