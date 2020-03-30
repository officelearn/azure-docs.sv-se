---
title: Skapa erbjudande för virtuella datorer på Azure Marketplace
description: Visar de steg som krävs för att skapa ett nytt virtuellt datorerbjudande (VM) för Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: bc1cf7a839307e65bd91eb29531663141e521472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278049"
---
# <a name="create-virtual-machine-offer"></a>Skapa erbjudande om virtuell dator

I det här avsnittet visas de steg som krävs för att skapa en ny begäran om virtuell dator (VM) för Azure Marketplace.  Varje erbjudande visas som sin egen enhet på Azure Marketplace och är associerat med en eller flera SKU:er.  Ett VM-erbjudande består av följande grupperingar av tillgångar och stödtjänster: 

![Tillgångar för ett VM-erbjudande](./media/publishvm_002.png)

där:

|  **Tillgångsgrupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU: er            |  Den minsta köpbara enheten i ett erbjudande. Ett enda erbjudande (produktklass) kan ha flera SKU:er associerade med det, för att skilja mellan funktioner som stöds, vm-avbildningstyper och faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsförings-, legal- och lead management-tillgångar och specifikationer.  <ul><li> Marknadsföringstillgångar inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar inkluderar en sekretesspolicy, användarvillkor och annan juridisk dokumentation</li>  <li> Med leadhanteringsprincipen kan du ange hur leads ska hanteras från Azure Marketplace-slutanvändarportalen.</li> </ul> |
| Support            | Innehåller supportkontakt- och policyinformation |
| Test Drive         | Definierar tillgångar som gör det möjligt för slutanvändare att testa ditt erbjudande innan de köper det |
|  |  |


## <a name="new-offer-form"></a>Formulär för nytt erbjudande

När du har loggat in på [Cloud Partner Portal](https://cloudpartner.azure.com/)klickar du på + Nytt **erbjudande** i det vänstra menyfältet. I den resulterande menyn klickar du på **virtuella datorer** för att visa formuläret **Nytt erbjudande** och starta processen med att definiera tillgångar för ett nytt vm-erbjudande. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Ny virtuell dator erbjuder val av användargränssnitt](./media/publishvm_003.png)

> [!WARNING]
> Om alternativet **Virtuella datorer** inte visas eller inte är aktiverat har ditt konto inte behörighet att skapa den här erbjudandetypen.  Kontrollera att du har uppfyllt alla [förutsättningar](./cpp-prerequisites.md) för den här erbjudandetypen, inklusive registrering för ett utvecklarkonto.


## <a name="next-steps"></a>Nästa steg

De efterföljande avsnitten i det här avsnittet speglar flikarna på sidan **Nytt erbjudande** (för en virtuell erbjudandetyp).  Varje artikel förklarar hur du använder den associerade fliken för att definiera tillgångsgrupper och stödtjänster för ditt nya vm-erbjudande.

- [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [fliken SKU:er](./cpp-skus-tab.md)
- [Fliken Test Drive](./cpp-test-drive-tab.md)
- [fliken Marketplace](./cpp-marketplace-tab.md)
- [fliken Stöd](./cpp-support-tab.md)
