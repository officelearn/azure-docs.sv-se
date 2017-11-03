---
title: "Felsökning av Microsoft Azure-stacken | Microsoft Docs"
description: "Azure Stack-felsökning."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3b40a657ee8eb391d14a38cb95acc0729a8dda21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Felsökning av Microsoft Azure-stacken

*Gäller för: Azure stacken Development Kit*

Det här dokumentet innehåller vanliga felsökningsinformation för Azure-stacken. 

Eftersom Azure Stack tekniska Development Kit erbjuds som en utvärderingsmiljö stöds inte officiellt från Microsoft kundsupport.  Om det uppstår ett problem som inte dokumenterats, se till att markera den [Azure Stack MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) för ytterligare hjälp och information.  

Rekommendationer för att felsöka problem som beskrivs i det här avsnittet härleds från flera källor och kan eller inte kan lösa ditt problem. Kodexempel som tillhandahålls som de är och förväntat resultat kan inte garanteras. Det här avsnittet regleras ofta ändringar och uppdateringar som implementeras förbättringar av produkten.

## <a name="deployment"></a>Distribution
### <a name="deployment-failure"></a>Distributionsfel
Om det uppstår ett fel under installationen, kan du använda alternativet Kör av distributionsskriptet att starta om distributionen från det felande steget.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>I slutet av distributionen PowerShell-session är fortfarande öppen och visas inte några utdata
Det här beteendet är förmodligen bara resultatet av standardfunktionen för PowerShell-kommandofönster när den har markerats. Development kit distributionen faktiskt har slutförts men skriptet pausades när du väljer fönstret. Du kan verifiera så är fallet genom att söka efter ordet ”Välj” i kommandofönstret namnlist.  Tryck på ESC för att avmarkera den och Slutförandemeddelande som ska visas efter den.

## <a name="templates"></a>Mallar
### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure-mall inte kommer att distribuera till Azure-stacken
Kontrollera att:

* Mallen måste använda en Microsoft Azure-tjänst som redan är tillgänglig eller i Förhandsgranska i Azure-stacken.
* API: er som används för en viss resurs som stöds av den lokala instansen av Azure-stacken och att du på en giltig plats (”lokalt” i Azure-stacken development kit vs ”östra USA” eller ”södra Indien” i Azure).
* Du kan granska [i den här artikeln](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) om Test AzureRmResourceGroupDeployment-cmdlets som catch-mindre skillnader vad gäller Azure Resource Manager-syntax.

