---
title: Jämföra anpassade bilder och formler i DevTest Labs | Microsoft Docs
description: Läs mer om skillnaderna mellan anpassade bilder och formler som VM-baser så att du kan avgöra vilken som bäst passar din miljö.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ae7556eda817b9eb7be84f9d4a23ea91d3d5440d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311326"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Jämföra anpassade bilder och formler i DevTest Labs
Båda [anpassade avbildningar](devtest-lab-create-template.md) och [formler](devtest-lab-manage-formulas.md) kan användas som grund för [skapas nya virtuella datorer](devtest-lab-add-vm.md). Dock viktiga skillnaden mellan anpassade bilder och formler är att en anpassad avbildning är helt enkelt en avbildning baserat på en virtuell Hårddisk, medan en formel är en avbildning baserat på en virtuell Hårddisk *förutom* förkonfigurerade inställningar – till exempel VM-storlek, virtuellt nätverk undernät och artefakter. Dessa förkonfigurerade inställningar är inställda med standardvärden som kan åsidosättas när den virtuella datorn skapas. Den här artikeln förklarar några av (personal) och nackdelarna (nackdelar) till att använda anpassade bilder med hjälp av formler.

## <a name="custom-image-pros-and-cons"></a>Anpassad avbildning- och nackdelar
Anpassade avbildningar är en statisk, kan ändras sätt att skapa virtuella datorer från en önskad miljö. 

**Experter**

* Det går snabbt att etablering av virtuella datorer från en anpassad avbildning som ingenting ändras när den virtuella datorn startas från avbildningen. Med andra ord, finns det inga inställningar tillämpas som den anpassade avbildningen är bara en bild utan inställningar. 
* Virtuella datorer som skapas från en enda anpassad avbildning är identiska.

**Nackdelar**

* Om du behöver uppdatera någon aspekt av den anpassade avbildningen måste du återskapa avbildningen.  

## <a name="formula-pros-and-cons"></a>Formeln- och nackdelar
Formler är ett dynamiskt sätt att skapa virtuella datorer från önskad konfigurationsinställningarna.

**Experter**

* Förändringar i miljön kan hämtas direkt via artefakter. Till exempel om du vill att en virtuell dator som installeras med de senaste nyheterna från din releasepipeline eller registrera den senaste koden från din lagringsplats, kan du bara ange en artefakt som distribuerar de senaste nyheterna eller registreras senaste koden i formeln tillsammans med en mål-bas bild. Varje gång den här formeln används för att skapa virtuella datorer, är den senaste bits/koden distribueras/registrerad till den virtuella datorn. 
* Formler kan definiera standardinställningar som anpassade avbildningar inte kan tillhandahålla – till exempel VM-storlekar och inställningar för virtuella nätverk. 
* Inställningarna sparas i en formel visas som standardvärden, men kan ändras när den virtuella datorn skapas. 

**Nackdelar**

* Skapa en virtuell dator från en formel kan det ta längre tid än att skapa en virtuell dator från en anpassad avbildning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
- [DevTest Labs vanliga frågor och svar](devtest-lab-faq.md)