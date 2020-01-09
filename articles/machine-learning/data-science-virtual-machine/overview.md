---
title: Vad är Azure-Data Science Virtual Machine
description: Viktiga analysscenarier och -komponenter för virtuella Windows- och Linux-datorer för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a63087620d50336c67472348da3b7f37fb380635
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611976"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Vad är Azure-Data Science Virtual Machine för Linux och Windows?

Data Science Virtual Machine (DSVM) är en anpassad VM-avbildning på Azure Cloud Platform som skapats specifikt för data vetenskap. Det finns många populära verktyg för data vetenskap förinstallerade och förkonfigurerade för att rivstart med skapa intelligenta program för avancerade analyser. 

DSVM finns på:
+ **Windows Server 2019 (för hands version)**
+ **Ubuntu 18,04 LTS (för hands version)**
+ Windows Server 2016
+ Ubuntu 16,04 LTS och CentOS 7,4


> [!NOTE]
> Alla virtuella dator verktyg för djup inlärning har viktas i Data Science Virtual Machine. 


## <a name="why-choose-the-dsvm"></a>Varför ska jag välja DSVM?
Målet med Data Science Virtual Machine är att tillhandahålla data experter för alla kunskaps nivåer och mellan olika branscher med en friktions fri, förkonfigurerad data vetenskaps miljö. I stället för att distribuera en jämförbar arbets yta på egen hand kan du etablera en DSVM. Detta val kan spara dagar eller till och med _veckor_ i installations-, konfigurations-och paket hanterings processerna. När din DSVM har allokerats kan du omedelbart börja arbeta med ditt datavetenskapsprojekt.

## <a name="sample-use-cases"></a>Exempel på användnings fall

Nedan illustreras några vanliga användnings fall för DSVM-kunder.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Flytta data vetenskaps arbets belastningar till molnet

DSVM tillhandahåller en bas linje konfiguration för data vetenskaps team som vill ersätta sina lokala skriv bord med ett hanterat moln skriv bord, och se till att alla data experter i ett team har en enhetlig konfiguration som du kan använda för att verifiera experiment och främja samarbete. Det minskar också kostnaderna genom att minska sysadmin-belastningen. Den här kostnads minskningen sparar vid den tid som behövs för att utvärdera, installera och underhålla program varu paket för avancerad analys.

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap
Företags utbildare och lärare som undervisar data vetenskaps klasser brukar tillhandahålla en avbildning av en virtuell dator. Avbildningen säkerställer att eleverna har en konsekvent inställning och att exemplen fungerar förutsägbart. 

DSVM skapar en miljö på begäran med en konsekvent konfiguration som underlättar support-och inkompatibilitets utmaningarna. Det är mycket användbart i fall där de här miljöerna behöver skapas ofta, särskilt för kortare kurser.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastisk kapacitet på begäran för storskaliga projekt
Data vetenskap Hackathon kickar/tävlingar eller storskalig data modellering och utforskning kräver skalbar maskin varu kapacitet, vanligt vis för kort varaktighet. DSVM kan hjälpa till att replikera data vetenskaps miljön snabbt på begäran, på utskalade servrar som tillåter att experiment som drivs av stora data resurser kan köras.

### <a name="custom-compute-power-for-azure-notebooks"></a>Anpassad beräknings kraft för Azure Notebooks
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) är en kostnads fri värdbaserad tjänst för att utveckla, köra och dela Jupyter-anteckningsböcker i molnet utan att installera. Den kostnads fria tjänst nivån är begränsad till 4 GB minne och 1 GB data. 

Om du vill frigöra alla gränser kan du bifoga ett Notebook-projekt till en DSVM eller någon annan virtuell dator som körs på en Jupyter-Server. Om du loggar in på Azure Notebooks med ett konto genom att använda Azure Active Directory (till exempel ett företags konto), visar antecknings böcker automatiskt Dsvm i alla prenumerationer som är kopplade till det kontot. Du kan [koppla en DSVM till Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) för att utöka den tillgängliga beräknings kraften.

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig experimentering och utvärdering
Du kan använda DSVM för att utvärdera eller lära dig nya data vetenskaps [verktyg](./tools-included.md), särskilt genom att gå igenom några av våra publicerade [exempel och genom gångar](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Djup inlärning med GPU: er
I DSVM kan dina utbildnings modeller använda djup inlärnings-algoritmer på maskin vara som baseras på GPU: er (Graphics Processing units). Genom att dra nytta av funktionerna för skalning av virtuella datorer i Azure-plattformen kan DSVM hjälpa dig att använda GPU-baserad maskin vara i molnet efter behov. Du kan växla till en GPU-baserad virtuell dator när du tränar stora modeller eller när du behöver snabba beräkningar samtidigt som du behåller samma OS-disk. Du kan välja någon av de N-seriens GPU-aktiverade virtuella datorer SKU: er med DSVM. Observera att de kostnads fria Azure-kontona inte stöder GPU-aktiverade virtuella datorer SKU: er.

Windows Server 2016-versionen av DSVM levereras i förväg med GPU-drivrutiner, ramverk och GPU-versioner av djup inlärnings ramverk. I Linux-versionen har djup inlärning på GPU: er Aktiver ATS på både CentOS-och Ubuntu-Dsvm. 

Du kan också distribuera Ubuntu-, CentOS-eller Windows 2016-versionen av DSVM till en virtuell Azure-dator som inte baseras på GPU: er. I det här fallet kommer alla ramverk för djup inlärning att återgå till CPU-läget.
 
[Lär dig mer om tillgängliga djup inlärnings-och AI-ramverk](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Vad ingår i DSVM?

Se en fullständig lista med verktyg på både Windows-och Linux-DSVM [.](tools-included.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer med de här artiklarna:

+ Windows:
  + [Konfigurera en Windows DSVM](provision-vm.md)
  + [Tio saker du kan göra på en Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Konfigurera en Linux-DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Konfigurera en Linux-DSVM (CentOS)](linux-dsvm-intro.md)
  + [Data vetenskap i en Linux-DSVM](linux-dsvm-walkthrough.md)
