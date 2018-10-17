---
title: Felsökning av Microsoft Azure Stack | Microsoft Docs
description: Azure Stack-felsökning.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c463599190c5bfaac47a70dbca7b8a67dc830f3a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363494"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Felsökning av Microsoft Azure Stack

Det här dokumentet innehåller vanliga felsökningsinformation för Azure Stack. 

> [!NOTE]
> Eftersom Azure Stack teknisk Development Kit (ASDK) erbjuds som en utvecklingsmiljö, finns det inga officiella support från Microsoft kundsupport. Om du har uppstått ett problem, se till att kontrollera den [MSDN-Forum för Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) behöver ytterligare hjälp och information.  

Rekommendationer för att felsöka problem som beskrivs i det här avsnittet härleds från flera källor och kan eller inte kan lösa ditt problem. Kodexempel som tillhandahålls som de är och kan inte garanteras önskat resultat. Det här avsnittet är föremål för frekventa ändringar och uppdateringar som förbättringar av produkten implementeras.

## <a name="deployment"></a>Distribution
### <a name="deployment-failure"></a>Distributionsfel
Om det uppstår ett fel under installationen kan du starta om distributionen från det felande steget med hjälp av kör alternativet - av distributionsskriptet.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>I slutet av ASDK distribution PowerShell-session är fortfarande öppen och visar inte inga utdata.
Detta är förmodligen bara resultatet av en PowerShell-kommandofönster standardbeteende när den har valts. Development kit distributionen har slutförts men skriptet pausades när du väljer fönstret. Du kan kontrollera att installationen har slutförts genom att leta efter ordet ”Välj” i kommandofönstret namnlist.  Tryck på ESC för att avmarkera det och Slutförandemeddelande som ska visas efter den.

## <a name="virtual-machines"></a>Virtuella datorer
### <a name="default-image-and-gallery-item"></a>Bild- och galleriet standardobjekt
Ett Windows Server-avbildning och galleri-objekt måste läggas till innan du distribuerar virtuella datorer i Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Efter omstart mitt Azure Stack-värden måste kanske vissa virtuella datorer inte startar automatiskt.
Efter omstart värden, kanske du märker Azure Stack-tjänster inte är omedelbart tillgängliga.  Detta beror på att Azure Stack [infrastrukturens virtuella datorer](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) och resursprovidrar ta lite tid att kontrollera konsekvens, men så småningom startar automatiskt.

Du kanske också ser den klient som virtuella datorer inte starta automatiskt efter en omstart av Azure Stack development kit värden. Detta är ett känt problem och krävs några manuella steg för att ansluta dem:

1.  Starta på Azure Stack development kit värden **Klusterhanteraren** från Start-menyn.
2.  Välj klustret **S Cluster.azurestack.local**.
3.  Välj **roller**.
4.  Virtuella klientdatorer som visas i en *sparade* tillstånd. När alla infrastrukturens virtuella datorer körs, högerklicka på de virtuella klientdatorerna och välj **starta** att återuppta den virtuella datorn.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Jag har tagit bort vissa virtuella datorer, men finns fortfarande i VHD-filer på disk. Förväntas problemet?
Ja, detta är förväntat. Den utformades för det här sättet eftersom:

* När du tar bort en virtuell dator, raderas inte virtuella hårddiskar. Diskar är separata resurser i resursgruppen.
* När ett lagringskonto tas bort, borttagningen syns direkt via Azure Resource Manager, men de diskar som den kan innehålla fortfarande ska sparas i lagring tills skräpinsamling körs.

Om du ser ”rad” VHD: er, är det viktigt att veta om de ingår i mappen för ett lagringskonto som har tagits bort. Om lagringskontot inte har tagits bort, är det normalt att de är kvar.

Du kan läsa mer om hur du konfigurerar kvarhållning tröskelvärde och på begäran frigöring i [hantera lagringskonton](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Storage frigöring
Det kan ta upp till 14 timmar för återvunnet kapacitet ska visas i portalen. Frigöring av utrymme beror på olika faktorer, inklusive nätverksgränssnittsanvändning i procent av interna behållarfiler i block blob store. Därför beroende på hur mycket data tas bort, finns det ingen garanti på mängden utrymme som kan vara frigöras när skräpinsamlaren körs.

