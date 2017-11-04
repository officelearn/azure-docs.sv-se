---
title: "Felsöka Autoskala med Virtual Machine-Skalningsuppsättningar | Microsoft Docs"
description: "Felsöka Autoskala med virtuella datorer. Förstå vanliga problem och hur du löser dem."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: guybo
ms.openlocfilehash: bd45a0fb99a77851aa7b91d23bd4b830b6f5cc7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Felsöka Autoskala med virtuella datorer
**Problemet** – du har skapat en autoskalning infrastruktur i Azure Resource Manager med Skalningsuppsättningar – till exempel genom att distribuera en mall så här: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – du har din definierade regler för skala och passar utmärkt, förutom att det kommer inte Autoskala oavsett hur mycket belastning som du angav i de virtuella datorerna.

## <a name="troubleshooting-steps"></a>Felsökningssteg
Vissa saker att tänka på är:

* Hur många kärnor finns varje virtuell dator, och du läser in varje core?
  Ovanstående exempel Azure Quickstart mallen har ett do_work.php skript som läser in en enda kärna. Om du använder en virtuell dator som är större än en enda kärna VM-storlek som Standard_A1 eller D1 skulle måste du köra den här inläsningen flera gånger. Kontrollera hur många kärnor dina virtuella datorer genom att granska [storlekar för Windows-datorer i Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Hur många virtuella datorer i den Skaluppsättning allt arbete på var och en?
  
    En skalbar händelse tar bara placera när Genomsnittlig CPU över **alla** de virtuella datorerna i en skaluppsättning överskrider tröskelvärdet, över tid internt definierats i reglerna Autoskala.
* Missade du skala händelser?
  
    Kontrollera granskningsloggar i Azure-portalen för skala händelser. Kanske det fanns en skala upp och en skala ned som har missats. Du kan filtrera efter ”skala”...
  
    ![Granskningsloggar][audit]
* Är din skala i och skalbar tröskelvärden tillräckligt?
  
    Anta att du anger en regel för att skala ut när Genomsnittlig CPU är större än 50% under 5 minuter och för att skala när Genomsnittlig CPU är mindre än 50%. Detta innebär en ”flapping” problem när CPU-användningen ligger nära det här tröskelvärdet med skalningsåtgärder ständigt öka och minska storleken på uppsättningen. Därför försöker Autoskala tjänsten förhindra ”växlar”, som kan visas som inte skalning. Därför kontrollera din skalbar och tröskelvärden för skalan är tillräckligt olika att utrymme mellan skalning.
* Du skriva en egen JSON-mall?
  
    Det är lätt att göra misstag, så starta med en mall som en ovan som bevisas att fungera och göra små inkrementella ändringar. 
* Kan du manuellt skala in eller ut?
  
    Försök att omdistribuera VM Scale Set-resursen med en annan ”kapacitet”-inställningen för att ändra antalet virtuella datorer manuellt. En exempelmall för att göra det här är: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – du kan behöva redigera mallen för att kontrollera att den har samma datorstorlek som Scale Set använder. Om du kan ändra antalet virtuella datorer manuellt, vet problemet är isolerad för att Autoskala.
* Kontrollera din Microsoft.Compute/virtualMachineScaleSet och Microsoft.Insights resurser i den [resursutforskaren i Azure](https://resources.azure.com/)
  
    Detta är ett nödvändigt felsökning verktyg som visar tillståndet för dina Azure Resource Manager-resurser. Klicka på din prenumeration och titta på den resursgrupp som du felsöker. Titta på den Skaluppsättning som du skapade under Compute-resursprovidern och kontrollera instansvyn som visar tillståndet för en distribution. Kontrollera också instansvyn för virtuella datorer i VM Scale Set. Sedan gå till resursprovidern Microsoft.Insights och kontrollera Autoskala regler ser bra ut.
* Diagnostiska tillägget fungerar och är avger prestandadata?
  
    **Uppdatering:** Azure Autoskala har förbättrats för att använda en värd baserat mått pipeline som kräver inte längre ett diagnostik-tillägg som ska installeras. Det innebär att nästa några stycken inte längre användas om du skapar ett autoskalning program med hjälp av ny pipeline. Ett exempel på Azure-mallar som har konverterats till använda pipeline värden är: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Med hjälp av värdbaserad mätvärden för Autoskala är bättre av följande skäl:
  
  * Färre rörliga delar som inga diagnostik tillägg måste installeras.
  * Enklare mallar. Lägg bara till insikter Autoskala regler till en befintlig mall för skala.
  * Mer tillförlitlig rapportering och snabbare start av nya virtuella datorer.
    
    Endast skälen kanske du vill fortsätta att använda tillägget diagnostiska skulle vara om du behöver minne diagnostik reporting/skalning. Värdbaserad mått rapporterar inte minne.
    
    Med detta i åtanke bara följa resten av den här artikeln om du fortfarande använder diagnostiska tillägg för din autoskalning.
    
    Autoskala i Azure Resource Manager kan arbeta (men har inte längre att) med hjälp av en virtuell dator tillägget kallas diagnostik-tillägg. Den skickar prestandadata till ett lagringskonto som du definierar i mallen. Informationen sammanställs sedan av Azure-Monitor-tjänsten.
    
    Om Insights-tjänsten inte kan läsa data från de virtuella datorerna, ska den skicka ett e-postmeddelande – till exempel om de virtuella datorerna ned, så kontrollera din e-post (det du angav när du skapar det Azure-kontot).
    
    Du kan också gå och granska data själv. Titta på Azure storage-konto med hjälp av en cloud explorer. Till exempel med hjälp av den [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), logga in och välj den Azure-prenumeration du använder och diagnostik lagringskontonamnet som refereras i resurstilläggsdefinitionen diagnostik i mallen för distribution...
    
    ![Cloud Explorer][explorer]
    
    Här visas flera tabeller där data från varje virtuell dator lagras. Tar Linux- och CPU-måttet som ett exempel kan titta på det senaste rader. Visual Studio cloud explorer stöder ett frågespråk så att du kan köra en fråga som ”tidsstämpel gt datetime'2016-02-02T21:20:00Z'” se till att du får de senaste händelser (antar tid är i UTC). Stöder de data som du ser i det motsvarar skala reglerna du ställa in? I exemplet nedan igång Processorn för datorn 20 öka till 100% under de senaste 5 minuterna...
    
    ![Storage-tabeller][tables]
    
    Om data inte är det, sedan innebär den att problemet har med filnamnstillägget diagnostik körs i de virtuella datorerna. Om data är det, innebär det att det finns problem med din skalningsregler eller Insights-tjänsten. Kontrollera [Azure Status](https://azure.microsoft.com/status/).
    
    När du har genom stegen, om du fortfarande har problem med Autoskala du försöka forumen [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp), eller [stackspill](http://stackoverflow.com/questions/tagged/azure), eller logga kundsupport. Var beredd på att dela mallen och en vy av prestandadata.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
