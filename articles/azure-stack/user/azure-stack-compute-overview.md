---
title: Introduktion till Azure-stacken virtuella datorer
description: "Lär dig mer om Azure-stacken virtuella datorer"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: c37ad8ac5b6c37261e22237e843dd97e2bbd09f9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introduktion till Azure-stacken virtuella datorer

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="overview"></a>Översikt
Azure Stack virtuell dator (VM) är en typ av på begäran, skalbara datorresurs som erbjuder Azure stacken. Normalt använder du en virtuell dator om du behöver mer kontroll över datormiljön än vad de andra alternativen erbjuder. Den här artikeln innehåller information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

En Azure-stacken VM ger dig virtualiseringsflexibilitet utan att behöva hantera enskilda kluster eller datorer. Du behöver dock fortfarande underhålla den virtuella datorn genom att utföra uppgifter, som att konfigurera, korrigera och underhålla programvaran som körs på den virtuella datorn.

Azure Stack virtuella datorer kan användas på olika sätt. Exempel:

* **Utveckling och testning** – Stack virtuella datorer i Azure ger en snabb och enkelt sätt att skapa en dator med en viss konfiguration krävs för att koda och testa ett program.

* **Program i molnet** – eftersom begäran för ditt program kan variera, kan det göra ekonomiskt meningsfullt att köra på en virtuell dator i Azure-stacken. Du betalar extra för virtuella datorer när du behöver dem och stänger av dem när du inte behöver dem.

* **Utökad datacenter** – virtuella datorer i ett virtuellt nätverk i Azure-stacken enkelt kan vara ansluten till din organisations nätverk eller Azure.

Antalet virtuella datorer som programmet använder kan skalas upp och ned beroende på vilka behov du har.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Vad behöver jag tänka på innan jag skapar en virtuell dator?

Det finns alltid en mängd olika designöverväganden när du skapar ett program i infrastrukturen i Azure-stacken. Följande aspekter av en virtuell dator är viktiga att tänka på innan du börjar:

- Programresursernas namn
- Den virtuella datorns storlek
- Det högsta antalet virtuella datorer som kan skapas
- Operativsystemet som körs på den virtuella datorn
- Konfigurationen av den virtuella datorn när den startats 
- Relaterade resurser som krävs för den virtuella datorn

### <a name="naming"></a>Namngivning

En virtuell dator har ett namn som tilldelats och har ett datornamn som har konfigurerats som en del av operativsystemet. En virtuell dators namn kan bestå av upp till 15 tecken.

Om du använder Azure-stacken för att skapa operativsystemdisk är datornamnet och namnet på virtuella datorn samma. Om du överför och använda en egen avbildning som innehåller en tidigare konfigurerade operativsystem och använda den för att skapa en virtuell dator kan kan namn vara olika. När du överför dina egna avbildningsfil gör datornamnet i operativsystemet och den virtuella datorn namnet samma som bästa praxis.

### <a name="vm-size"></a>Storlek på virtuell dator

Storleken på den virtuella datorn som du använder bestäms av den arbetsbelastning som du vill köra. Storleken som du väljer avgör sedan faktorer som processorkraft, minne och lagringskapacitet. Azure-stacken erbjuder en mängd olika storlekar för att stödja många typer av använder.

### <a name="vm-limits"></a>VM-gränser

Din prenumeration har standard kvotgränser som kan påverka distributionen av många virtuella datorer för projektet. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region.

### <a name="operating-system-disks-and-images"></a>Operativsystemsdiskar och avbildningar

Virtuella datorer använder virtuella hårddiskar (VHD:ar) för att lagra sitt operativsystem (OS) och sin data. VHD:ar används också för de avbildningar du kan välja mellan för att installera ett operativsystem.
Azure-stacken ger en marknadsplats ska användas med olika versioner och typer av operativsystem. Marketplace-avbildningar identifieras av avbildningens utgivare, erbjudande, sku och version (vanligtvis anges versionen som den senaste).

Följande tabell visar några olika sätt att du kan hitta information för en bild:


