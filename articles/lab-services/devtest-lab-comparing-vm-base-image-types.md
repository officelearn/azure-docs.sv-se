---
title: Jämföra anpassade bilder och formler i DevTest Labs | Microsoft-dokument
description: Lär dig mer om skillnaderna mellan anpassade bilder och formler som VM-baser så att du kan bestämma vilken som passar din miljö bäst.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "64680303"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Jämföra anpassade bilder och formler i DevTest Labs
Både [anpassade bilder](devtest-lab-create-template.md) och [formler](devtest-lab-manage-formulas.md) kan användas som baser för skapade nya virtuella [datorer](devtest-lab-add-vm.md). Den viktigaste skillnaden mellan anpassade bilder och formler är dock att en anpassad avbildning helt enkelt är en bild baserad på en virtuell hårddisk, medan en formel är en bild baserad på en virtuell hårddisk utöver förkonfigurerade inställningar – *till* exempel VM-storlek, virtuellt nätverk, undernät och artefakter. Dessa förkonfigurerade inställningar ställs in med standardvärden som kan åsidosättas när den virtuella datorn skapas. I den här artikeln beskrivs några av fördelarna (för-) och nackdelar (nackdelar) med att använda anpassade bilder jämfört med att använda formler.

## <a name="custom-image-pros-and-cons"></a>Anpassade bild för-och nackdelar
Anpassade bilder är ett statiskt, oföränderligt sätt att skapa virtuella datorer från en önskad miljö. 

**Fördelar**

* VM-etablering från en anpassad avbildning är snabb eftersom ingenting ändras efter att den virtuella datorn har spunnits upp från avbildningen. Med andra ord finns det inga inställningar att använda eftersom den anpassade bilden bara är en bild utan inställningar. 
* Virtuella datorer som skapas från en enda anpassad avbildning är identiska.

**Nackdelar**

* Om du behöver uppdatera någon aspekt av den anpassade avbildningen måste bilden återskapas.  

## <a name="formula-pros-and-cons"></a>Formel för-och nackdelar
Formler är ett dynamiskt sätt att skapa virtuella datorer från önskad konfiguration/inställningar.

**Fördelar**

* Förändringar i miljön kan fångas i farten via artefakter. Om du till exempel vill ha en virtuell dator installerad med de senaste bitarna från versionspipelinen eller registrera den senaste koden från databasen, kan du helt enkelt ange en artefakt som distribuerar de senaste bitarna eller registrerar den senaste koden i formeln tillsammans med en målbas Bild. När den här formeln används för att skapa virtuella datorer distribueras/anordrass/anordras de senaste bitarna/koden till den virtuella datorn. 
* Formler kan definiera standardinställningar som anpassade avbildningar inte kan tillhandahålla , till exempel vm-storlekar och inställningar för virtuella nätverk. 
* Inställningarna som sparas i en formel visas som standardvärden, men kan ändras när den virtuella datorn skapas. 

**Nackdelar**

* Det kan ta längre tid att skapa en virtuell dator från en formel än att skapa en virtuell dator från en anpassad avbildning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
- [Vanliga frågor och svar om DevTest Labs](devtest-lab-faq.md)