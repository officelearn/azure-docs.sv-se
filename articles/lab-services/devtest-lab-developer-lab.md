---
title: Använd Azure DevTest Labs för utvecklare | Microsoft-dokument
description: Lär dig mer om Azure DevTest Labs-funktioner som kan användas för att uppfylla utvecklarkraven och de detaljerade steg som du kan följa för att konfigurera ett labb.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: ae44696f62a085bf1e798f0915e2bd1e27c3a78f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760155"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Använda Azure DevTest Labs för utvecklare
Azure DevTest Labs kan användas för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för utvecklingsdatorer för utvecklare. I det här scenariot har DevTest Labs följande fördelar:

- Utvecklare kan snabbt etablera sina utvecklingsmaskiner på begäran.
- Utvecklare kan enkelt anpassa sina utvecklingsdatorer när så behövs.
- Administratörer kan kontrollera kostnaderna genom att se till att:
  - Utvecklare kan inte få fler virtuella datorer än de behöver för utveckling.
  - Virtuella datorer stängs av när de inte används. 

![Använd DevTest Labs för utbildning](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

I den här artikeln får du lära dig mer om olika Azure DevTest Labs-funktioner som kan användas för att uppfylla utvecklarkraven och de detaljerade steg som du kan följa för att konfigurera ett labb.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementera utvecklarmiljöer med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs är utgångspunkten i Azure DevTest Labs. När du har skapat ett labb kan du utföra uppgifter som att lägga till användare (utvecklare) i labbet, ange principer för att styra kostnaderna, definiera VM-avbildningar som kan skapa snabbt och mer.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig hur du skapar ett labb i Azure DevTest Labs i Azure-portalen. |
2. **Skapa virtuella datorer på några minuter med färdiga marketplace-bilder och anpassade bilder** 
   
    Du kan välja färdiga avbildningar från en mängd olika avbildningar på Azure Marketplace och göra dem tillgängliga i labbet. Om de färdiga avbildningarna inte uppfyller dina krav kan du skapa en anpassad avbildning genom att skapa en virtuell labbdator med hjälp av en färdig avbildning från Azure Marketplace, installera all programvara som du behöver och spara den virtuella datorn som en anpassad avbildning i labbet.

    Om du använder anpassade bilder kan du överväga att använda en bildfabrik för att skapa och distribuera dina bilder. En avbildningsfabrik är en konfigurationslösning som regelbundet bygger och distribuerar dina konfigurerade avbildningar automatiskt. Detta sparar den tid som krävs för att manuellt konfigurera systemet efter att en virtuell dator har skapats med basoperativsystemet.
  
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan vitlista Azure Marketplace-avbildningar, vilket gör det möjligt att endast välja de avbildningar du vill ha för utvecklarna.|
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att förinstallera programvaran du behöver så att utvecklare snabbt kan skapa en virtuell dator med den anpassade avbildningen.|
   | [Läs mer om bildfabriken](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Titta på ett videoklipp som beskriver hur du konfigurerar och använder en bildfabrik.|

3. **Skapa återanvändbara mallar för utvecklardatorer** 
   
    En formel i Azure DevTest Labs är en lista över standardegenskapsvärden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en avbildning, en vm-storlek (en kombination av CPU och RAM) och ett virtuellt nätverk. Varje utvecklare kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs-formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att plocka upp en avbildning, VM-storlek (kombination av CPU och RAM) och ett virtuellt nätverk.|

4. **Skapa artefakter för att aktivera flexibel anpassning av virtuella datorer**

   Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

   - Verktyg som du vill installera på den virtuella datorn – till exempel agenter, Spelman och Visual Studio.
   - Åtgärder som du vill köra på den virtuella datorn – till exempel kloning av en repo.
   - Program som du vill testa.

   Många artefakter är redan tillgängliga direkt. Du kan skapa egna anpassade artefakter om du vill ha mer anpassning för dina specifika behov.

   Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa anpassade artefakter för din Vm-dator med DevTest Labs](devtest-lab-artifact-author.md) |Skapa dina egna anpassade artefakter för de virtuella datorerna i labbet.|
   | [Lägga till en Git-databas för att lagra anpassade artefakter och Azure Resource Manager-mallar för användning i Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Lär dig hur du lagrar dina egna artefakter i din egen privata Git-repo.|

5. **Kontrollera kostnader**
   
    Med Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en utvecklare i labbet. 
   
    Om utvecklarteamet har ett fastställt arbetsschema och du vill stoppa alla virtuella datorer vid en viss tidpunkt på dagen och sedan automatiskt starta om dem följande dag, kan du enkelt utföra det genom att ställa in principer för automatisk avstängning och automatisk start i labbet. 
   
    Slutligen, när apputveckling är klar, kan du ta bort alla virtuella datorer på en gång genom att köra ett enda PowerShell-skript. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnaderna genom att ange principer i labbet. |
   | [Ta bort alla virtuella labbdamer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labb i en åtgärd när utvecklingen är klar.|

1. **Lägga till ett virtuellt nätverk i en virtuell dator** 
   
    DevTest Labs skapar ett nytt virtuellt nätverk (VNET) när ett labb skapas. Om du har konfigurerat ditt eget virtuella nätverk – till exempel genom att använda ExpressRoute eller plats-till-plats-VPN – kan du lägga till det här virtuella nätverket i labbets inställningar för virtuella nätverk så att det är tillgängligt när du skapar virtuella datorer.

    Dessutom finns det en Azure Active Directory-domän kopplingsartefakt tillgänglig som ansluter en virtuell dator till en domän när den virtuella datorn skapas. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md) |Lär dig hur du konfigurerar ett virtuellt nätverk i Azure DevTest Labs med Azure-portalen.|

6. **Dela labbet med varje utvecklare**
   
    Labb kan nås direkt via en länk som du delar med dina utvecklare. De behöver inte ens ha ett Azure-konto, så länge de har ett [Microsoft-konto.](devtest-lab-faq.md#what-is-a-microsoft-account) Utvecklare kan inte se virtuella datorer som skapats av andra utvecklare.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägga till en utvecklare i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure-portalen för att lägga till utvecklare i labbet.|
   | [Lägga till utvecklare i labbet med ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera lägger till utvecklare i labbet. |
   | [Få en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Läs om hur utvecklare direkt kan komma åt ett labb via en hyperlänk.|

7. **Automatisera labbskapande för fler team** 
   
    Du kan automatisera skapande av labb, inklusive anpassade inställningar, genom att skapa en Resource Manager-mall och använda den för att skapa identiska labb om och om igen. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med hjälp av en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Hjälp av Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