|Metod|Beskrivning|
|---------|---------|
|Azure Stack-portalen|Värdena anges automatiskt åt dig när du väljer en avbildning som du vill använda.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|REST API:er     |[Lista över avbildningsutgivare](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Lista över avbildningserbjudanden](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Visa bilden SKU: er](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Du kan välja att överföra och använda en egen avbildning. Om du gör används inte utgivarens namn, erbjudandet och sku.

### <a name="extensions"></a>Tillägg

VM-tillägg ger din VM ytterligare funktioner via post distributionskonfiguration och automatiserade åtgärder.
Dessa vanliga uppgifter kan utföras med hjälp av tillägg:

* Köra anpassade skript – kan det tillägget för anpassat skript du konfigurera arbetsbelastningar på den virtuella datorn genom att köra skriptet när den virtuella datorn har etablerats.
* Distribuera och hantera konfigurationer – i PowerShell önskad tillstånd Configuration DSC ()-tillägget kan du ställa in DSC på en virtuell dator för att hantera konfigurationer och miljöer.
* Samla in diagnostikdata – Azure Diagnostics utvidgning hjälper dig att konfigurera den virtuella datorn för att samla in diagnostikdata som kan användas för att övervaka hälsotillståndet för programmet.

### <a name="related-resources"></a>Relaterade resurser

Resurser i följande tabell som används av den virtuella datorn och måste finnas eller skapas när den virtuella datorn skapas.


|Resurs|Krävs|Beskrivning|
|---------|---------|---------|
|Resursgrupp|Ja|Den virtuella datorn måste ingå i en resursgrupp.|
|Lagringskonto|Ja|Den virtuella datorn behöver lagringskontot för att kunna lagra sina virtuella hårddiskar.|
|Virtuellt nätverk|Ja|Den virtuella datorn måste ingå i ett virtuellt nätverk.|
|Offentlig IP-adress|Nej|Den virtuella datorn kan tilldelas en offentlig IP-adress för att möjliggöra fjärråtkomst till den.|
|Nätverksgränssnitt|Ja|Den virtuella datorn behöver nätverksgränssnittet för att kunna kommunicera i nätverket.|
|Datadiskar|Nej|Den virtuella datorn kan innehålla datadiskar för att expandera lagringskapaciteten.|

## <a name="how-do-i-create-my-first-vm"></a>Hur skapar jag min första virtuella dator?

Du har flera alternativ för att skapa en virtuell dator. Ditt val beror på din miljö.
Följande tabell innehåller information för att komma igång med att skapa den virtuella datorn.


|Metod|Artikel|
|---------|---------|
|Azure Stack-portalen|Skapa en virtuell Windows-dator med Azure Stack-portalen<br>[Skapa en Linux-dator med hjälp av Azure Stack-portalen](azure-stack-quick-linux-portal.md)|
|Mallar|Azure Stack-snabbstartsmallar finns på:<br> [https://github.com/Azure/AzureStack-Quickstart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Skapa en Windows-dator med hjälp av PowerShell i Azure-stacken](azure-stack-quick-create-vm-windows-powershell.md)<br>[Skapa en virtuell Linux-dator med hjälp av PowerShell i Azure-stacken](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Skapa en virtuell Windows-dator med hjälp av CLI i Azure-stacken](azure-stack-quick-create-vm-windows-cli.md)<br>[Skapa en virtuell Linux-dator med hjälp av CLI i Azure-stacken](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Hur hanterar jag den virtuella datorn som jag skapat?

Virtuella datorer kan hanteras från en webbläsarbaserad portal, kommandoradsverktyg med skriptstöd eller direkt från API:erna. Några vanliga hanteringsuppgifter som du kan utföra är att få information om en virtuell dator, logga in på en virtuell dator, hantera tillgänglighet och göra säkerhetskopior.

### <a name="get-information-about-a-vm"></a>Hämta information om en virtuell dator

Följande tabell visar några exempel på hur du kan få information om en virtuell dator.


|Metod|Beskrivning|
|---------|---------|
|Azure Stack-portalen|Gå till hubbmenyn, klicka på virtuella datorer och välj sedan den virtuella datorn från listan. På sidan för den virtuella datorn har åtkomst till översiktlig information inställningsvärden och övervakning mått.|
|Azure PowerShell|Hantera virtuella datorer är liknande i Azure och Azure-stacken. Mer information om hur du använder PowerShell finns i avsnittet för följande Azure:<br>[Skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Klient-SDK: er|Använda C# för att hantera virtuella datorer liknar i Azure och Azure-stacken. Mer information finns i följande avsnitt i Azure:<br>[Skapa och hantera virtuella Windows-datorer i Azure med C#](https://docs.microsoft.com/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Du kan använda den **Anslut** knappen i Azure Stack-portalen för att ansluta till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
* [Överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md)

