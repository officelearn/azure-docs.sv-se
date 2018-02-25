---
title: "Felsökning av Microsoft Azure-stacken | Microsoft Docs"
description: "Azure Stack-felsökning."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 799a7f7ed7e2373e4cf819a34d5deb362c9e6a3f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Felsökning av Microsoft Azure-stacken

*Gäller för: Azure stacken Development Kit*

Det här dokumentet innehåller vanliga felsökningsinformation för Azure-stacken. 

Eftersom Azure Stack tekniska Development Kit erbjuds som en utvärderingsmiljö stöds inte officiellt från Microsoft kundsupport. Om det uppstår ett problem som inte dokumenterats, se till att markera den [Azure Stack MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) för ytterligare hjälp och information.  

Rekommendationer för att felsöka problem som beskrivs i det här avsnittet härleds från flera källor och kan eller inte kan lösa ditt problem. Kodexempel som tillhandahålls som de är och förväntat resultat kan inte garanteras. Det här avsnittet regleras ofta ändringar och uppdateringar som implementeras förbättringar av produkten.

## <a name="deployment"></a>Distribution
### <a name="deployment-failure"></a>Distributionsfel
Om det uppstår ett fel under installationen måste du starta om distributionen från det felande steget genom att använda kör alternativet - av skriptet för distribution.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>I slutet av distributionen PowerShell-session är fortfarande öppen och visas inte några utdata
Det här beteendet är förmodligen bara resultatet av standardfunktionen för PowerShell-kommandofönster när den har markerats. Development kit distributionen faktiskt har slutförts men skriptet pausades när du väljer fönstret. Du kan verifiera installationen har slutförts genom att söka efter ordet ”Välj” i kommandofönstret namnlist.  Tryck på ESC för att avmarkera den och Slutförandemeddelande som ska visas efter den.

## <a name="virtual-machines"></a>Virtuella datorer
### <a name="default-image-and-gallery-item"></a>Avbildningen och galleriet standardobjekt
En Windows-avbildning och galleriet serverobjektet måste läggas till innan du distribuerar virtuella datorer i Azure-stacken.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Efter omstart mitt Azure Stack-värden kan kanske vissa virtuella datorer inte startar automatiskt.
Efter omstart värden, märker du kanske Azure Stack-tjänster inte är omedelbart tillgängliga.  Detta beror på att Azure stacken [infrastruktur VMs](azure-stack-architecture.md#virtual-machine-roles) och RPs ta en liten stund att kontrollera konsekvens, men till slut startar automatiskt.

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

Du kan läsa mer om hur du konfigurerar kvarhållning återvinning för tröskelvärde och på begäran i [lagringskonton hantera](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Lagring
### <a name="storage-reclamation"></a>Återvinning av lagring
Det kan ta upp till 14 timmar för återvunnet kapacitet visas i portalen. Utrymmesåtertagning beror på olika faktorer, inklusive användning procentandelen interna behållarfiler i block blobstore. Beroende på hur mycket data raderas finns det därför ingen garanti på mängden utrymme som kan frigöras när skräpinsamlingen körs.

## <a name="windows-azure-pack-connector"></a>Windows Azure Pack-koppling
* Du kan inte längre konfigurera flera molnet läge om du ändrar lösenordet för kontot azurestackadmin när du har distribuerat Azure Stack development kit. Den fungerar därför inte möjligt att ansluta till Windows Azure Pack målmiljön.
* När du ställer in läget för flera molnet:
    * En användare kan se instrumentpanelen förrän de återställa portalinställningar. (I användarportalen, klicka på ikonen portalinställningar (kugghjulsikonen i det övre högra hörnet). Under **återställa standardinställningarna**, klickar du på **Använd**.)
    * Instrumentpanelen kan inte rubriker. Om det här problemet uppstår, du måste manuellt lägga till dem igen.
    * Vissa paneler kanske inte visas korrekt när du lägger till dem på instrumentpanelen. Uppdatera webbläsaren om du vill åtgärda problemet.



