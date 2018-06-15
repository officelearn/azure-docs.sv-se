---
title: Felsökning av Microsoft Azure-stacken | Microsoft Docs
description: Azure Stack Development Kit (ASDK) felsökningsinformation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6c715f07f75c9196b7cf2cc8659c6e541e1260da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30167910"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Felsökning av Microsoft Azure Stack Development Kit (ASDK)
Det här dokumentet innehåller vanliga felsökningsinformation för ASDK. Om det uppstår ett problem som inte beskrivs, se till att markera den [Azure Stack MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) för ytterligare hjälp och information.  

> [!IMPORTANT]
> Eftersom ASDK är en utvärderingsmiljö, stöds inte officiellt erbjuds via Microsoft kundservice (CSS).

Rekommendationer för att felsöka problem som beskrivs i det här avsnittet härleds från flera källor och kan eller inte kan lösa ditt problem. Kodexempel tillhandahålls ”i befintligt skick” och kan inte garanteras förväntat resultat. Det här avsnittet regleras ofta ändringar och uppdateringar som implementeras förbättringar av produkten.

## <a name="deployment"></a>Distribution
### <a name="deployment-failure"></a>Distributionsfel
Om det uppstår ett fel under installationen måste du starta om distributionen från det felande steget genom att använda kör alternativet - av distributionsskriptet som i följande exempel:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>I slutet av distributionen PowerShell-session är fortfarande öppen och visas inte några utdata
Det här beteendet är förmodligen bara resultatet av standardfunktionen för PowerShell-kommandofönster när den har markerats. Development kit distributionen har slutförts men skriptet pausades när du väljer fönstret. Du kan verifiera installationen har slutförts genom att söka efter ordet ”Välj” i kommandofönstret namnlist. Tryck på ESC för att avmarkera den och Slutförandemeddelande som ska visas efter den.

## <a name="virtual-machines"></a>Virtuella datorer
### <a name="default-image-and-gallery-item"></a>Avbildningen och galleriet standardobjekt
En Windows-avbildning och galleriet serverobjektet måste läggas till innan du distribuerar virtuella datorer i Azure-stacken.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Efter omstart mitt Azure Stack-värden kan kanske vissa virtuella datorer inte startar automatiskt.
Efter omstart värden, märker du kanske Azure Stack-tjänster inte är omedelbart tillgängliga. Detta beror på att Azure stacken [infrastruktur VMs](asdk-architecture.md#virtual-machine-roles) och RPs vissa tid för att kontrollera konsekvens, men till slut startar automatiskt.

Du kan också hända att klienten virtuella datorer inte startar automatiskt efter en omstart av Azure-stacken development kit värden. Detta är ett känt problem och krävs några manuella steg för att ansluta dem:

1.  Starta på Azure-stacken development kit värden **Klusterhanteraren** från Start-menyn.
2.  Markera klustret **S Cluster.azurestack.local**.
3.  Välj **roller**.
4.  Klient virtuella datorer visas i en *sparade* tillstånd. När alla infrastruktur för virtuella datorer som körs, högerklicka på de virtuella klientdatorerna och välj **starta** att återuppta den virtuella datorn.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Jag har tagit bort vissa virtuella datorer, men finns fortfarande VHD-filer på disk. Förväntas problemet?
Ja, är det beteende som förväntat. Det har utformats för det här sättet eftersom:

* När du tar bort en virtuell dator, raderas inte virtuella hårddiskar. Diskar är separata resurser i resursgruppen.
* När du tar bort ett lagringskonto hämtar borttagningen syns direkt via Azure Resource Manager, men de diskar som den kan innehålla är fortfarande förvaras tills skräpinsamling körs.

Om du ser ”rad” virtuella hårddiskar, är det viktigt att veta om de är en del av mappen för ett lagringskonto som har tagits bort. Om lagringskontot inte har tagits bort, är det normalt de finns fortfarande kvar.

Du kan läsa mer om hur du konfigurerar kvarhållning återvinning för tröskelvärde och på begäran i [lagringskonton hantera](.\.\azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Lagring
### <a name="storage-reclamation"></a>Återvinning av lagring
Det kan ta upp till 14 timmar för återvunnet kapacitet visas i portalen. Utrymmesåtertagning beror på olika faktorer, inklusive användning procentandelen interna behållarfiler i block blobstore. Beroende på hur mycket data raderas finns det därför ingen garanti på mängden utrymme som kan frigöras när skräpinsamlingen körs.

## <a name="next-steps"></a>Nästa steg
[Besök forumet för Azure Stack-support](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

