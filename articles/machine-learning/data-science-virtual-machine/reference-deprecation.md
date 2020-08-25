---
title: 'Referens: Data Science Virtual Machine avbildnings pensioner'
titleSuffix: Azure Data Science Virtual Machine
description: Information om pensioner som påverkar Azure-Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816328"
---
# <a name="reference-retirements-of-dsvm-images"></a>Referens: borttagning av DSVM-avbildningar

Nedan diskuterar vi pensionering (utfasning) av bilder och förslag på hur du kan hantera kommande indragningar på Azure Data Science Virtual Machine.

## <a name="why-we-retire-dsvm-images"></a>Varför ska vi dra tillbaka DSVM-avbildningar

Data Science Virtual Machine för närvarande har två versioner:

* Ubuntu
* Windows Server

DSVM-avbildningen för dessa versioner bygger på en speciell version av operativ systemet, till exempel Ubuntu 18,04 LTS. Med tiden kommer underhålls perioden för operativ system _versionen_ att avslutas och/eller data vetenskaps verktygen har inte längre stöd för äldre operativ system versioner. För att hålla DSVM-avbildningen uppdaterad med de senaste operativ systemen och data vetenskaps verktyg, lanserar vi en ny DSVM-avbildning baserat på nyare versioner av operativ systemet.

## <a name="how-we-retire-dsvm-images"></a>Så här drar du tillbaka DSVM-avbildningar

Vi utfärdar ett _meddelande_ om att befintliga DSVM-användare meddelas (via e-post) för en kommande DSVM-avbildning. Indragnings meddelandet ger information om _indragnings datumet_ (efter det här datumet då avbildningen inte är tillgänglig) och åtgärder för minskning (till exempel uppgradering till en nyare DSVM-avbildning).

På datumet för _meddelandet_ döljer vi avbildningen i Marketplace så att:

1. nya användare hindras oavsiktligt etablering av en tillbakadragen DSVM-avbildning.
2. befintliga användare kan använda ARM-distributioner fram till indragnings datumet.

Den dolda avbildningen kommer att få operativ Systems uppdateringar tills _indragnings datumet_ , men kommer __inte__ att ta emot uppdateringar av data vetenskaps verktyg och ramverk. På _indragnings datumet_kommer avbildningen inte att vara tillgänglig för arm-distributioner.

Alla etablerade DSVM-avbildningar i prenumerationen kommer att fortsätta att arbeta efter indragnings datumet. Vi rekommenderar dock att du uppgraderar till den senaste DSVM-avbildningen så att du har de senaste operativ systemen och data vetenskaps verktygen.

## <a name="impact"></a>Påverkan

Befintliga DSVM etablerade avbildningar i din prenumeration kommer att fortsätta att arbeta efter indragnings datumet. Vi rekommenderar dock att användare uppgraderar sina DSVM-avbildningar till den nyare versionen med hjälp av en mall för Azure Portal eller ARM.

> [!WARNING]
> Föråldrade DSVM-avbildningar med Virtual Machine Scale Sets kan inte skalas upp efter indragnings datumet.
>
> ARM-mallar som inte har uppdaterats med den nya avbildnings informationen för DSVM kommer att kunna distribueras efter indragnings datumet.

## <a name="mitigating-upcoming-retirements"></a>Minimera kommande pensioner

I det här avsnittet diskuterar vi åtgärder för kommande pensioner.

### <a name="upgrade-windows-2016-dsvm"></a>Uppgradera Windows 2016-DSVM

Utför följande steg för att migrera en datadisk från din befintliga Windows 2016-DSVM till en Windows 2019-DSVM:

1. Skapa en ny Windows 2019-DSVM genom att följa anvisningarna som visas [här](./provision-vm.md#create-your-dsvm).
1. Koppla från befintliga data diskar från Windows 2016-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/detach-disk.md).
1. Koppla disken från föregående steg till Windows 2019-avbildningen med hjälp av [de här anvisningarna](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

### <a name="upgrade-ubuntu-1604-dsvm"></a>Uppgradera Ubuntu 16,04 DSVM

Vi rekommenderar att du uppgraderar befintliga Ubuntu 16,04-Dsvm till [Ubuntu 18,04 DSVM Edition](./dsvm-ubuntu-intro.md).
