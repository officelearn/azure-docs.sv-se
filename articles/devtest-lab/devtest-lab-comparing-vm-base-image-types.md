---
title: "Jämföra anpassade avbildningar och formler i DevTest Labs | Microsoft Docs"
description: "Lär dig mer om skillnaderna mellan anpassade avbildningar och formler som VM baserar så att du kan bestämma vilket som passar din miljö."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: v-craic
ms.openlocfilehash: 78c0255f142bd3d4b2311ac953541153b72ac12d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Jämföra anpassade avbildningar och formler i DevTest Labs
Båda [anpassade avbildningar](devtest-lab-create-template.md) och [formler](devtest-lab-manage-formulas.md) kan användas som grund för [skapa nya virtuella datorer](devtest-lab-add-vm.md). Dock viktiga skillnaden mellan anpassade avbildningar och formler är att en anpassad avbildning är helt enkelt en avbildning baserat på en virtuell Hårddisk, medan en formel är en avbildning baserat på en virtuell Hårddisk *förutom* förkonfigurerade inställningar – till exempel VM-storlek, virtuella nätverk, undernät och artefakter. Inställningarna förinställda ställs in med standardvärden som kan åsidosättas vid tidpunkten för att skapa en virtuell dator. Den här artikeln förklarar några av (tekniker) och nackdelar (nackdelar) till att använda anpassade avbildningar jämfört med formler.

## <a name="custom-image-pros-and-cons"></a>Anpassad bild- och nackdelar
Anpassade avbildningar är statisk, ändras kan du skapa virtuella datorer från en önskad miljö. 

**Tekniker**

* Etablering av virtuell dator från en anpassad avbildning är snabb eftersom inget ändras när den virtuella datorn är de från avbildningen. Med andra ord, finns det inga inställningar att tillämpa den anpassade avbildningen är en bild utan inställningar. 
* Virtuella datorer skapas från en anpassad bild är identiska.

**Nackdelar**

* Om du behöver uppdatera viss aspekt av den anpassade avbildningen måste du återskapa avbildningen.  

## <a name="formula-pros-and-cons"></a>Formeln- och nackdelar
Formler ger ett dynamiskt sätt att skapa virtuella datorer från önskade konfigurationsinställningarna.

**Tekniker**

* Ändringar i miljön kan hämtas direkt via artefakter. Om du vill att en virtuell dator som installerats med den senaste bits från din pipeline versionen eller registrera senaste koden från din lagringsplats kan du bara ange en artefakt som distribuerar den senaste bits eller anlitar senaste koden i formeln tillsammans med en mål-basavbildning. När den här formeln används för att skapa virtuella datorer, är den senaste bits/kod distribueras/registrerad till den virtuella datorn. 
* Formler kan definiera standardinställningar som anpassade avbildningar inte kan tillhandahålla - som VM-storlekar och inställningarna för virtuella nätverk. 
* Inställningarna sparas i en formel som standardvärden, men kan ändras när den virtuella datorn skapas. 

**Nackdelar**

* Skapa en virtuell dator från en formel kan det ta längre tid än att skapa en virtuell dator från en anpassad avbildning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anpassade avbildningar eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
- [DevTest Labs vanliga frågor och svar](devtest-lab-faq.md)