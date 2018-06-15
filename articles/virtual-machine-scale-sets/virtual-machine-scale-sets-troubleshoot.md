---
title: Felsöka Autoskala med Virtual Machine-Skalningsuppsättningar | Microsoft Docs
description: Felsöka Autoskala med virtuella datorer. Förstå vanliga problem och hur du löser dem.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: ea634ea8bcb4fed1ed63dc8d1e17d215a00758c6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161030"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Felsöka Autoskala med virtuella datorer
**Problem** – du har skapat en autoskalning infrastruktur i Azure Resource Manager med skaluppsättningar för den virtuella datorn – t.ex, genom att distribuera en mall som det här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – du har din definierade regler för skala och den passar utmärkt, utom Nej betydelse hur mycket inläsning på de virtuella datorerna kan den inte Autoskala.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Vissa saker att tänka på är:

* Hur många vCPUs finns varje virtuell dator, och du läser in varje vCPU?
  Föregående exempel Azure Quickstart mallen har ett do_work.php skript som läser in en enskild vCPU. Om du använder en virtuell dator som är större än en enda vCPU VM-storlek som Standard_A1 eller D1 skulle du behöva köra den här inläsningen flera gånger. Kontrollera hur många vCPUs för dina virtuella datorer genom att granska [storlekar för Windows-datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hur många virtuella datorer i skaluppsättning för virtuell dator allt arbete på var och en?
  
    En skalbar-händelse endast sker när Genomsnittlig CPU över **alla** de virtuella datorerna i en skaluppsättning överskrider tröskelvärdet, över tid internt definierats i reglerna Autoskala.
* Missade du skala händelser?
  
    Kontrollera granskningsloggar i Azure-portalen för skala händelser. Kanske det fanns en skala upp och en skala ned som har missats. Du kan filtrera efter ”skala”.
  
    ![Granskningsloggar][audit]
* Är din skala i och skalbar tröskelvärden tillräckligt?
  
    Anta att du anger en regel för att skala ut när Genomsnittlig CPU är större än 50% över fem minuter och för att skala när Genomsnittlig CPU är mindre än 50%. Den här inställningen innebär att ett ”flapping” problem när CPU-användningen ligger nära tröskelvärdet med skalningsåtgärder ständigt öka och minska storleken på uppsättningen. På grund av den här inställningen försöker Autoskala tjänsten förhindra ”växlar”, som kan visas som inte skalning. Därför vara säker på att din skalbar och tröskelvärden för skalan är tillräckligt olika att utrymme mellan skalning.
* Du skriva en egen JSON-mall?
  
    Det är lätt att göra misstag, så starta med en mall som en ovan som bevisas att fungera och göra små inkrementella ändringar. 
* Kan du manuellt skala in eller ut?
  
    Försök att omdistribuera virtuella datorns skaluppsättning ange resursen med en annan ”kapacitet” anger för att ändra antalet virtuella datorer manuellt. En exempel-mallen finns här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – du kan behöva redigera mallen för att kontrollera att den har samma datorstorlek som Scale Set använder. Om du kan ändra antalet virtuella datorer manuellt, vet du sedan problemet är isolerad för att Autoskala.
* Kontrollera din Microsoft.Compute/virtualMachineScaleSet och Microsoft.Insights resurser i den [resursutforskaren i Azure](https://resources.azure.com/)
  
    Resursutforskaren i Azure är ett nödvändigt felsökning verktyg som visar tillståndet för dina Azure Resource Manager-resurser. Klicka på din prenumeration och titta på den resursgrupp som du felsöker. Titta på virtuella datorns skaluppsättning du skapat och kontrollera instansvyn som visar tillståndet för en distribution under Compute-resursprovidern. Kontrollera också instansvyn för virtuella datorer i virtuella datorns skaluppsättning. Sedan gå till resursprovidern Microsoft.Insights och kontrollera att Autoskala regler ser ut.
* Diagnostiska tillägget fungerar och är avger prestandadata?
  
    **Uppdatering:** Azure Autoskala har förbättrats för att använda en värdbaserad mått pipelinen, som kräver inte längre ett diagnostik-tillägg som ska installeras. Nästa några stycken användas inte längre om du skapar ett autoskalning program med hjälp av ny pipeline. Ett exempel på Azure-mallar som har konverterats för att använda värden pipeline finns här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Med hjälp av värdbaserad mätvärden för Autoskala är bättre av följande skäl:
  
  * Färre rörliga delar som inga diagnostik tillägg måste installeras.
  * Enklare mallar. Lägg bara till insikter Autoskala regler till en befintlig mall för skala.
  * Mer tillförlitlig rapportering och snabbare start av nya virtuella datorer.
    
    Endast skälen kanske du vill fortsätta att använda tillägget diagnostiska är om du behöver minne diagnostik reporting/skalning. Värdbaserad mått rapporterar inte minne.
    
    Med detta i åtanke bara följa resten av den här artikeln om du använder diagnostiska tillägg för din autoskalning.
    
    Autoskala i Azure Resource Manager kan arbeta (men har inte längre att) med hjälp av en virtuell dator tillägget kallas diagnostik-tillägg. Den skickar prestandadata till ett lagringskonto som du definierar i mallen. Informationen sammanställs sedan av Azure-Monitor-tjänsten.
    
    Om Insights-tjänsten inte kan läsa data från de virtuella datorerna, ska den skicka ett e-postmeddelande. Till exempel få ett e-postmeddelande om de virtuella datorerna är nere. Glöm inte att kontrollera din e-post, till den e-postadress som du angav när du skapade ditt Azure-konto.
    
    Du kan också se data själv. Titta på Azure storage-konto med hjälp av en cloud explorer. Till exempel med hjälp av den [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), logga in och välj den Azure-prenumeration du använder. Titta på diagnostik för lagringskontonamnet som refereras i resurstilläggsdefinitionen diagnostik i mallen för distribution.
    
    ![Cloud Explorer][explorer]
    
   Du ser flera tabeller där data från varje virtuell dator lagras. Tar Linux- och CPU-måttet som ett exempel kan titta på det senaste rader. Visual Studio cloud explorer stöder ett frågespråk så att du kan köra en fråga. Du kan till exempel köra en fråga för ”tidsstämpel gt datetime'2016-02-02T21:20:00Z'” se till att du får de senaste händelserna. Tidszonen som motsvarar UTC. Stöder de data som du ser i det motsvarar skala reglerna du ställa in? I följande exempel startas Processorn för datorn 20 öka till 100% under de senaste fem minuterna.
    
    ![Storage-tabeller][tables]
    
    Om data inte är det, innebär det att problemet har med filnamnstillägget diagnostik körs i de virtuella datorerna. Om data är det, innebär det att det finns problem med din skalningsregler eller Insights-tjänsten. Kontrollera [Azure Status](https://azure.microsoft.com/status/).
    
    När du har genom stegen, om du fortfarande har problem med Autoskala, kan du prova följande resurser: 
    * Läsa forumen på [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), eller [stackspill](http://stackoverflow.com/questions/tagged/azure) 
    * Logga kundsupport. Var beredd på att dela mallen och en vy av prestandadata.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
