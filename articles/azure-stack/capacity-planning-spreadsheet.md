---
title: Kalkylblad för kapacitetsplanering för Azure Stack | Microsoft Docs
description: Läs mer om kapacitetsplanering kalkylblad för Azure Stack-distributioner.
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
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: d140b80ecb5fe2a7211245044b08f43fde4233a8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240994"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Azure Stack Capacity Planner är ett kalkylblad som används för kapacitetsplanering för Azure Stack-resurs. Capacity planner ger dig möjlighet att utforma olika allokeringen av datorresurser och se hur de skulle passa över ett urval av maskinvara. Detaljerade anvisningar för användning av Azure Stack-Kalkylatorn finns nedan.

## <a name="worksheet-descriptions"></a>Kalkylbladet beskrivningar
Följande är en kort sammanfattning av kalkylblad i Azure Stack Capacity Planner-kalkylblad som kan laddas ned från [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Fliknamn|Beskrivning|
|-----|-----|
|Version-Disclaimer|Kort översikt över syftet med kalkylator, versionsnummer och utgivna datum.|
|Instruktioner|Innehåller detaljerade anvisningar om användning av Azure Stack Capacity Planner.|
|DefinedSolutionSKUs|Tabell med flera kolumner som innehåller definitioner som är upp till fem maskinvara. Poster i det här bladet är exempel. Avsikten är att användaren ändrar information om du vill matcha systemkonfigurationer ses som för användning eller köpa.|
|DefineByVMFootprint|Hitta rätt maskinvara SKU: N genom att skapa en samling av olika storlekar och kvantiteter av virtuella datorer.|
|DefineByWorkloadFootprint|Hitta rätt maskinvara SKU: N genom att skapa en samling med Azure Stack-arbetsbelastningar.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs instruktioner
Det här kalkylbladet innehåller upp till fem maskinvara definition exempel. Ändra uppgifter för att matcha systemkonfigurationer ses som för användning eller köp.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Maskinvara-val som tillhandahålls av behörighet Maskinvarupartner
Azure Stack levereras som ett integrerat system med programvara som installeras av samarbetspartner. Dessa samarbetspartner har sina egna, auktoritativa versioner av Azure Stack kapacitetsplanering verktyg och de verktyg som ska användas för att slutföra diskussioner lösning kapacitet.

### <a name="multiple-ways-to-model-computing-resources"></a>Flera sätt att modellera bearbetningsresurser
De grundläggande byggstenarna som används för resursen modellering i Azure Stack-planner är olika storlekar på Azure Stack-datorer (VM). Dessa virtuella datorer-intervallet i storlek från det minsta ”grundläggande 0”, upp till den aktuella största virtuella datorn, ”Standard_Fsv2”. Utifrån dina behov, kan olika mängder olika virtuella datorer används för att skapa databehandling resursallokeringar med det här verktyget på två olika sätt.

1. Planner-användare väljer en specifik maskinvara erbjudande och ser vilka kombinationer av olika resurser som ska rymmas inom den här maskinvaruresurs. 

2. Planner-användaren skapar en specifik kombination av VM-allokeringar och sedan kan visas i Azure-Kalkylatorn för resursen som tillgänglig maskinvara SKU: er kan ge stöd för VM-konfigurationen.

Det här verktyget erbjuder två metoder för att tilldela VM-resurser. antingen som en enskild samling resursallokeringar för virtuell dator eller som en samling av upp till sex olika konfigurationer för arbetsbelastning. Varje arbetsbelastningskonfiguration kan innehålla en annan fördelning av tillgängliga VM-resurser. Detaljerad information för att skapa och använda var och en av dessa allokering modeller finns nedan. Endast värden i icke-bakgrunden skuggade celler eller inom SKU nedrullningsbara listor på det här kalkylbladet ska ändras. Ändringar som gjorts i skuggade celler kan bryta resource beräkningar.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint instructions
Välj fliken ”DefineByVMFootprint” för att skapa en modell med en enda samling av olika storlekar och kvantiteter av virtuella datorer, och följ den här sekvensen av steg.

1. I det övre högra hörnet av det här kalkylbladet använder du de tillhandahållna nedrullningsbara listrutekontroller för att välja en ursprungsvärdet av servrar (mellan 4 och 16) som ska installeras i varje maskinvarusystem (SKU). Det här antalet servrar som kan ändras när som helst under modelleringsprocessen att se hur det påverkar den totala tillgängliga resurser för din modell.
2. Om du vill att modellera olika VM-resursallokeringar mot en specifik maskinvarukonfiguration, hitta blå nedrullningsbara listrutan direkt under etiketten ”aktuella SKU: N” i det övre högra hörnet på sidan. Hämta den här listrutan och välj din önskade maskinvara SKU.
3. Du är nu redo att börja lägga till olika storlekar virtuella datorer i din modell. För att inkludera en viss typ av virtuell dator, anger du värdet för antal i blått nedan rutan till vänster om den virtuella dator posten.

  > [!NOTE]
  > Totalt antal VM-lagring refererar till den totala kapaciteten för datadisken för den virtuella datorn (antal diskar stöds * maximal kapacitet för en enskild disk (1 TB)). Baserat på konfigurationen indikatorer ifyllda vi tillgängliga lagringskonfigurationer-tabellen så att du kan välja din önskade nivå av lagringsresurs för varje virtuell dator i Azure Stack. Det är dock viktigt att Observera att du kan lägga till eller ändra tabellen tillgängliga lagringskonfigurationer efter behov.<br><br>Varje virtuell dator startar med ett initialt tilldelade lokal temporär lagring. För att återspegla tunn allokering av temporär lagring kan lokala temp-talet ändras till något i nedrullningsbara menyn som bland annat hur maximal tillåten temporär lagring.

4. När du lägger till virtuella datorer visas diagram som visar tillgängliga SKU: N resurser ändra. På så sätt kan du se effekterna av att lägga till olika storlekar och kvantiteter av virtuella datorer under modelleringsprocessen. Ett annat sätt att visa effekten av ändringarna är att titta på förbrukade och fortfarande tillgängliga siffrorna visas direkt nedanför listan med tillgängliga virtuella datorer. Dessa siffror visas beräknade värden baserat på valda SKU-maskinvara.
5. När du har skapat en uppsättning virtuella datorer kan hittar du den föreslagna maskinvaran SKU: N genom att klicka på ”föreslagna SKU” hittades i det övre högra hörnet på sidan, direkt under etiketten ”aktuella SKU: N”. Med den här knappen kan du sedan ändrar dina VM-konfigurationer och se vilka maskinvaran har stöd för varje konfiguration.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint instructions
Välj fliken ”DefineByWorkloadFootprint” för att skapa en modell med hjälp av en samling av arbetsbelastningar på Azure Stack, och följ den här sekvensen av steg. Azure Stack-arbetsbelastningar skapas med hjälp av tillgängliga VM-resurser.   

> [!TIP]
> Om du vill ändra den angivna lagringsstorleken för en Azure Stack-dator, se kommentaren från steg tre i föregående avsnitt.

1. I det övre högra hörnet på den här sidan använder du de nedrullningsbara listan kontrollerna för att välja en ursprungsvärdet av servrar (mellan 4 och 16) som ska installeras i varje maskinvarusystem (SKU).
2. Om du vill att modellera olika VM-resursallokeringar mot en specifik maskinvarukonfiguration, hitta blå nedrullningsbara listrutan direkt under etiketten ”aktuella SKU: N” i det övre högra hörnet på sidan. Hämta den här listrutan och välj din önskade maskinvara SKU.
3. Välj lämplig storlek för var och en av dina önskade Azure Stack virtuella datorer på sidan DefineByVMFootprint enligt beskrivningen ovan under steg tre DefineByVMFootprint instruktioner. Lagringsstorleken per virtuell dator har definierats i DefineByVMFootprint-bladet.
4. Från och med det övre vänstra hörnet av sidan DefineByWorkloadFootprint, skapa konfigurationer för upp till sex olika typer av arbetsbelastningar genom att ange hur många varje typ av virtuell dator som ingår i arbetsbelastningen. Detta görs genom att placera numeriska värden i kolumnen direkt under namn på den arbetsbelastning. Namn på arbetsbelastning kan ändras för att återspegla typ av arbetsbelastningar som stöds av den här specifik konfiguration.
5. Du kan inkludera en viss mängd för varje typ av arbetsbelastning genom att ange ett värde längst ned i kolumnen direkt under etiketten ”kvantitet”.
6. När du har skapat typer av arbetsbelastningar och kvoterna leder att klicka på ”föreslås SKU: N” knappen som finns i det övre högra hörnet på sidan, direkt under etiketten ”aktuella SKU: N” till den minsta SKU: N med tillräckligt med resurser för att stödja den här övergripande konfiguration av arbetsbelastningar som ska visas.
7. Ytterligare modellering kan åstadkommas genom att ändra antalet servrar som valts för en maskinvarubaserad SKU, eller ändra VM-allokeringar eller kvantiteterna inom din arbetsbelastning-konfigurationer. Associerade diagram visar omedelbar feedback som visar hur ändringarna påverkar den övergripande resursförbrukningen.
8. När du är nöjd med ändringarna visas att klicka på knappen ”föreslagna SKU” igen den SKU som föreslås för den nya konfigurationen.


## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [datacenter integration överväganden för Azure Stack](azure-stack-datacenter-integration.md)
