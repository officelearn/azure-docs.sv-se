---
title: Microsoft Azure Stack Development Kit viktig information | Microsoft Docs
description: Förbättringar, korrigeringar och kända problem med Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: d1ff154c42709f0c672b30f7ec51a436fb44ce13
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724750"
---
# <a name="asdk-release-notes"></a>Viktig information om ASDK 
 
Den här artikeln innehåller information om förbättringar, korrigeringar och kända problem i Azure Stack Development Kit (ASDK). Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](../azure-stack-updates.md#determine-the-current-version).

> Håll dig uppdaterad med Nyheter i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-118110101"></a>Skapa 1.1811.0.101

### <a name="new-features"></a>Nya funktioner

Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack:  

- Det finns en uppsättning nya minsta och rekommenderade maskin- och programvarukrav för ASDK. Dessa nya rekommenderade specifikationer finns dokumenterade i [Azure Stack-distributioner saker att tänka på](asdk-deploy-considerations.md). Azure Stack-plattformen har utvecklats fler tjänster är nu tillgängliga och fler resurser som kan krävas. Specifikationer för ökad återspeglar rekommendationerna reviderade.

### <a name="fixed-issues"></a>Åtgärdade problem

<!-- TBD - IS ASDK --> 
- Ett problem där den offentliga IP-adress mätaren användningsdata visade samma har åtgärdats **EventDateTime** värde för varje post i stället för den **TimeDate** stämpel som visar när posten skapades. Du kan nu använda dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- 3099544 – IS, ASDK --> 
- Ett problem uppstod när du skapar en ny virtuell dator (VM) med hjälp av Azure Stack-portalen har åtgärdats. Välja VM-storlek orsakade kolumnen USD/månad så visas en **ej tillgänglig** meddelande. Den här kolumnen visas inte längre. Visar den virtuella datorn stöds prissättning kolumn inte i Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Ett problem har åtgärdats där administratörsportalen vid åtkomst till information om alla användarprenumeration efter stänga bladet och klicka på **senaste**, fanns inte användaren prenumerationens namn. Användarnamn för prenumerationen visas nu.

<!-- 3060156 - IS ASDK --> 
- Ett problem har åtgärdats i både den administratörs- och analytikerportaler: när du klickar på portalinställningar och välja **ta bort alla inställningar och privata instrumentpaneler** inte fungerar som förväntat och ett felmeddelande visas. 

<!-- 2930799 - IS ASDK --> 
- Ett problem har åtgärdats i både den administratörs- och analytikerportaler: under **alla tjänster**, tillgången **DDoS-skyddsplaner** angavs felaktigt, även om de inte finns i Azure Stack.
 
<!--2760466 – IS  ASDK --> 
- Ett problem uppstod när du har installerat en ny Azure Stack-miljö som har åtgärdats den avisering som anger **aktivering krävs** visas inte. Nu ska visas.

<!--1236441 – IS  ASDK --> 
- Ett problem som förhindrade tillämpning av RBAC-principer till en användargrupp när du använder AD FS har åtgärdats.

<!--3463840 - IS, ASDK --> 
- Problem har åtgärdats med infrastruktur säkerhetskopieringar misslyckas på grund av otillgängliga filservern från det offentliga VIP-nätverket. Den här snabbkorrigeringen flyttar infrastruktur säkerhetskopieringstjänsten tillbaka till nätverkets infrastruktur för offentliga. Om du tillämpar senast [Azure Stack snabbkorrigering för 1809](#azure-stack-hotfixes) som löser problemet, 1811 uppdateringen inte gör några ändringar. 

<!-- 2967387 – IS, ASDK --> 
- Ett problem där visas det konto som du använde för att logga in på portalen för Azure Stack-administratör eller användare som har åtgärdats **Oidentifierad användare**. Det här meddelandet visades när kontot inte har antingen en *första* eller *senaste* namnen.   

<!--  2873083 - IS ASDK --> 
- Ett problem i vilka använda portal för att skapa en virtuell dator scale Sets (VMSS) har åtgärdats i *instansstorlek* listrutan lästes inte in korrekt när du använder Internet Explorer. Den här webbläsaren nu fungerar korrekt.  

<!-- 3190553 - IS ASDK -->
- Ett problem som genererade bort störande aviseringar som tyder på att en infrastruktur-Rollinstans inte var tillgänglig eller skala enhet noden var offline har åtgärdats.

### <a name="known-issues"></a>Kända problem

#### <a name="portal"></a>Portalen  

<!-- 2930820 - IS ASDK --> 
- Om du söker efter ”Docker”, i både den administratörs- och analytikerportaler är returnerade felaktigt objektet. Det är inte tillgängliga i Azure Stack. Om du försöker skapa den, visas ett blad med fel uppgift. 

<!-- 2931230 – IS  ASDK --> 
- Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

<!-- TBD - IS ASDK --> 
- Två administrativa prenumerationstyper som introducerades med version 1804 ska inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **Standard Provider** prenumerationstyp.

<!-- TBD - IS ASDK --> 
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

<!-- TBD - IS ASDK --> 
- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

#### <a name="compute"></a>Compute 

<!-- 3235634 – IS, ASDK -->
- Distribuera virtuella datorer med storlekarna som innehåller en **v2** suffix, till exempel **Standard_A2_v2**, ange suffix som **Standard_A2_v2** (gemener v). Använd inte **Standard_A2_V2** (versaler V). Detta fungerar i globala Azure och är en inkonsekvens i Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- När du använder den [ **Lägg till AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan misslyckas cmdleten med följande fel: *Tidskrävande åtgärden misslyckades med statusen ”misslyckades”*. 

<!--  2795678 – IS, ASDK --> 
- När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. Du kan ignorera varningen där det står: **Du har valt att använda en standardisk med en storlek som har stöd för premiumdiskar. Detta kan påverka operativsystemets prestanda och rekommenderas inte. Överväg att använda premium storage (SSD) i stället.**

<!-- 2967447 - IS, ASDK --> 
- Upplevelse VM scale set (VMSS) när du skapar innehåller 7.2 CentOS-baserade som ett alternativ för distribution. Välj en annan OS för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace av operatorn eftersom avbildningen inte är tillgänglig på Azure Stack.  

<!-- TBD - IS ASDK --> 
- Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

<!-- 1662991 IS ASDK --> 
- Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  

<!-- 2724961- IS ASDK --> 
- När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverat, CPU-procent diagrammet på översiktssidan för virtuell dator inte visar mätvärden.

   Gå till fönstret mått för att hitta mätvärden, till exempel CPU-procent diagrammet för den virtuella datorn, och visar alla stöds Windows VM gäst-mått.

<!-- 3507629 - IS, ASDK --> 
- Hanterade diskar skapar två nya [compute kvottyper](../azure-stack-quota-types.md#compute-quota-types) att begränsa den maximala kapaciteten på hanterade diskar som kan etableras. Som standard tilldelas 2 048 GiB för varje typ av kvot för hanterade diskar. Men du kan stöta på följande problem:

   - Av kvoter som skapats före uppdateringen 1808, visa kvot på Managed Disks 0 värden i administratörsportalen kan även om 2 048 GiB har allokerats. Du kan öka eller minska värdet utifrån dina faktiska behov och den nyligen ange kvotvärde åsidosätter 2048 GiB standard.
   - Om du uppdaterar kvotvärdet till 0, motsvarar det att standardvärdet 2 048 GiB. Som en lösning kan du ange kvotvärdet till 1.

<!-- TBD - IS ASDK --> 
- Efter att ha tillämpat 1811 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   1. Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att **Azure Stack – hanterad Disk** visas.
   2. Om du har konfigurerat en miljö med flera organisationer kan kan distribuera virtuella datorer i en prenumeration som är associerade med en gäst-katalog misslyckas med ett internt felmeddelande. Lös felet genom att följa stegen i [i den här artikeln](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.

#### <a name="networking"></a>Nätverk

<!-- 1766332 - IS ASDK --> 
- Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

<!-- 16309153 - IS ASDK --> 
- I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

<!-- 2529607 - IS ASDK --> 
- Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

<!-- 2664148 - IS ASDK --> 
-   De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 

#### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>Skapa 1.1809.0.90

### <a name="new-features"></a>Nya funktioner
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

<!--  2712869   | IS  ASDK -->  
- **Azure Stack syslog-klienten (allmän tillgänglighet)** den här klienten gör att vidarebefordra granskningar, aviseringar och säkerhetsloggar som rör Azure Stack-infrastruktur till en syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara extern till Azure Stack. Syslog-klienten har nu stöd för att ange den port som syslog-servern lyssnar.

Syslog-klienten är allmänt tillgänglig i den här versionen och den kan användas i produktionsmiljöer.

Mer information finns i [Azure Stack syslog-vidarebefordran](../azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Åtgärdade problem

<!-- TBD - IS ASDK -->
- Minne-diagrammet reporting kostnadsfria/används kapacitet är nu korrekt på portalen. Du kan nu förutsäga hur många virtuella datorer kan du skapa mer tillförlitligt.

<!-- TBD - IS ASDK --> 
- Ett problem där du skapade virtuella datorer på Azure Stack-användarportalen och portalen visas ett felaktigt antal datadiskar som kan ansluta till virtuella datorer i DS-serien har åtgärdats. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- <!-- 2702741 -  IS, ASDK --> Ett problem har åtgärdats i vilka offentliga IP-adresser som har distribuerats med hjälp av dynamisk allokering metoden inte har garanterat bevaras när en frigörandet har utfärdats. De finns kvar.

- <!-- 3078022 - IS, ASDK --> Om en virtuell dator var frigörandet innan 1808 gick det inte att uppdatera allokerade efter 1808 uppdateringen.  Det här problemet löses i 1809. Instanser som fanns i det här tillståndet och kunde inte startas kan startas i 1809 med den här snabbkorrigeringen. Korrigeringen förhindrar också att det här problemet igen.

<!-- TBD -  IS ASDK --> 
- När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

- **Olika korrigeringar** för prestanda, stabilitet, säkerhet och det operativsystem som används av Azure Stack

### <a name="changes"></a>Ändringar

### <a name="known-issues"></a>Kända problem

#### <a name="portal"></a>Portalen  

<!-- 2515955   | IS ,ASDK--> 
- *Alla tjänster* ersätter *fler tjänster* i Azure Stack administratörs- och portaler. Du kan nu använda *alla tjänster* som ett alternativ till att navigera i Azure Stack-portaler på samma sätt som du gör i Azure-portaler.

<!--  TBD – IS, ASDK --> 
- *Basic A* storlekar för virtuella datorer är inte längre tillgängligt för [skapar VM-skalningsuppsättningar](../azure-stack-compute-add-scalesets.md) (VMSS) via portalen. Använd PowerShell eller en mall för att skapa en VMSS med den här storleken.

#### <a name="health-and-monitoring"></a>Hälsa och övervakning

<!-- 1264761 - IS ASDK -->  
- Du kan se aviseringar för den *hälsotillstånd controller* komponent som har följande information:  

   Avisera #1:
   - NAMN:  Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: Varning
   - KOMPONENT: Hälsotillstånd controller
   - DESCRIPTION (BESKRIVNING): Kontrollanten hälsotillstånd pulsslag skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.  

  Avisera #2:
   - NAMN:  Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: Varning
   - KOMPONENT: Hälsotillstånd controller
   - DESCRIPTION (BESKRIVNING): Kontrollanten hälsotillstånd fel skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.

  Båda aviseringarna kan ignoreras och stängs automatiskt över tid.  

<!-- 2368581 - IS. ASDK --> 
- Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.


#### <a name="compute"></a>Compute 

<!-- 3164607 – IS, ASDK -->
- Återansluta en kopplas från disk till samma virtuella-dator (VM) med samma namn och LUN misslyckas med ett fel som **det går inte att koppla data disk 'datadisk' till virtuell dator ”vm1”**. Felet inträffar eftersom disken håller på att frånkopplas eller senaste frånkopplingen misslyckades. Vänta tills disken är helt frånkopplad och sedan försök igen eller ta bort/frånkoppla disken igen. Lösningen är att ansluta den igen med ett annat namn eller på en annan logisk enhet. 

<!-- 3235634 – IS, ASDK -->
- Distribuera virtuella datorer med storlekarna som innehåller en **v2** suffix, till exempel **Standard_A2_v2**, ange suffix som **Standard_A2_v2** (gemener v). Använd inte **Standard_A2_V2** (versaler V). Detta fungerar i globala Azure och är en inkonsekvens i Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- När du skapar en ny virtuell dator (VM) med hjälp av Azure Stack-portalen och du väljer virtuella datorstorlek, kolumnen USD/månad visas med en **ej tillgänglig** meddelande. Den här kolumnen visas inte; Visar den virtuella datorn stöds prissättning kolumn inte i Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- När du använder den [ **Lägg till AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan misslyckas cmdleten med följande fel: *Tidskrävande åtgärden misslyckades med statusen ”misslyckades”*. 

<!--  2966665 – IS, ASDK --> 
- Koppla SSD managed datadiskar till premium storlek disk virtuella datorer (DS, DSv2, Fs, Fs_V2) misslyckas med felmeddelandet:  *Det gick inte att uppdatera diskar för den virtuella datorn 'vmname' fel: Begärt inte kan utföra åtgärden eftersom lagringskontotypen ”Premium_LRS” inte stöds för VM-storleken ”Standard_DS/Ds_V2/FS/Fs_v2)*

   Undvik problemet genom att använda *Standard_LRS* datadiskar i stället för *Premium_LRS diskar*. Användning av *Standard_LRS* datadiskar ändras inte IOPs eller fakturering kostnaden.  

<!--  2795678 – IS, ASDK --> 
- När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. 

   Du kan ignorera varningen där det står: *Du har valt att använda en standardisk med en storlek som har stöd för premiumdiskar. Detta kan påverka operativsystemets prestanda och rekommenderas inte. Överväg att använda premium storage (SSD) i stället.*

<!-- 2967447 - IS, ASDK --> 
- Virtuella datorns skalningsuppsättning (VMSS) skapa upplevelse ger 7.2 CentOS-baserade som ett alternativ för distribution. Eftersom avbildningen inte är tillgänglig på Azure Stack, Välj en annan OS för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace av operatorn.

<!-- TBD -  IS ASDK --> 
- Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.



<!-- TBD -  IS ASDK --> 
- Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

<!-- 1662991 - IS ASDK --> 
- Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.

<!-- 2724961- IS ASDK --> 
- När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverad, kan inte visa måttdata i diagrammet CPU-procent på översiktssidan för virtuell dator.
 
  För att hitta diagrammet CPU-procent för den virtuella datorn, gå till den **mått** gästen mått bladet och visa alla Windows-VM som stöds.

 

#### <a name="networking"></a>Nätverk
<!-- 1766332 - IS, ASDK --> 
- Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

<!-- 16309153 -  IS ASDK --> 
- I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

<!-- 2702741 -  IS ASDK --> 
- Offentliga IP-adresser som distribueras med hjälp av dynamisk fördelning garanteras att bevaras när en frigörandet har utfärdats.

<!-- 2529607 - IS ASDK --> 
- Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

<!-- 2664148 - IS ASDK --> 
- De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

<!-- TBD - IS ASDK --> 
- För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.  


#### <a name="usage"></a>Användning  
<!-- TBD -  IS ASDK --> 
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- #### Identity -->

## <a name="build-11808097"></a>Skapa 1.1808.0.97

### <a name="new-features"></a>Nya funktioner
Den här versionen innehåller följande förbättringar och korrigeringar för Azure Stack.  

<!-- 1658937 | ASDK, IS --> 
- **Starta säkerhetskopieringar på ett fördefinierat schema** – som en enhet, Azure Stack kan nu automatiskt utlösa infrastruktur säkerhetskopieringar med jämna mellanrum för att eliminera mänsklig inblandning. Azure Stack kommer automatiskt att rensa upp den externa resursen för säkerhetskopieringar som är äldre än definierade kvarhållningsperioden. Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Har lagts till dataöverföring tid i den totala tiden för säkerhetskopieringen.** Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **Kapacitet för säkerhetskopiering externa visar nu rätt kapaciteten för den externa resursen.** (Detta var tidigare hårdkoda till 10 GB.) Mer information finns i [Aktivera säkerhetskopiering för Azure Stack med PowerShell](../azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Azure Resource Manager-mallar stöder nu elementet villkor** – du kan nu distribuera en resurs i en Azure Resource Manager-mall med hjälp av ett villkor. Du kan utforma mallen för att distribuera en resurs baserat på ett villkor som kontrollerar om ett parametervärde finns. Information om hur du använder en mall som ett villkor finns i [villkorsstyrd distribution av en resurs](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) och [Variables-avsnittet av Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) i Azure-dokumentationen. 

   Du kan också använda mallar som [distribuera resurser till mer än en prenumeration eller resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **Versionsstöd för Microsoft.Network-API-resursen har uppdaterats** att inkludera stöd för API-versionen 2017-10-01 från 2015-06-15 för Azure Stack-nätverksresurser. Stöd för resurs-versioner mellan 2017-10-01 och 2015-06-15 ingår inte i den här versionen. Se [överväganden för nätverk i Azure Stack](../user/azure-stack-network-differences.md) för funktionen skillnader.

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack har lagt till stöd för omvänd DNS-sökning för utåtriktade Azure Stack-infrastrukturslutpunkter** (som används för portalen, adminportal, hantering och adminmanagement). På så sätt kan Azure Stack extern slutpunkt som ska matchas mot en IP-adress.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack har nu stöd för att lägga till ytterligare nätverksgränssnitt i en befintlig virtuell dator.**  Den här funktionen är tillgänglig med hjälp av portalen, PowerShell och CLI. Mer information finns i [Lägg till eller ta bort nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) i Azure-dokumentationen. 

<!-- 2222444 | IS, ASDK   -->  
- **Förbättringar i Precision och återhämtning har gjorts till nätverk användning taxor**. Mätare för användning av nätverk är nu mer exakta och tar i prenumerationer för kontot som har pausats, avbrott punkter och konkurrenstillstånd.

<!-- 2297790 | IS, ASDK -->  
- **Förbättringar av Azure Stack syslog-klienten (förhandsgranskningsfunktion)**. Den här klienten gör att vidarebefordran av gransknings- och relaterade till Azure Stack-infrastruktur till en syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara externt till Azure Stack. Syslog-klienten stöder nu TCP-protokollet med oformaterad text eller TLS 1.2-kryptering, den senare är standardkonfigurationen. Du kan konfigurera TLS-anslutningen med endast server eller ömsesidig autentisering.

  Använd cmdleten Set-SyslogServer om du vill konfigurera hur syslog-klienten kommunicerar (till exempel protokoll, kryptering och autentisering) med syslog-servern. Denna cmdlet är tillgängligt från den privilegierade slutpunkten (program). 

- <!-- ASDK --> **Galleriobjekt för Virtual Machine Scale Sets är nu inbyggda**.  Virtual Machine Scale Sets galleriobjekt är nu tillgänglig i användar- och analytikerportaler utan att behöva hämta dem. 

- <!-- IS, ASDK --> **Virtual Machine Scale Sets skalning**.  Du kan använda portalen för att [skala en VM-Skalningsuppsättning](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Stöd för anpassade konfigurationer för IPSec/IKE-princip** för [VPN-gatewayer i Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Marketplace-objekt för Kubernetes**. Du kan nu distribuera Kubernetes-kluster med hjälp av den [Kubernetes Marketplace-objekt](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Användare kan det Kubernetes-objekt och fylla i några parametrar för att distribuera ett Kubernetes-kluster i Azure Stack. Syftet med mallarna är att göra det enkelt för användarna att installationsprogrammet utveckling/testning i Kubernetes-distribution med några få steg.

<!-- ####### | IS, ASDK -->  
- **Azure Resource Manager innehåller namnet på regionen.** Med den här versionen innehåller objekt som hämtas från Azure Resource Manager nu namnattributet region. Om ett befintligt PowerShell.skript direkt skickar objektet till en annan cmdlet, kan skriptet producera ett fel och misslyckas. Detta är kompatibla Azure Resource Manager-beteende och kräver den anropande klienten att subtrahera attributet region. För mer information om Azure Resource Manager finns [Azure Resource Manager-dokumentation](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Flytta prenumerationer mellan delegerade providrar.** Du kan nu flytta prenumerationer mellan ny eller befintlig delegerad Provider-prenumerationer som hör till samma Directory-klient. Prenumerationer som hör till Providerprenumeration standard kan också flyttas till Providerprenumerationer delegerad i samma katalog-klient. Mer information finns i [delegera erbjudanden i Azure Stack](../azure-stack-delegated-provider.md).
 
<!-- 2536808 IS ASDK --> 
- **Förbättrad virtuella datorn skapas** för virtuella datorer som skapas med avbildningar som du hämtar från Azure marketplace.

### <a name="fixed-issues"></a>Åtgärdade problem
- <!-- IS ASDK--> Vi har åtgärdat problemet för att skapa en tillgänglighetsuppsättning i portalen som resulterade i gruppen med en feldomän och uppdateringsdomän 1.

<!-- TBD | ASDK, IS --> 
- Olika förbättringar har gjorts till uppdateringsprocessen så att de blir mer tillförlitlig. Dessutom gjorts korrigeringar i underliggande infrastruktur, vilket förbättrar Töm nod, vilket minimerar potentiella driftstopp för arbetsbelastningar under uppdateringen.

<!--2292271 | ASDK, IS --> 
- Vi ett problem har åtgärdats där en ändrad kvotgränsen inte tillämpas för befintliga prenumerationer.  Nu när du ökar en kvot för en nätverksresurs som är en del av ett erbjudande och Plan som är associerade med en användarprenumeration, gäller den nya gränsen för befintliga prenumerationer, samt nya prenumerationer.

<!-- 2448955 | IS ASDK --> 
- Du kan nu har fråga aktivitetsloggar för system som har distribuerats i en UTC + N tidszon.    

<!-- 2319627 |  ASDK, IS --> 
- Förkontroll för konfiguration av säkerhetskopiering parametrar (sökväg/användarnamn/lösenord/krypteringsnyckeln) anger inte längre felaktiga inställningar till konfigurationen för säkerhetskopiering. (Tidigare felaktiga inställningar har angetts i säkerhetskopieringen och säkerhetskopiering skulle misslyckas när den utlöses.)

<!-- 2215948 |  ASDK, IS --> 
- Säkerhetskopieringen i listan uppdaterar nu när du manuellt ta bort säkerhetskopian från den externa resursen.

<!-- 2360715 |  ASDK, IS -->  
- När du har konfigurerat datacenter-integrering kan du inte längre komma åt AD FS-metadatafilen från en resurs. Mer information finns i [om du konfigurerar AD FS-integrationen genom att tillhandahålla federation metadatafil](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Vi har åtgärdat ett problem att förhindras användare från som har tilldelats en befintlig offentlig IP-adress som hade tidigare tilldelats en nätverksgränssnitt eller belastningsutjämnaren till en ny nätverksgränssnitt eller belastningsutjämnare.  

<!-- 2551834 - IS, ASDK --> 
- När du väljer *översikt* för ett lagringskonto på administratören eller användaren portaler, den *Essentials* rutan visar nu den förväntade informationen korrekt. 

<!-- 2551834 - IS, ASDK --> 
- När du väljer *taggar* för ett lagringskonto i administratören eller användaren portaler, visas information om nu korrekt.

<!-- TBD - IS ASDK --> 
- Den här versionen av Azure Stack åtgärdar problem som gjorde att tillämpningen av drivrutinsuppdateringar från OEM-tilläggspaket.

<!-- 2055809- IS ASDK --> 
- Vi har åtgärdat ett problem som inte får du ta bort virtuella datorer från bladet beräkning när den virtuella datorn inte kunde skapas.  

<!--  2643962 IS ASDK -->  
- Avisering för *låg minneskapacitet* inte längre visas inte korrekt.

<!--  TBD ASDK --> 
- Den virtuella dator som värd för behörighet slutpunkten (program) har ökat till 4GB. I ASDK heter den här virtuella datorn AzS-ERCS01.

- <!--  TBD – IS, ASDK --> *Basic A* storlekar för virtuella datorer är inte längre tillgängligt för [skapar VM-skalningsuppsättningar](../azure-stack-compute-add-scalesets.md) (VMSS) via portalen. Använd PowerShell eller en mall för att skapa en VMSS med den här storleken. 

### <a name="known-issues"></a>Kända problem

#### <a name="portal"></a>Portalen  
- <!-- 2967387 – IS, ASDK --> Det konto som används för att logga in på Azure Stack-administratör eller användare portalen visas som **Oidentifierad användare**. Detta inträffar när kontot inte har antingen en *första* eller *senaste* namnen. Undvik problemet genom att redigera användarkontot om du vill använda den första eller sista. Du måste sedan logga ut och logga sedan in igen på portalen. 

-  <!--  2873083 - IS ASDK --> När du använder portalen för att skapa en virtuell datorskalning ange (VMSS), den *instansstorlek* listrutan inte in korrekt när du använder Internet Explorer. Undvik problemet genom att använda en annan webbläsare när du använder portalen för att skapa en VMSS.  

#### <a name="portal"></a>Portalen  
<!-- 2931230 – IS  ASDK --> 
- Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

<!--2760466 – IS  ASDK --> 
- När du installerar en ny Azure Stack-miljö med den här versionen, aviseringen-värde som anger *aktivering krävs* kanske inte visas. [Aktivering](../azure-stack-registration.md) krävs innan du kan använda marketplace syndikering. 

<!-- TBD - IS ASDK --> 
- Två administrativa prenumerationstyper som introducerades med version 1804 ska inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **standard providerprenumeration** typen.

<!-- 2403291 - IS ASDK --> 
- Du kanske inte användning av den vågräta rullningslisten längst ned på administratörs- och portaler. Om du inte kommer åt den vågräta rullningslisten, Använd spåret och navigera till ett tidigare blad i portalen genom att välja namn på bladet du vill visa i dynamiska länkar på listan som finns längst upp till vänster i portalen.
  ![Brödsmula](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

<!-- TBD -  IS ASDK --> 
- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.

<!--  TBD | ASDK -->  
- Standardtidszon för din Azure Stack-distribution ställs nu till UTC. Du kan välja en tidszon när du installerar Azure Stack, men det återgår automatiskt till UTC som standard under installationen.

#### <a name="health-and-monitoring"></a>Hälsa och övervakning
<!-- 1264761 - IS ASDK -->  
- Du kan se aviseringar för den *hälsotillstånd controller* komponent som har följande information:  

   Avisera #1:
   - NAMN:  Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: Varning
   - KOMPONENT: Hälsotillstånd controller
   - DESCRIPTION (BESKRIVNING): Kontrollanten hälsotillstånd pulsslag skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.  

  Avisera #2:
   - NAMN:  Infrastrukturrollen defekt
   - ALLVARLIGHETSGRAD: Varning
   - KOMPONENT: Hälsotillstånd controller
   - DESCRIPTION (BESKRIVNING): Kontrollanten hälsotillstånd fel skannern är inte tillgänglig. Detta kan påverka rapporter om hälsotillstånd och mått.

  Båda aviseringarna kan ignoreras och stängs automatiskt över tid.  

<!-- 2368581 - IS. ASDK --> 
- Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en *fel vid skapande av Fabric VM*, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.

<!-- TBD - IS. ASDK --> 
- När du kör den **Test-AzureStack** cmdlet på privilegierad slutpunkt (program), den **instans för Azure Stack-infrastruktur Rollprestanda** test genererar ett WARN-meddelande för ERCS VM. På ett säkert sätt kan du ignorera WARN-meddelandet och fortsätta att använda ASDK.

#### <a name="compute"></a>Compute
<!-- 2494144 - IS, ASDK --> 
- När du väljer en VM-storlek för en distribution av virtuella datorer, vissa F-seriens storlekar visas inte som en del av storlek Väljaren när du skapar en virtuell dator. Storlek på följande Virtuella datorer visas inte i Väljaren: *F8s_v2*, *F16s_v2*, *F32s_v2*, och *F64s_v2*.  
  Som en lösning kan du använda en av följande metoder för att distribuera en virtuell dator. Du måste ange VM-storlek som du vill använda i varje metod.

- <!-- 3099544 – IS, ASDK --> När du skapar en ny virtuell dator (VM) med hjälp av Azure Stack-portalen och du väljer virtuella datorstorlek, kolumnen USD/månad visas med en **ej tillgänglig** meddelande. Den här kolumnen visas inte; Visar den virtuella datorn stöds prissättning kolumn inte i Azure Stack.

- <!-- 2869209 – IS, ASDK --> När du använder den [ **Lägg till AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan misslyckas cmdleten med följande fel: *Tidskrävande åtgärden misslyckades med statusen ”misslyckades”*. 

- <!--  2966665 – IS, ASDK --> Koppla SSD managed datadiskar till premium storlek disk virtuella datorer (DS, DSv2, Fs, Fs_V2) misslyckas med felmeddelandet:  *Det gick inte att uppdatera diskar för den virtuella datorn 'vmname' fel: Begärt inte kan utföra åtgärden eftersom lagringskontotypen ”Premium_LRS” inte stöds för VM-storleken ”Standard_DS/Ds_V2/FS/Fs_v2)*

   Undvik problemet genom att använda *Standard_LRS* datadiskar i stället för *Premium_LRS diskar*. Användning av *Standard_LRS* datadiskar ändras inte IOPs eller fakturering kostnaden.  

- <!--  2795678 – IS, ASDK --> När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. 

   Du kan ignorera varningen där det står: *Du har valt att använda en standardisk med en storlek som har stöd för premiumdiskar. Detta kan påverka operativsystemets prestanda och rekommenderas inte. Överväg att använda premium storage (SSD) i stället.*

- <!-- 2967447 - IS, ASDK --> Virtuella datorns skalningsuppsättning (VMSS) skapa upplevelse ger 7.2 CentOS-baserade som ett alternativ för distribution. Välj en annan OS för din distribution eller använder en ARM-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace av operatorn eftersom avbildningen inte är tillgänglig på Azure Stack.

<!-- TBD -  IS ASDK --> 
- Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- När du skapar virtuella datorer på Azure Stack-användarportalen visar ett felaktigt antal datadiskar som kan bifogas en D-serien VM i portalen. Alla stöds D-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

<!-- TBD -  IS ASDK --> 
- När en VM-avbildning som inte kan skapas, kan ett misslyckade objekt som du inte kan ta bort läggas till VM-avbildningar compute-bladet.

  Som en lösning kan du skapa en ny VM-avbildning med en dummy virtuell Hårddisk som kan skapas via Hyper-V (New-VHD-sökväg C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda de problem som förhindrar att ta bort objektet som misslyckades. Därefter, 15 minuter när du har skapat den dummy-avbildningen har bort den.

  Du kan sedan försök hämtning av VM-avbildning som tidigare misslyckats.

<!-- TBD -  IS ASDK --> 
- Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

<!-- 1662991 - IS ASDK --> 
- Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.

<!-- 2724961- IS ASDK --> 
- När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverat, översiktssidan VM inte visar mätvärden. 

 

#### <a name="networking"></a>Nätverk
<!-- 1766332 - IS, ASDK --> 
- Under **nätverk**, om du klickar på **skapa VPN-Gateway** att konfigurera en VPN-anslutning **principbaserad** har listats som en VPN-typ. Välj inte det här alternativet. Endast den **Vägbaserad** stöds i Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- Azure Stack stöd för en enda *lokal nätverksgateway* per IP-adress. Detta gäller för alla klient-prenumerationer. Efter skapandet av den första gateway nätverksanslutningen, efterföljande försök att skapa en resurs för gatewayen lokalt nätverk med samma IP-adress blockeras.

<!-- 16309153 -  IS ASDK --> 
- I ett virtuellt nätverk som har skapats med en DNS-Server-inställningarna för *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna skickas inte till virtuella datorer i det virtuella nätverket.

<!-- 2702741 -  IS ASDK --> 
- Offentliga IP-adresser som distribueras med hjälp av dynamisk fördelning garanteras att bevaras när en frigörandet har utfärdats.

<!-- 2529607 - IS ASDK --> 
- Under Azure Stack *hemlighet Rotation*, där offentliga IP-adresser inte kan nås i två till fem minuter på en stund.

<!-- 2664148 - IS ASDK --> 
- De kan stöta på ett scenario där anslutningsförsök misslyckas om det lokala undernätet har lagts till i den lokala nätverksgateway när gatewayen har redan skapats i scenarier där klienten har åtkomst till sina virtuella datorer med hjälp av en S2S VPN-tunnel. 


#### <a name="sql-and-mysql"></a>SQL- och MySQL
<!-- TBD - ASDK --> 
- Databasen som är värd för servrar måste vara reserverad för användning av resurs-providern och användaren arbetsbelastningar. Du kan inte använda en instans som används av någon annan konsument, inklusive App Services.

<!-- IS, ASDK --> 
- Specialtecken, inklusive blanksteg och punkter, stöds inte i den **familj** namn när du skapar en SKU för SQL- och MySQL-resursprovider.

#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.

<!-- TBD - IS ASDK --> 
- För att skala ut infrastruktur (arbetare, hantering, frontend-roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.  

<!-- TBD - IS ASDK --> 
- App Service kan bara distribueras till den *standard providerprenumeration* just nu.

#### <a name="usage"></a>Användning  
<!-- TBD -  IS ASDK --> 
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.

<!-- #### Identity -->

