---
title: Använd Azure DevTest Labs för virtuella datorn och PaaS testmiljöer | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för den virtuella datorn och PaaS testa scenarier för miljön.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 58ab6d502ec5397604c562aedffddb9f48cbb699
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788398"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Använd Azure DevTest Labs för virtuella datorn och PaaS testmiljöer

Du kan använda Azure DevTest Labs för att implementera många viktiga scenarier, men en primär scenariet inbegriper med DevTest Labs för värddatorerna för Testare. 

I det här scenariot har DevTest Labs följande fördelar:

- Testare kan testa den senaste versionen av sina program genom att snabbt etablera Windows och Linux-miljöer med hjälp av återanvändbara mallar och artefakter.
- Testare kan skala upp sina belastningen testar genom att etablera flera test-agenter.
- Administratörer kan styra kostnader genom att säkerställa att:
  - Testare kan inte hämta flera virtuella datorer än de behöver.
  - Virtuella datorer är avstängd när inte i användning.

![Använda DevTest Labs för träning](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

I den här artikeln får du lära dig om olika Azure DevTest Labs funktioner som används för att uppfylla krav på tester och detaljerade anvisningar följer för att ställa in ett labb.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementera testmiljöer med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labs utgör startpunkten i Azure DevTest Labs. När du skapar ett labb kan utföra du uppgifter som att lägga till användare (Testare) i labbet, ange principer för att styra kostnader, definiera VM-avbildningar som kan skapa snabbare och mer.  
   
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
   | [Konfigurera Azure Marketplace-bilder](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan godkända Azure Marketplace-bilder, gör att välja endast avbildningarna du vill använda för Testare.|
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att installera före den programvara du behöver så att Testare kan snabbt skapa en virtuell dator med hjälp av den anpassade avbildningen.|
   | [Lär dig mer om image fabriken](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Se en video som beskriver hur du skapar och använder en bild-fabriken.|

3. **Skapa återanvändbara mallar för testdatorer** 
   
    En formel i Azure DevTest Labs är en lista över egenskapen standardvärden används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en bild, en VM-storlek (en kombination av Processorn och RAM-minne) och ett virtuellt nätverk. Varje tester kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en bild, VM-storlek (kombination av Processorn och RAM-minne) och ett virtuellt nätverk.|

3. **Skapa flera Virtuella testmiljöer** 
   
    Du kan använda Azure Resource Manager-mallar för att definiera infrastrukturen och konfigurationen av din lösning för Azure och upprepade gånger distribuera flera test virtuella datorer i ett konsekvent tillstånd.

    Azure PaaS-resurser kan etableras i en miljö med en Resource Manager-mall och visas i Kostnadsuppföljning. Automatisk avstängning på VM gäller dock inte för PaaS-resurser.

    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md) |Lär dig hur du kan distribuera flera virtuella datorer i ett konsekvent tillstånd för en testmiljö.|

4. **Skapa de artefakter för att aktivera flexibel VM-anpassning**

   Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

   - Verktyg som du vill installera på den virtuella datorn – t.ex agenter, Fiddler och Visual Studio.
   - Åtgärder som du vill köra på den virtuella datorn – t.ex att klona en repo.
   - Program som du vill testa.

   Många artefakter är redan tillgänglig out-of-the-box. Men om du vill ha mer anpassning för dina specifika behov kan du skapa egna anpassade artefakter.

   Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa anpassade artefakter för den virtuella datorn med DevTest Labs](devtest-lab-artifact-author.md) |Skapa egna anpassade artefakter för virtuella datorer i labbet.|
   | [Lägg till en Git-lagringsplats för att lagra anpassade artefakter och Azure Resource Manager-mallar för användning i Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Lär dig mer om att lagra dina anpassade artefakter i din egen privata Git-lagringsplatsen.|

5. **Kontrollera kostnader**
   
    Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas med en tester i labbet. 
   
    Om din test-grupp har en uppsättning fungerar schema och du vill stoppa alla virtuella datorer på en viss tid på dagen och sedan automatiskt starta om dem följande dag, kan du enkelt göra det genom att ange principer för automatisk avstängning och automatisk start i labbet. 
   
    Slutligen när app-utveckling är klar, du kan ta bort alla virtuella datorer samtidigt genom att köra en enda PowerShell-skript. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrollera kostnader genom att ange principer i labbet. |
   | [Ta bort alla labbet virtuella datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labs i en enda åtgärd när testet är klart.|

1. **Lägg till ett virtuellt nätverk i ett labb** 
   
    DevTest Labs skapar ett nytt virtuellt nätverk (VNET) när ett labb skapas. Om du har konfigurerat dina egna virtuella nätverk – till exempel med hjälp av ExpressRoute eller plats-till-plats VPN – du kan lägga till detta virtuella nätverk inställningarna för ditt virtuella nätverk så att den är tillgänglig när du skapar virtuella datorer.

    Dessutom är en Azure Active Directory-domän koppling artefakt tillgängliga som ansluter till en virtuell dator till en domän när den virtuella datorn skapas. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md) |Lär dig hur du konfigurerar ett virtuellt nätverk i Azure DevTest Labs med Azure-portalen.|

6. **Dela labbet med varje tester**
   
    Övningar kan nås direkt med en länk som du delar med din Testare. De behöver inte ha en Azure-konto så länge som de har en [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Testare kan inte se virtuella datorer som skapats av andra Testare.  
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Lägg till en tester i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure-portalen för att lägga till Testare ditt labb.|
   | [Lägg till Testare i labbet använder ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera att lägga till Testare att ditt labb. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur Testare direkt åtkomst till ett labb via en hyperlänk.|

7. **Automatisera genereringen av testlabb för flera team** 
   
    Du kan automatisera genereringen av lab, inklusive anpassade inställningar genom att skapa en mall för hanteraren för filserverresurser och använder den för att skapa identiska labs och om igen. 
   
    Lär dig mer genom att klicka på länkarna i följande tabell:
   
   | Aktivitet | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb som använder en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

