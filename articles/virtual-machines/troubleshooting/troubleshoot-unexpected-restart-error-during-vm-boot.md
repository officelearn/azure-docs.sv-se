---
title: OS-start – datorn har startats om oväntat eller ett oväntat fel uppstod
description: Den här artikeln innehåller steg för att lösa problem där den virtuella datorn upplever en oväntad omstart eller fel vid installation av Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: daefaca45adb061295928c64b6a0e328a12d8a3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85269124"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>OS-start – datorn har startats om oväntat eller ett oväntat fel uppstod

Den här artikeln innehåller steg för att lösa problem där den virtuella datorn (VM) upplever en oväntad omstart eller fel vid installation av Windows.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar att Windows-installationen Miss känner av följande fel:

**Datorn startades om oväntat eller påträffades ett oväntat fel. Windows-installationen kan inte fortsätta. Installera Windows genom att klicka på OK för att starta om datorn och starta sedan om installationen.**

![Ett fel uppstod när Windows-installationen pågår: datorn startade oväntat om eller påträffade ett oväntat fel. Windows-installationen kan inte fortsätta. Installera Windows genom att klicka på OK för att starta om datorn och starta sedan om installationen.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Fel vid start av installations programmet för Windows: datorn har oväntat startats om eller ett oväntat fel uppstod. Windows-installationen kan inte fortsätta. Installera Windows genom att klicka på OK för att starta om datorn och starta sedan om installationen.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Orsak

Datorn försöker utföra en inledande start av en [generaliserad avbildning](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), men påträffar problem på grund av en anpassad svarsfil (unattend.xml) som bearbetas. Anpassade svarsfiler stöds inte i Azure. 

Svars filen är en särskild XML-fil som innehåller inställnings definitioner och värden för de konfigurations inställningar som du vill automatisera under installationen av en Windows Server-installation av operativ systemet. Konfigurations alternativen innehåller instruktioner för hur du partitionerar diskar, var du hittar Windows-avbildningen som ska installeras, vilka produkt nycklar som ska användas och andra kommandon som du vill köra.

I Azure stöds inte anpassade svarsfiler. Det här felet kan inträffa om du har angett en anpassad **Unattend.xml** -fil med hjälp av `sysprep /unattend:<your file’s name>` alternativet.

I Azure använder du alternativet för att **Ange OOBE (out-of-Box Experience)** i **Sysprep.exe**eller använder i `sysprep /oobe` stället för Unattend.xml-filen.

Det här problemet skapas oftast när du använder **Sysprep.exe** med en lokal virtuell dator för att överföra en GENERALISERAD virtuell dator till Azure. I det här fallet kanske du också är intresse rad av att ladda upp en generaliserad virtuell dator på ett korrekt sätt.

## <a name="solution"></a>Lösning

### <a name="replace-unattended-answer-file-option"></a>Alternativet Ersätt obevakad svarsfil

Den här situationen uppstår när en avbildning förbereds för användning i Azure, men den använde en anpassad svarsfil, som inte stöds i Azure, och du har använt **Sysprep** med en flagga som liknar följande kommando:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- I föregående kommando ersätter du `<NameOfYourAnswerFile.XML>` med namnet på filen.

Åtgärda problemet genom att följa [Azure-vägledningen om hur du förbereder/fångar en avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) och förbereder en ny generaliserad avbildning. Använd inte flagga under Sysprep `/unattend:<answerfile>` . Använd i stället bara flaggorna nedan:

`sysprep /oobe /generalize /shutdown`

- OOBE ( **out-of-Box-Experience** ) är den inställning som stöds för virtuella Azure-datorer.

Du kan också använda **GUI-verktyget för system förberedelse** för att utföra samma uppgift som kommandot ovan genom att välja de alternativ som visas nedan:

- Ange färdiga erfarenheter
- Generalisera
- Avstängning
 
![Fönstret system förberedelse verktyg med alternativen OOBE, generalize och shutdown markerat.](./media/unexpected-restart-error-during-vm-boot/3.png)
