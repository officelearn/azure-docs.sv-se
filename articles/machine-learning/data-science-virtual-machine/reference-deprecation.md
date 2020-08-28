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
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001628"
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

