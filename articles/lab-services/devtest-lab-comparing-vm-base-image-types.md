---
title: Jämföra anpassade bilder och formler i DevTest Labs | Microsoft Docs
description: Lär dig mer om skillnaderna mellan anpassade bilder och formler som virtuella dator baser, så att du kan bestämma vilka som passar din miljö bäst.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "64680303"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Jämföra anpassade bilder och formler i DevTest Labs
Både [anpassade bilder](devtest-lab-create-template.md) och [formler](devtest-lab-manage-formulas.md) kan användas som baser för [skapade nya virtuella datorer](devtest-lab-add-vm.md). Skillnaden mellan anpassade bilder och formler är dock att en anpassad avbildning bara är en bild som baseras på en virtuell hård disk, medan en formel är en avbildning baserad på en virtuell hård disk *förutom* förkonfigurerade inställningar – till exempel VM-storlek, virtuellt nätverk, undernät och artefakter. Dessa förkonfigurerade inställningar ställs in med standardvärden som kan åsidosättas när den virtuella datorn skapas. Den här artikeln förklarar några av fördelarna (-och nack delarna) med att använda anpassade bilder jämfört med formler.

## <a name="custom-image-pros-and-cons"></a>Anpassade image-tekniker och nack delar
Anpassade avbildningar ger ett statiskt och oföränderligt sätt att skapa virtuella datorer från en önskad miljö. 

**Fördelar**

* Etablering av virtuella datorer från en anpassad avbildning är snabb, precis som inga ändringar efter att den virtuella datorn har anpassats från avbildningen. Det finns alltså inga inställningar att tillämpa eftersom den anpassade avbildningen bara är en bild utan inställningar. 
* Virtuella datorer som har skapats från en enda anpassad avbildning är identiska.

**Nack delar**

* Om du behöver uppdatera någon aspekt av den anpassade avbildningen måste avbildningen återskapas.  

## <a name="formula-pros-and-cons"></a>Formel för-och nack delar
Formler ger ett dynamiskt sätt att skapa virtuella datorer från önskad konfiguration/inställningar.

**Fördelar**

* Ändringar i miljön kan samlas in direkt via artefakter. Om du till exempel vill att en virtuell dator ska installeras med de senaste bitarna från din versions pipeline eller om du anger den senaste koden från din lagrings plats, kan du bara ange en artefakt som distribuerar de senaste bitarna eller registrerar den senaste koden i formeln tillsammans med en mål bas avbildning. När den här formeln används för att skapa virtuella datorer distribueras/registreras de senaste bitarna/kodarna till den virtuella datorn. 
* Formler kan definiera standardinställningar som anpassade avbildningar inte kan tillhandahålla – till exempel VM-storlekar och virtuella nätverks inställningar. 
* Inställningarna som sparas i en formel visas som standardvärden, men kan ändras när den virtuella datorn skapas. 

**Nack delar**

* Det kan ta längre tid att skapa en virtuell dator från en formel än att skapa en virtuell dator från en anpassad avbildning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogg inlägg
* [Anpassade bilder eller formler?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Nästa steg
- [Vanliga frågor och svar om DevTest Labs](devtest-lab-faq.md)