Du kan också använda Azure Stack-mallar som redan finns i den [GitHub-lagringsplatsen](http://aka.ms/AzureStackGitHub/) som hjälper dig att komma igång.

## <a name="virtual-machines"></a>Virtuella datorer
### <a name="default-image-and-gallery-item"></a>Avbildningen och galleriet standardobjekt
Du måste först lägga till ett Windows Server-avbildning och galleriet objekt innan du distribuerar virtuella datorer i Azure-stacken.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Efter omstart mitt Azure Stack-värden kan kanske vissa virtuella datorer inte startar automatiskt.
Efter omstart värden, märker du kanske Azure Stack-tjänster inte är omedelbart tillgängliga.  Detta beror på att Azure stacken [infrastruktur VMs](azure-stack-architecture.md#virtual-machine-roles) och RPs ta en liten stund att kontrollera konsekvens, men till slut startar automatiskt.

Du kan också hända att klienten virtuella datorer inte startar automatiskt efter en omstart av Azure-stacken development kit värden.  Detta är ett känt problem och krävs några manuella steg för att ansluta dem:

1.  Starta på Azure-stacken development kit värden **Klusterhanteraren** från Start-menyn.
2.  Markera klustret **S Cluster.azurestack.local**.
3.  Välj **roller**.
4.  Klient virtuella datorer visas i en *sparade* tillstånd.  När alla infrastruktur för virtuella datorer som körs, högerklicka på de virtuella klientdatorerna och välj **starta** att återuppta den virtuella datorn.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Jag har tagit bort vissa virtuella datorer, men finns fortfarande VHD-filer på disk. Förväntas problemet?
Ja, är det beteende som förväntat. Det har utformats för det här sättet eftersom:

* När du tar bort en virtuell dator, raderas inte virtuella hårddiskar. Diskar är separata resurser i resursgruppen.
* När du tar bort ett lagringskonto hämtar borttagningen visas direkt via Azure Resource Manager (portal PowerShell), men de diskar som den kan innehålla är fortfarande förvaras tills skräpinsamling körs.

Om du ser ”rad” virtuella hårddiskar, är det viktigt att veta om de är en del av mappen för ett lagringskonto som har tagits bort. Om lagringskontot inte har tagits bort, är det normalt de finns fortfarande kvar.

Du kan läsa mer om hur du konfigurerar kvarhållning återvinning för tröskelvärde och på begäran i [lagringskonton hantera](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Lagring
### <a name="storage-reclamation"></a>Återvinning av lagring
Det kan ta upp till 14 timmar för återvunnet kapacitet visas i portalen. Utrymmesåtertagning beror på olika faktorer, inklusive användning procentandelen interna behållarfiler i block blobstore. Beroende på hur mycket data raderas finns det därför ingen garanti på mängden utrymme som kan frigöras när skräpinsamlingen körs.

## <a name="powershell"></a>PowerShell
### <a name="resource-providers-not-registered"></a>Resursleverantörer har inte registrerats
När du ansluter till klient prenumerationer med PowerShell, ser du att resursleverantörer inte registreras automatiskt. Använd den [Anslut modulen](https://github.com/Azure/AzureStack-Tools/tree/master/Connect), eller kör följande kommando från PowerShell (efter att du [installera och ansluta](azure-stack-connect-powershell.md) som en klient): 
  
       Get-AzureRMResourceProvider | Register-AzureRmResourceProvider

## <a name="cli"></a>CLI

* CLI interaktivt läge engångsfaktorautentisering den `az interactive` kommandot stöds inte ännu i Azure-stacken.
* Om du vill hämta listan över tillgängliga i Azure-stacken avbildningar av virtuella datorer använder den `az vm images list --all` kommandot i stället för den `az vm image list` kommando. Ange den `--all` alternativet ser till att svaret returnerar bara de avbildningar som är tillgängliga i Azure Stack-miljö. 
* Virtuella avbildningen alias som är tillgängliga i Azure kan inte tillämpas på Azure-stacken. När du använder avbildningar av virtuella datorer, måste du använda parametern hela URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) i stället för det bild aliaset. Och den här URNmust matcha avbildningen specifikationer som härletts från den `az vm images list` kommando.
* Som standard använder CLI 2.0 ”Standard_DS1_v2” som standardstorlek för avbildning av virtuell dator. Men den här storleken inte är tillgängligt i Azure-stacken, så du måste ange den `--size` explicit när du skapar en virtuell dator. Du kan hämta listan över storlekar för virtuella datorer som är tillgängliga i Azure-stacken genom att använda den `az vm list-sizes --location <locationName>` kommando.


## <a name="windows-azure-pack-connector"></a>Windows Azure Pack-koppling
* Du kan inte längre konfigurera flera molnet läge om du ändrar lösenordet för kontot azurestackadmin när du har distribuerat Azure Stack development kit. Den fungerar därför inte möjligt att ansluta till Windows Azure Pack målmiljön.
* När du ställer in läget för flera molnet:
    * En användare kan se instrumentpanelen förrän de återställa portalinställningar. (I användarportalen, klicka på ikonen portalinställningar (kugghjulsikonen i det övre högra hörnet). Under **återställa standardinställningarna**, klickar du på **Använd**.)
    * Instrumentpanelen kan inte rubriker. Om det här problemet uppstår, du måste manuellt lägga till dem igen.
    * Vissa paneler kanske inte visas korrekt när du lägger till dem på instrumentpanelen. Uppdatera webbläsaren om du vill åtgärda problemet.



