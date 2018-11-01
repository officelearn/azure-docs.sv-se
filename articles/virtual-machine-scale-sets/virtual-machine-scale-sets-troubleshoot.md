---
title: Felsök automatisk skalning med Virtual Machine Scale Sets | Microsoft Docs
description: Felsök automatisk skalning med Virtual Machine Scale Sets. Förstå vanliga problem och hur du löser dem.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: e4b1153e46625f88c717fd9b7a5336ffe4ca7f6a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739557"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Felsöka automatisk skalning med Virtual Machine Scale Sets
**Problemet** – du har skapat en infrastruktur för automatisk skalning i Azure Resource Manager med hjälp av VM-skalningsuppsättningar – till exempel genom att distribuera en mall som den här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – du har din definierade skalningsregler och det fungerar bra, utom Nej betydelse hur mycket inläsning på de virtuella datorerna, inte automatisk skalning.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Några saker att tänka på är:

* Hur många virtuella processorer varje virtuell dator har och du läser in varje vCPU?
  Föregående exempel Azure Quickstart mallen har ett do_work.php-skript som läser in en enda virtuell processor. Om du använder en virtuell dator som är större än en enda vCPU VM-storlek som Standard_A1 eller D1, skulle du behöva köra den här belastningen flera gånger. Kontrollera hur många virtuella processorer för dina virtuella datorer genom att granska [storlekar för Windows-datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hur många virtuella datorer i skalningsuppsättningen för virtuell dator, gör du arbete på var och en?
  
    En skalbar-händelse endast sker när den genomsnittliga CPU över **alla** de virtuella datorerna i en skalningsuppsättning överskrider tröskelvärdet, interna framöver definierats i reglerna för automatisk skalning.
* Missade du alla skalningshändelser?
  
    Kontrollera granskningsloggarna i Azure-portalen för skalningshändelser. Kanske det var en skala upp och en skala ned som uppfylldes inte. Du kan filtrera efter ”skala”.
  
    ![Granskningsloggar][audit]
* Skiljer sig tröskelvärden för in- och skalbar tillräckligt?
  
    Anta att du anger en regel för att skala ut när Processorgenomsnitt är större än 50% under fem minuter och skala i när Processorgenomsnitt är mindre än 50%. Den här inställningen kan orsaka ett problem med ”flapping” när CPU-användning är nära tröskelvärdet med skalningsåtgärder ständigt öka och minska storleken på uppsättningen. På grund av den här inställningen, automatisk skalning tjänsten försöker förhindra ”växlar”, som kan visas som skalas inte. Därför att tröskelvärden för skalning och skala in är tillräckligt olika att tillåta utrymme mellan skalning.
* Du skriva din egen JSON-mallen?
  
    Det är enkelt att göra misstag, så börja med en mall som det ovan som beprövade fungerar och gör små inkrementella ändringar. 
* Kan du manuellt skala in eller ut?
  
    Försök att omdistribuera de VM-skalningsuppsättningen resurs med en annan ”kapacitet” ställa in ändra hur många virtuella datorer manuellt. En exempelmall finns här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – du kan behöva redigera mallen för att kontrollera att den har samma datorstorlek som din Skalningsuppsättning använder. Om du kan ändra hur många virtuella datorer manuellt, vet du sedan problemet är isolerad till automatisk skalning.
* Kontrollera din Microsoft.Compute/virtualMachineScaleSet och Microsoft.Insights resurser i den [resursutforskaren i Azure](https://resources.azure.com/)
  
    Azure Resource Explorer är ett oumbärligt verktyg för felsökning som visar tillståndet för dina Azure Resource Manager-resurser. Klicka på din prenumeration och titta på den resursgrupp som du felsöker. Titta på virtuella datorns skaluppsättning du skapade och kontrollera instansvyn, som visar tillståndet för en distribution under Compute-resursprovidern. Kontrollera också instansvyn för virtuella datorer i virtuella datorns skalningsuppsättning. Sedan går du till resursprovidern Microsoft.Insights och kontrollera att reglerna för automatisk skalning ser ut.
* Diagnostiktillägget fungerar och är avger prestandadata?
  
    **Uppdatering:** automatisk skalning i Azure har förbättrats för att använda en pipeline för värdbaserade mått som kräver inte längre en diagnostiktillägget installeras. Följande stycken gäller inte längre om du skapar ett program med autoskalning med ny pipeline. Ett exempel på Azure-mallar som har konverterats om du vill använda värd pipelinen finns här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Använder värdbaserade mått för autoskalning är bättre av följande skäl:
  
  * Färre rörliga delar som inga diagnostics-tillägg måste vara installerade.
  * Enklare mallar. Lägga till regler för automatisk skalning av insikter till en befintlig mall för skalningsuppsättningen.
  * Mer tillförlitliga reporting och snabbare startas av nya virtuella datorer.
    
    De enda orsakerna till kanske du vill fortsätta att använda en diagnostiktillägget är om du behöver minne diagnostik reporting/skalning. Värdbaserade måtten rapporterar inte minne.
    
    Med det i åtanke endast Följ resten av den här artikeln om du använder diagnostik tillägg för dina automatisk skalning.
    
    Automatisk skalning i Azure Resource Manager kan arbeta (men inte längre) med hjälp av en virtuell dator kallas tillägget Diagnostics-tillägg. Den genererar prestandadata till ett lagringskonto som du definierar i mallen. Dessa data sammanställs sedan av Azure Monitor-tjänsten.
    
    Om tjänsten Insights inte kan läsa data från de virtuella datorerna, ska det skickar dig ett e-postmeddelande. Exempelvis kan få du ett e-postmeddelande om de virtuella datorerna är otillgängliga. Glöm inte att kontrollera din e-post, till den e-postadress som du angav när du skapade ditt Azure-konto.
    
    Du kan också titta på data själv. Titta på Azure storage-kontot med en cloud explorer. Till exempel med hjälp av den [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), logga in och välj den Azure-prenumeration du använder. Titta sedan på diagnostik för lagringskontonamnet som refereras till i definitionen för diagnostik-tillägget i mallen för distribution.
    
    ![Cloud Explorer][explorer]
    
   Du ser en massa tabeller där data från varje virtuell dator lagras. Med Linux- och CPU-mått exempelvis titta på det senaste rader. Visual Studio cloud explorer stöder ett frågespråk så att du kan köra en fråga. Du kan till exempel köra en fråga för ”tidsstämpel gt datetime 2016-02-02T21:20:00Z'” att kontrollera att du får de senaste händelserna. Tidszonen som motsvarar UTC. Stöder de data som du ser i det motsvarar skala regler du ställer in? I följande exempel startas Processorn för datorn 20 öka till 100 procent under de senaste fem minuterna.
    
    ![Storage-tabeller][tables]
    
    Om data inte finns där, innebär det att problemet är med diagnostiktillägg som körs i de virtuella datorerna. Om data är det, innebär det att det finns problem med din skalningsregler eller med Insights-tjänsten. Kontrollera [Azure-Status](https://azure.microsoft.com/status/).
    
    När du har gått igenom de här stegen om du fortfarande har problem med automatisk skalning, försöker du att följande resurser: 
    * Läs forum på [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), eller [stackspill](http://stackoverflow.com/questions/tagged/azure) 
    * Logga en supportsamtal. Var beredd på att dela mallen och en vy över dina prestandadata.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
