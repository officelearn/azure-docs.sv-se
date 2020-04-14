---
title: Felsöka automatisk skalning med skaluppsättningar för virtuella datorer
description: Felsöka automatisk skalning med skaluppsättningar för virtuella datorer. Förstå vanliga problem som uppstått och hur du löser dem.
author: mimckitt
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: mimckitt
ms.openlocfilehash: 4bc5e66f5b0759bdb5fe34276369161200bd5442
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273383"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Felsöka automatisk skalning med VM-skalningsuppsättningar
**Problem** – du har skapat en infrastruktur för automatisk skalning i Azure Resource Manager med skalningsuppsättningar för virtuella datorer – till exempel genom att distribuera en mall som den här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – du har definierat skalningsreglerna och det fungerar bra, förutom hur mycket belastning du lägger på de virtuella datorerna, det skalar inte automatiskt.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Några saker att tänka på är:

* Hur många virtuella processorer har varje virtuell dator, och läser du in varje vCPU?
  Det föregående exemplet på Azure Quickstart-mallen har ett do_work.php-skript som läser in en enda vCPU. Om du använder en virtuell dator som är större än en virtuell datorstorlek med en virtuell dator som Standard_A1 eller D1 måste du köra den här inläsningen flera gånger. Kontrollera hur många virtuella processorer för dina virtuella datorer genom att granska [storlekar för virtuella Windows-datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hur många virtuella datorer i den virtuella datorn skala uppsättning, gör du arbete på var och en?
  
    En utskalningshändelse äger bara rum när den genomsnittliga processorn för **alla** virtuella datorer i en skalningsuppsättning överskrider tröskelvärdet, över den tid som internt definieras i reglerna för automatisk skalning.
* Missade du några fjällhändelser?
  
    Kontrollera granskningsloggarna i Azure-portalen för skalningshändelser. Kanske fanns det en skala upp och en skala ner som missades. Du kan filtrera efter "Skala".
  
    ![Granskningsloggar][audit]
* Är dina inskalnings- och skalningströsklar tillräckligt olika?
  
    Anta att du anger en regel för att skala ut när den genomsnittliga processorn är större än 50 % under fem minuter och att skala in när den genomsnittliga processorn är mindre än 50 %. Den här inställningen skulle orsaka ett "flapping"-problem när CPU-användningen ligger nära tröskelvärdet, med skalningsåtgärder som ständigt ökar och minskar storleken på uppsättningen. På grund av den här inställningen försöker tjänsten för automatisk skalning förhindra att "flaxa", vilket kan visas som inte skalning. Se därför till att tröskelvärdena för utskalning och skalning är tillräckligt olika för att det ska finnas utrymme mellan skalning.
* Har du skrivit din egen JSON mall?
  
    Det är lätt att göra misstag, så börja med en mall som den ovan som har visat sig fungera, och göra små inkrementella förändringar. 
* Kan du manuellt skala in eller ut?
  
    Prova att distribuera om den virtuella datorns skalningsuppsättningsresurs med en annan "kapacitetsinställning" för att ändra antalet virtuella datorer manuellt. En exempelmall finns https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing här: – Du kan behöva redigera mallen för att se till att den har samma maskinstorlek som skaluppsättningen använder. Om du kan ändra antalet virtuella datorer manuellt vet du att problemet är isolerat till automatisk skalning.
* Kontrollera dina Microsoft.Compute/virtualMachineScaleSet- och Microsoft.Insights-resurser i [Azure Resource Explorer](https://resources.azure.com/)
  
    Azure Resource Explorer är ett oumbärligt felsökningsverktyg som visar tillståndet för dina Azure Resource Manager-resurser. Klicka på din prenumeration och titta på den resursgrupp som du felsöker. Under beräkningsresursprovidern tittar du på den virtuella datorskalauppsättning som du skapade och kontrollerar instansvyn, som visar tillståndet för en distribution. Kontrollera också instansvyn för virtuella datorer i skaluppsättningen för den virtuella datorn. Gå sedan in i microsoft.insights-resursleverantören och kontrollera att reglerna för automatisk skalning ser bra ut.
* Fungerar diagnostiktillägget och avger prestandadata?
  
    **Uppdatering:** Automatisk azure-skalning har förbättrats för att använda en värdbaserad måttpipeline, som inte längre kräver ett diagnostiktillägg som ska installeras. De följande styckena gäller inte längre om du skapar ett automatiskt skalningsprogram med den nya pipelinen. Ett exempel på Azure-mallar som har konverterats för https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscaleatt använda värdpipelinen finns här: . 
  
    Att använda värdbaserade mått för automatisk skalning är bättre av följande skäl:
  
  * Färre rörliga delar eftersom inga diagnostiktillägg behöver installeras.
  * Enklare mallar. Lägg bara till regler för automatisk skalning av insikter i en befintlig skalningsuppsättningsmall.
  * Mer tillförlitlig rapportering och snabbare lansering av nya virtuella datorer.
    
    De enda anledningarna till att du kanske vill fortsätta använda ett diagnostiktillägg är om du behöver minnesdiagnostik rapportering / skalning. Värdbaserade mått rapporterar inte minne.
    
    Med detta i åtanke följer du bara resten av den här artikeln om du använder diagnostiska tillägg för din automatisk skalning.
    
    Automatisk skalning i Azure Resource Manager kan fungera (men inte längre behöver) med hjälp av ett VM-tillägg som kallas Diagnostiktillägg. Prestandadata avger till ett lagringskonto som du definierar i mallen. Dessa data aggregeras sedan av Azure Monitor-tjänsten.
    
    Om insights-tjänsten inte kan läsa data från de virtuella datorerna ska den skicka ett e-postmeddelande till dig. Du får till exempel ett e-postmeddelande om de virtuella datorerna är nere. Var noga med att kontrollera din e-post, på den e-postadress du angav när du skapade ditt Azure-konto.
    
    Du kan också titta på data själv. Titta på Azure-lagringskontot med hjälp av en molnutforskare. Med hjälp av [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)kan du till exempel logga in och välja den Azure-prenumeration du använder. Titta sedan på diagnostiklagringskontonamnet som refereras i diagnostiktilläggsdefinitionen i distributionsmallen.
    
    ![Cloud Explorer][explorer]
    
    Du ser en massa tabeller där data från varje virtuell dator lagras. Med Linux och CPU-måttet som exempel, titta på de senaste raderna. Visual Studio-molnutforskaren stöder ett frågespråk så att du kan köra en fråga. Du kan till exempel köra en fråga för "Tidsstämpel gt datetime'2016-02-02T21:20:00Z"" för att se till att du får de senaste händelserna. Tidszonen motsvarar UTC. Motsvarar de data som visas där de skalningsregler som du har ställt in? I följande exempel började processorn för maskin 20 öka till 100% under de senaste fem minuterna.
    
    ![Lagringstabeller][tables]
    
    Om data inte finns där innebär det att problemet är med diagnostiktillägget som körs i de virtuella datorerna. Om data finns där innebär det att det antingen finns ett problem med dina skalningsregler eller med insights-tjänsten. Kontrollera [Azure-status](https://azure.microsoft.com/status/).
    
    När du har gått igenom de här stegen kan du prova följande resurser om du fortfarande har problem med automatisk skalning: 
    * Läs forumen på [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows)eller [Stack-spill](https://stackoverflow.com/questions/tagged/azure) 
    * Logga ett supportsamtal. Var beredd på att dela mallen och en vy över dina resultatdata.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
