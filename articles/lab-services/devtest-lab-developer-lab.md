---
title: Använda Azure DevTest Labs för utvecklare | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för scenarier för utvecklare.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38633289"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Använda Azure DevTest Labs för utvecklare
Azure DevTest Labs kan användas för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för utvecklingsdatorer för utvecklare. I det här scenariot har DevTest Labs följande fördelar:

- Utvecklare kan snabbt etablera sina utveckling datorer på begäran.
- Utvecklare kan enkelt anpassa sina utvecklingsdatorer när så behövs.
- Administratörer kan styra kostnaderna genom att säkerställa att:
  - Utvecklare kan inte hämta flera virtuella datorer än de behöver för utveckling.
  - Virtuella datorer är att Stäng av när du inte är i användning. 

![Använda labb för träning](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

I den här artikeln får du lära dig om olika Azure DevTest Labs-funktioner som kan användas för att uppfylla kraven för utvecklare och detaljerade anvisningar som du kan följa för att skapa ett labb.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementera utvecklarmiljöer med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs utgör startpunkten i Azure DevTest Labs. När du skapar ett labb kan utföra du uppgifter som att lägga till användare (utvecklare) i labbet, ange principer för att styra kostnader och definiera VM-avbildningar som kan skapa snabbt, och mycket mer.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig mer om att skapa ett labb i Azure DevTest Labs i Azure-portalen. |
2. **Skapa virtuella datorer på några minuter med hjälp av färdiga marketplace-avbildningar och anpassade avbildningar** 
   
    Du kan välja färdiga avbildningar från ett brett utbud av avbildningar i Azure Marketplace och göra dem tillgängliga i laboratoriet. Om de färdiga avbildningarna inte uppfyller dina krav kan skapa du en anpassad avbildning genom att skapa ett labb virtuell dator med en färdig avbildning från Azure Marketplace, installera all programvara som du behöver, och spara den virtuella datorn som en anpassad avbildning i laboratoriet.

    Om du kommer att använda anpassade avbildningar du använda en avbildning datafabrik att skapa och distribuera dina avbildningar. En bild factory är en konfiguration som kod-lösning som regelbundet skapar och distribuerar bilderna konfigurerade automatiskt. Detta sparar tid som krävs för att manuellt konfigurera systemet när du har skapat en virtuell dator med grundläggande operativsystem.
  
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan godkänna Azure Marketplace-avbildningar, för att göra välja endast avbildningarna du vill använda för utvecklare.|
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att installera före den programvara som du behöver så att utvecklare snabbt kan skapa en virtuell dator med den anpassade avbildningen.|
   | [Lär dig mer om avbildningen factory](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Se en video som beskriver hur du skapar och använder en bild-datafabrik.|

3. **Skapa återanvändningsbara mallar för datorer för utvecklare** 
   
    En formel i Azure DevTest Labs är en lista över standardegenskapsvärden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en bild, en VM-storlek (en kombination av processor och RAM-minne) och ett virtuellt nätverk. Varje utvecklare kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera labb formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en avbildning, VM-storlek (kombination av processor och RAM-minne) och ett virtuellt nätverk.|

4. **Skapa artefakter för att aktivera flexibla VM-anpassning**

   Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

   - Verktyg som du vill installera på den virtuella datorn – t.ex agenter, Fiddler och Visual Studio.
   - Åtgärder som du vill köra på den virtuella datorn – till exempel kloning av en lagringsplats.
   - Program som du vill testa.

   Många artefakter är redan tillgänglig out-of the box. Du kan skapa egna anpassade artefakter om du vill anpassa mer för dina specifika behov.

   Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa anpassade artefakter för din virtuella dator DevTest-labb](devtest-lab-artifact-author.md) |Skapa dina egna anpassade artefakter för virtuella datorer i labbet.|
   | [Lägg till en Git-lagringsplats för att lagra anpassade artefakter och Azure Resource Manager-mallar för användning i Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Lär dig hur du lagrar dina anpassade artefakter i ditt eget privata Git-lagringsplats.|

5. **Kontrollera kostnader**
   
    Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en utvecklare i laboratoriet. 
   
    Om ditt developer-team har en uppsättning fungerar schema och du vill stoppa alla virtuella datorer på en viss tid på dagen och automatiskt starta om dem dagen, kan du enkelt göra det genom att ange principer för automatisk avstängning och starta automatiskt i labbet. 
   
    Slutligen när app-utveckling är klar kan du ta bort alla virtuella datorer samtidigt genom att köra en enda PowerShell-skript. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnader genom att ange principer i laboratoriet. |
   | [Ta bort alla labbets virtuella datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labs i en enda åtgärd när utveckling har slutförts.|

1. **Lägg till ett virtuellt nätverk till en virtuell dator** 
   
    DevTest Labs skapar ett nytt virtuellt nätverk (VNET) varje gång ett labb skapas. Om du har konfigurerat ditt eget virtuellt nätverk – till exempel genom att använda ExpressRoute eller VPN för plats-till-plats – du kan lägga till det här virtuella nätverket inställningar för ditt virtuella nätverk så att den är tillgänglig när du skapar virtuella datorer.

    Det finns dessutom en Azure Active Directory-domän join artefakt tillgängliga som ansluts till en virtuell dator till en domän när den virtuella datorn skapas. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md) |Lär dig hur du konfigurerar ett virtuellt nätverk i Azure DevTest Labs med Azure portal.|

6. **Dela labbet med varje utvecklare**
   
    Labs kan nås direkt med en länk som du delar med dina utvecklare. De behöver inte ha en Azure-konto, så länge som de har en [microsoftkonto](devtest-lab-faq.md#what-is-a-microsoft-account). Utvecklare kan inte se virtuella datorer som skapas av andra utvecklare.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägga till en utvecklare till ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använda Azure-portalen för att lägga till utvecklare i labbet.|
   | [Lägg till utvecklare i labb med hjälp av ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera att lägga till utvecklare till labbet. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur utvecklare kan direkt åtkomst till ett labb via en hyperlänk.|

7. **Automatisera genereringen av labbet för fler lag** 
   
    Du kan automatisera lab skapas, inklusive anpassade inställningar genom att skapa en Resource Manager-mall och använder det för att skapa identiska labs och om igen. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med hjälp av en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med hjälp av Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

