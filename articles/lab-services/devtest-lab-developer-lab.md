---
title: Använd Azure DevTest Labs för utvecklare | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för utvecklare scenarier.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 96432abe619ea23c1a06735567d00660e5430550
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787565"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Använd Azure DevTest Labs för utvecklare
Azure DevTest Labs kan användas för att implementera många viktiga scenarier, men ett av scenarierna för primära är med hjälp av DevTest Labs till utveckling värddatorerna för utvecklare. I det här scenariot har DevTest Labs följande fördelar:

- Utvecklare kan snabbt etablera deras utveckling datorer på begäran.
- Utvecklare kan enkelt anpassa sina development datorer när så behövs.
- Administratörer kan styra kostnader genom att säkerställa att:
  - Utvecklare kan få fler virtuella datorer än de behöver för utveckling.
  - Virtuella datorer är avstängd när inte i användning. 

![Använda DevTest Labs för träning](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

I den här artikeln lär du olika funktioner i Azure DevTest Labs som kan användas för att uppfylla kraven för utvecklare och detaljerade anvisningar som du kan följa för att ställa in ett labb.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementera developer miljöer med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs utgör startpunkten i Azure DevTest Labs. När du skapar ett labb kan kan du utföra uppgifter som att lägga till användare (utvecklare) i laboratoriet kan ange principer för att styra kostnader, definiera VM-avbildningar som kan skapa snabbt, med mera.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig mer om att skapa ett labb i Azure DevTest Labs i Azure-portalen. |
2. **Skapa virtuella datorer i minuter med hjälp av fördefinierade marketplace-bilder och anpassade avbildningar** 
   
    Du kan välja färdiga avbildningar från en mängd olika avbildningar i Azure Marketplace och göra dem tillgängliga i labbet. Om de färdiga bilderna inte uppfyller dina krav kan skapa du en anpassad avbildning genom att skapa ett labb VM med en färdiga avbildning från Azure Marketplace, installera den programvara som du behöver och sparar den virtuella datorn som en anpassad avbildning i labbet.

    Om du kommer att använda anpassade avbildningar, Överväg att använda en avbildning factory att skapa och distribuera bilderna. En avbildning fabriken är en lösning för som konfigurationskod som regelbundet skapar och distribuerar bilderna konfigurerade automatiskt. Detta sparar tid som krävs för att manuellt konfigurera systemet efter att en virtuell dator har skapats med det grundläggande Operativsystemet.
  
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-bilder](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan godkända Azure Marketplace-bilder, gör att välja endast avbildningarna du vill använda för utvecklare.|
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att installera före den programvara du behöver så att utvecklare kan snabbt skapa en virtuell dator med hjälp av den anpassade avbildningen.|
   | [Lär dig mer om image fabriken](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Se en video som beskriver hur du skapar och använder en bild-fabriken.|

3. **Skapa återanvändbara mallar för utvecklare datorer** 
   
    En formel i Azure DevTest Labs är en lista över egenskapen standardvärden används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en bild, en VM-storlek (en kombination av Processorn och RAM-minne) och ett virtuellt nätverk. Varje utvecklare kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en bild, VM-storlek (kombination av Processorn och RAM-minne) och ett virtuellt nätverk.|

4. **Skapa de artefakter för att aktivera flexibel VM-anpassning**

   Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

   - Verktyg som du vill installera på den virtuella datorn – t.ex agenter, Fiddler och Visual Studio.
   - Åtgärder som du vill köra på den virtuella datorn – t.ex att klona en repo.
   - Program som du vill testa.

   Många artefakter är redan tillgänglig out-of-the-box. Du kan skapa egna anpassade artefakter om du vill ha mer anpassning för dina specifika behov.

   Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa anpassade artefakter för den virtuella datorn med DevTest Labs](devtest-lab-artifact-author.md) |Skapa egna anpassade artefakter för virtuella datorer i labbet.|
   | [Lägg till en Git-lagringsplats för att lagra anpassade artefakter och Azure Resource Manager-mallar för användning i Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Lär dig mer om att lagra dina anpassade artefakter i din egen privata Git-lagringsplatsen.|

5. **Kontrollera kostnader**
   
    Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en utvecklare i labbet. 
   
    Om din developer-grupp har en uppsättning fungerar schema och du vill stoppa alla virtuella datorer på en viss tid på dagen och sedan automatiskt starta om dem följande dag, kan du enkelt göra det genom att ange principer för automatisk avstängning och automatisk start i labbet. 
   
    Slutligen när app-utveckling är klar, du kan ta bort alla virtuella datorer samtidigt genom att köra en enda PowerShell-skript. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnader genom att ange principer i labbet. |
   | [Ta bort alla labbet virtuella datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labs i en enda åtgärd när är slutförda.|

1. **Lägg till ett virtuellt nätverk till en virtuell dator** 
   
    DevTest Labs skapar ett nytt virtuellt nätverk (VNET) när ett labb skapas. Om du har konfigurerat dina egna virtuella nätverk – till exempel med hjälp av ExpressRoute eller plats-till-plats VPN – du kan lägga till detta virtuella nätverk inställningarna för ditt virtuella nätverk så att den är tillgänglig när du skapar virtuella datorer.

    Dessutom är en Azure Active Directory-domän koppling artefakt tillgängligt som kommer att ansluta till en virtuell dator till en domän när den virtuella datorn skapas. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md) |Lär dig hur du konfigurerar ett virtuellt nätverk i Azure DevTest Labs med Azure-portalen.|

6. **Dela labbet med varje utvecklare**
   
    Övningar kan nås direkt med en länk som du delar med utvecklarna. De behöver inte ha en Azure-konto så länge som de har en [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Utvecklare kan inte se virtuella datorer som skapats av andra utvecklare.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägga till en utvecklare i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure-portalen för att lägga till utvecklare i labbet.|
   | [Lägg till utvecklare i labbet använder ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera att lägga till utvecklare att ditt labb. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur utvecklare kan direkt åtkomst till ett labb via en hyperlänk.|

7. **Automatisera genereringen av testlabb för flera team** 
   
    Du kan automatisera genereringen av lab, inklusive anpassade inställningar genom att skapa en mall för hanteraren för filserverresurser och använder den för att skapa identiska labs och om igen. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb som använder en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

