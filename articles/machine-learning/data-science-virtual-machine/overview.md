---
title: Vad är Virtual Machine för Azure Data Science
titleSuffix: Azure Data Science Virtual Machine
description: Översikt över Virtual Machine för Azure Data Science – En enkel att skapa och använda virtuell dator på Azure-molnplattformen med förinstallerade och konfigurerade verktyg och bibliotek för datavetenskap och utveckling av intelligenta program.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 03bfee258fe96d90c32b6a305b99856a11d9a087
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754980"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Vad är Virtual Machine för Azure Data Science för Linux och Windows?

Data Science Virtual Machine (DSVM) är en anpassad VM-avbildning på Azure-molnplattformen som skapats speciellt för datavetenskap. Den har många populära datascience verktyg förinstallerade och förkonfigurerade för att jumpstart bygga intelligenta program för avancerad analys. 

DSVM finns på:

+ **Windows Server 2019**
+ **Ubuntu 18,04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS

> [!NOTE]
> Alla VM-verktyg för djupinlärning har vikts in i virtual machine för datavetenskap. 

## <a name="why-choose-the-dsvm"></a>Varför välja DSVM?

Målet med Virtual Machine för datavetenskap är att ge dataproffs på alla kompetensnivåer och inom branscher en friktionsfri, förkonfigurerad datavetenskapsmiljö. I stället för att distribuera en jämförbar arbetsyta på egen hand kan du etablera en DSVM. Det valet kan spara dagar eller _veckor_ på installations-, konfigurations- och pakethanteringsprocesserna. När din DSVM har allokerats kan du omedelbart börja arbeta med ditt datavetenskapsprojekt.

## <a name="sample-use-cases"></a>Exempel på användningsfall

Nedan illustrerar vi några vanliga användningsfall för DSVM-kunder.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Flytta datavetenskapsarbetsbelastningar till molnet

DSVM tillhandahåller en baslinjekonfiguration för datavetenskapslag som vill ersätta sina lokala skrivbord med ett hanterat molnskrivbord, vilket säkerställer att alla datavetare i ett team har en konsekvent konfiguration för att verifiera experiment och främja samarbete. Det sänker också kostnaderna genom att minska sysadmin bördan. Den här bördan minskning sparar på den tid som behövs för att utvärdera, installera och underhålla programpaket för avancerad analys.

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap

Företagsutbildare och lärare som undervisar i datavetenskap ger vanligtvis en avbildning av virtuella datorer. Avbildningen säkerställer att deltagarna har en konsekvent inställning och att exemplen fungerar förutsägbart. 

DSVM skapar en miljö på begäran med en konsekvent inställning som underlättar support- och inkompatibilitetsutmaningarna. Det är mycket användbart i fall där de här miljöerna behöver skapas ofta, särskilt för kortare kurser.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastisk kapacitet på begäran för storskaliga projekt

Data science hackathons/tävlingar eller storskalig datamodellering och utforskning kräver skalad maskinvarukapacitet, vanligtvis under kort tid. DSVM kan hjälpa till att replikera datavetenskapsmiljön snabbt på begäran, på utskalade servrar som tillåter experiment som kraftfulla datorresurser kan köra.

### <a name="custom-compute-power-for-azure-notebooks"></a>Anpassad beräkningskraft för Azure-anteckningsböcker

[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) är en kostnadsfri värdtjänst för att utveckla, köra och dela Jupyter-anteckningsböcker i molnet utan installation. Den kostnadsfria tjänstnivån är begränsad till 4 GB minne och 1 GB data. 

Om du vill släppa alla gränser kan du koppla ett projekt för anteckningsböcker till en DSVM eller någon annan virtuell dator som körs på en Jupyter-server. Om du loggar in på Azure Notebooks med ett konto med hjälp av Azure Active Directory (till exempel ett företagskonto) visar anteckningsböcker automatiskt DSVM:er i alla prenumerationer som är associerade med det kontot. Du kan [koppla en DSVM till Azure-anteckningsböcker](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) för att expandera den tillgängliga beräkningskraften.

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig experimentering och utvärdering

Du kan använda DSVM för att utvärdera eller lära dig nya [datavetenskapsverktyg,](./tools-included.md)särskilt genom att gå igenom några av våra publicerade [prover och genomgångar.](./dsvm-samples-and-walkthroughs.md)


### <a name="deep-learning-with-gpus"></a>Djupinlärning med GPU:er

I DSVM kan dina träningsmodeller använda djupinlärningsalgoritmer på maskinvara som baseras på grafikprocessorer (GPU:er). Genom att dra nytta av den virtuella datorns skalningsfunktioner för Azure-plattformen hjälper DSVM dig att använda GPU-baserad maskinvara i molnet efter dina behov. Du kan växla till en GPU-baserad virtuell dator när du tränar stora modeller, eller när du behöver höghastighetsberäkningar samtidigt som du behåller samma OS-disk. Du kan välja någon av N-serien GPU-aktiverade virtuella dator SKU:er med DSVM. Observera att kostnadsfria Azure-konton inte stöder GPU-aktiverade SKU:er för virtuella datorer.

Windows-utgåvorna av DSVM levereras förinstallerade med GPU-drivrutiner, ramverk och GPU-versioner av ramverk för djupinlärning. På Linux-utgåvan är djupinlärning på GPU:er aktiverat på Ubuntu DSVMs. 

Du kan också distribuera Ubuntu- eller Windows-utgåvorna av DSVM till en virtuell Azure-dator som inte baseras på GPU:er. I det här fallet kommer alla ramverk för djupinlärning att falla tillbaka till CPU-läget.

[Läs mer om tillgängliga ramverk för djupinlärning och AI](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>
## <a name="whats-included-on-the-dsvm"></a>Vad ingår i DSVM?

Se en fullständig lista över verktyg på både Windows och Linux DSVM är [här](tools-included.md).

## <a name="next-steps"></a>Nästa steg

Läs mer med följande artiklar:

+ Windows:
  + [Konfigurera en Windows DSVM](provision-vm.md)
  + [Tio saker du kan göra på en Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Konfigurera en Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Datavetenskap på en Linux DSVM](linux-dsvm-walkthrough.md)
