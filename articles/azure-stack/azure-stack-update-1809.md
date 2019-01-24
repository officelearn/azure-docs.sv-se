---
title: Uppdatering av Azure Stack 1809 | Microsoft Docs
description: Läs mer om nyheter i uppdateringen 1809 för integrerade Azure Stack-system, inklusive kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: a5afcbb219d7792325faa03c5319b07a7c68732b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850512"
---
# <a name="azure-stack-1809-update"></a>Uppdatering av Azure Stack 1809

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskriver innehållet i 1809 uppdateringspaketet. Uppdateringspaketet innehåller förbättringar, korrigeringar och kända problem för den här versionen av Azure Stack. Den här artikeln innehåller också en länk så att du kan ladda ned uppdateringen. Kända problem är indelade i problem direkt relaterade till uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]  
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure Stack 1809 update build-nummer är **1.1809.0.90**.  

### <a name="new-features"></a>Nya funktioner

Den här uppdateringen innehåller följande förbättringar för Azure Stack:

- Den här versionen har integrerade Azure Stack-system har stöd för konfigurationer av 4 – 16 noder. Du kan använda den [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) till hjälp i planeringen för Azure Stack-kapacitet och konfiguration.

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog-klienten (allmän tillgänglighet)** den här klienten gör att vidarebefordra granskningar, aviseringar och säkerhetsloggar som rör Azure Stack-infrastruktur till en syslog-server eller säkerhet och händelsehantering (SIEM) hanteringsprogramvara extern till Azure Stack. Syslog-klienten har nu stöd för att ange den port som syslog-servern lyssnar.

   Syslog-klienten är allmänt tillgänglig i den här versionen och den kan användas i produktionsmiljöer.

   Mer information finns i [Azure Stack syslog-vidarebefordran](azure-stack-integrate-security.md).

- Du kan nu [flytta resursen registrering](azure-stack-registration.md#move-a-registration-resource) i Azure mellan resursgrupper utan att behöva registrera igen. Cloud Solution Providers (CSP) kan också flytta resursen registrering prenumerationer så länge som både nya och gamla prenumerationer mappas till samma CSP-partner-ID. Detta påverkar inte befintliga kund klient mappningar. 

- Stöd har lagts till för att tilldela flera IP-adresser per nätverksgränssnitt.  Mer information finns i [tilldela flera IP-adresser till virtuella datorer med hjälp av PowerShell](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Åtgärdade problem

<!-- TBD - IS ASDK -->
- Minne-diagrammet reporting kostnadsfria/används kapacitet är nu korrekt på portalen. Du kan nu förutsäga hur många virtuella datorer kan du skapa mer tillförlitligt.

<!-- TBD - IS ASDK --> 
- Ett problem där du skapade virtuella datorer på Azure Stack-användarportalen och portalen visas ett felaktigt antal datadiskar som kan ansluta till virtuella datorer i DS-serien har åtgärdats. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- Följande problem för hanterad disk har korrigerats i 1809 och korrigeras också i 1808 [Azure Stack-snabbkorrigeringen 1.1808.9.117](https://support.microsoft.com/help/4481066/): 

   <!--  2966665 – IS, ASDK --> 
   - Åtgärdat problemet i vilka kopplar SSD datadiskar till premium-storlek som hanterad disk virtuella datorer (DS, DSv2, Fs, Fs_V2) misslyckades med ett fel:  *Det gick inte att uppdatera diskar för den virtuella datorn 'vmname' fel: Begärt inte kan utföra åtgärden eftersom lagringskontotypen ”Premium_LRS” inte stöds för VM-storleken ”Standard_DS/Ds_V2/FS/Fs_v2)*. 
   
   - Skapa en hanterad disk VM med hjälp av **createOption**: **Bifoga** misslyckas med följande fel: *Tidskrävande åtgärden misslyckades med statusen ”misslyckades”. Ytterligare information: ”ett internt körningsfel inträffade”.*
   Felkod: InternalExecutionError ErrorMessage: Ett internt körningsfel har inträffat.
   
   Det här problemet har nu åtgärdats.

- <!-- 2702741 -  IS, ASDK --> Ett problem har åtgärdats i vilka offentliga IP-adresser som har distribuerats med hjälp av dynamisk allokering metoden inte har garanterat bevaras när en frigörandet har utfärdats. De finns kvar.

- <!-- 3078022 - IS, ASDK --> Om en virtuell dator var frigörandet innan 1808 gick det inte att uppdatera allokerade efter 1808 uppdateringen.  Det här problemet löses i 1809. Instanser som fanns i det här tillståndet och kunde inte startas kan startas i 1809 med den här snabbkorrigeringen. Korrigeringen förhindrar också att det här problemet igen.

### <a name="changes"></a>Ändringar

<!-- 2635202 - IS, ASDK -->
- Infrastruktur-säkerhetskopieringstjänsten flyttas från den [infrastruktur för offentliga nätverk](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-infrastructure-network) till den [offentliga VIP-nätverket](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network). Kunder måste se till att tjänsten har åtkomst till lagringsplatsen för säkerhetskopian från det offentliga VIP-nätverket.  

> [!IMPORTANT]  
> Om du har en brandvägg som inte tillåter anslutningar från det offentliga VIP-nätverket till filservern innebär ändringen infrastruktur säkerhetskopieringar att misslyckas med ”fel 53 inte gick att hitta nätverkssökvägen”. Det här är en viktig ändring som har ingen rimlig lösning. Baserat på feedback från kunder, återgår Microsoft den här ändringen i en snabbkorrigering. Granska den [efter uppdatering i avsnittet steg](#post-update-steps) för mer information om tillgängliga snabbkorrigeringar för 1809. När snabbkorrigeringen är tillgänglig, se till att använda den när du har uppdaterat till 1809 endast om din nätverksprinciper inte tillåter det offentliga VIP-nätverket för åtkomst till resurser i infrastrukturen. i 1811, kommer den här ändringen tillämpas på alla system. Om du har installerat snabbkorrigeringen i 1809, det finns ingen ytterligare åtgärd krävs.  

### <a name="common-vulnerabilities-and-exposures"></a>Vanliga säkerhetsproblem och exponeringar

Den här uppdateringen installeras följande säkerhetsuppdateringar:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Klicka på föregående länkarna för mer information om dessa säkerhetsrisker eller finns i Microsoft Knowledge Base-artiklar [4457131](https://support.microsoft.com/help/4457131) och [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Förutsättningar

- Installera den senaste Azure Stack snabbkorrigering för 1808 innan du tillämpar 1809. Mer information finns i [KB 4481066 – Azure Stack snabbkorrigering Azure Stack snabbkorrigering 1.1808.9.117](https://support.microsoft.com/help/4481066/). Microsoft rekommenderar att den senaste snabbkorrigeringen tillgänglig, är den minimun-versionen som krävs för att installera 1809 1.1808.5.110.

  > [!TIP]  
  > Prenumerera på följande *RRS* eller *Atom* flöden, hålla jämna steg med Azure Stack snabbkorrigeringar:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- När du kör [Test AzureStack](azure-stack-diagnostic-test.md) efter 1809 uppdateringen visas ett varningsmeddelande från den Hanteringsstyrenheten för baskort (BMC). Du kan ignorera den här varningen.

- <!-- 2468613 - IS --> Under installationen av den här uppdateringen kan du se aviseringar med rubriken *fel – mall för FaultType UserAccounts.New saknas.*  Du kan ignorera dessa aviseringar. De här aviseringarna stängs automatiskt när installationen av uppdateringen har slutförts.

- <!-- 2489559 - IS --> Försök inte att skapa virtuella datorer under installationen av uppdateringen. Mer information om hur du hanterar uppdateringar finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Om du har använt en uppdatering till Azure Stack från din OEM, den **uppdatering** meddelande visas inte i Azure Stack-administratörsportalen. Om du vill installera Microsoft update, hämta och importera den manuellt med hjälp av anvisningarna här [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Steg efter uppdateringen

> [!Important]  
> Förbereda distributionen av Azure Stack för tillägget värd som aktiveras av nästa uppdateringspaketet. Förbered datorn med hjälp av nedanstående procedur, [förbereda för tillägget för värd för Azure Stack](azure-stack-extension-host-prepare.md).

Installera alla tillämpliga snabbkorrigeringar efter installationen av uppdateringen. Visa mer information i följande artiklar i kunskapsbasen, samt våra [Servicing princip](azure-stack-servicing-policy.md).  
- [KB 4481548 – Azure Stack snabbkorrigering Azure Stack snabbkorrigering 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Här följer efter installation kända problem för den här build-versionen.

### <a name="portal"></a>Portalen

- Den tekniska dokumentationen för Azure Stack fokuserar på den senaste versionen. På grund av portalen ändringar mellan versioner vad som visas när du använder Azure Stack-portalerna kan skilja sig från vad som visas i dokumentationen.

<!-- 2930718 - IS ASDK --> 
- I administratörsportalen kan få åtkomst till information om alla användarprenumeration efter stänga bladet och klicka på **senaste**, användarnamn för prenumeration visas inte.

<!-- 3060156 - IS ASDK --> 
- I både den administratörs- och portaler, klickar på portalinställningar och välja **ta bort alla inställningar och privata instrumentpaneler** fungerar inte som förväntat. En felmeddelandet visas. 

<!-- 2930799 - IS ASDK --> 
- I både den administratörs- och analytikerportaler under **alla tjänster**, tillgången **DDoS-skyddsplaner** felaktigt visas. Det är inte tillgängliga i Azure Stack. Om du försöker skapa den visas ett felmeddelande om att portalen inte kunde skapa marketplace-objekt. 

<!-- 2930820 - IS ASDK --> 
- Om du söker efter ”Docker”, i både den administratörs- och analytikerportaler är returnerade felaktigt objektet. Det är inte tillgängliga i Azure Stack. Om du försöker skapa den, visas ett blad med fel uppgift. 

<!-- 2967387 – IS, ASDK --> 
- Det konto som används för att logga in på Azure Stack-administratör eller användare portalen visas som **Oidentifierad användare**. Det här meddelandet visas när kontot inte har antingen en *första* eller *senaste* namnen. Undvik problemet genom att redigera användarkontot om du vill använda den första eller sista. Du måste sedan logga ut och logga sedan in igen på portalen.  

<!--  2873083 - IS ASDK --> 
-  När du använder portalen för att skapa en virtuell datorskalning ange (VMSS), den *instansstorlek* listrutan inte in korrekt när du använder Internet Explorer. Undvik problemet genom att använda en annan webbläsare när du använder portalen för att skapa en VMSS.  

<!-- 2931230 – IS  ASDK --> 
- Planer som läggs till i en användarprenumeration som en tilläggsplanen kan inte raderas även när du tar bort planen från användarprenumerationen. Planen finns kvar tills de prenumerationer som refererar till tilläggsplanen tas också bort. 

<!--2760466 – IS  ASDK --> 
- När du installerar en ny Azure Stack-miljö med den här versionen, aviseringen-värde som anger *aktivering krävs* kanske inte visas. [Aktivering](azure-stack-registration.md) krävs innan du kan använda marketplace syndikering.  

<!-- TBD - IS ASDK --> 
- Två administrativa prenumerationstyper som introducerades med version 1804 ska inte användas. Typerna av prenumeration är **Avläsning av prenumeration**, och **förbrukning prenumeration**. Dessa typer av prenumerationer visas i den nya Azure Stack miljöer från och med version 1804 men ännu inte är redo att användas. Du bör fortsätta att använda den **Standard Provider** prenumerationstyp.

<!-- TBD - IS ASDK --> 
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.

<!-- TBD - IS ASDK --> 
- Du kan inte visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan du använda PowerShell för att kontrollera behörigheterna.


### <a name="health-and-monitoring"></a>Hälsa och övervakning

<!-- TBD - IS -->
- Du kan se följande aviseringar visas flera gånger och sedan försvinner på Azure Stack-system:
   - *Infrastruktur-rollinstans som är inte tillgänglig*
   - *Skala enhet noden är offline*
   
  Kör den [Test AzureStack](azure-stack-diagnostic-test.md) cmdlet för att kontrollera hälsotillståndet för rollinstanser för infrastruktur och skala enhet noder. Om inga problem har identifierats av [Test-AzureStack](azure-stack-diagnostic-test.md), du kan ignorera dessa aviseringar. Om ett problem har identifierats, kan du försöker starta rollinstansen infrastruktur eller nod med hjälp av administrationsportalen eller PowerShell.

  Det här problemet löses i senast [1809 snabbkorrigering versionen](https://support.microsoft.com/help/4481548/), så var noga med att installera snabbkorrigeringen om du upplever problemet. 

<!-- 1264761 - IS ASDK -->  
- Du kan se aviseringar för den **hälsotillstånd controller** komponent som har följande information:  

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

  Båda aviseringarna kan ignoreras och de stängs automatiskt över tid.  


<!-- 2812138 | IS --> 
- Du kan se en avisering för den **Storage** komponent som innehåller följande information:

   - NAMN: Internt kommunikationsfel vid lagring  
   - ALLVARLIGHETSGRAD: Kritisk  
   - KOMPONENT: Storage  
   - DESCRIPTION (BESKRIVNING): Storage-tjänsten internt kommunikationsfel inträffade när begäranden skickas till följande noder.  

    Aviseringen kan ignoreras, men du måste stänga aviseringen manuellt.

<!-- 2368581 - IS, ASDK --> 
- Azure Stack-operatör, om du får en avisering om ont om minne och virtuella datorer inte att distribueras med en **fel vid skapande av Fabric VM**, är det möjligt att Azure Stack-stämpel har tillräckligt med tillgängligt minne. Använd den [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) så att den tillgängliga kapaciteten för dina arbetsbelastningar.

### <a name="compute"></a>Compute

- När du skapar en [Dv2-serien VM](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), D11 14v2 virtuella datorer kan du skapa 4, 8, 16 och 32 datadiskar respektive. Skapa VM-fönstret visas dock 8, 16, 32 och 64 datadiskar.

<!-- 3235634 – IS, ASDK -->
- Distribuera virtuella datorer med storlekarna som innehåller en **v2** suffix, till exempel **Standard_A2_v2**, ange suffix som **Standard_A2_v2** (gemener v). Använd inte **Standard_A2_V2** (versaler V). Detta fungerar i globala Azure och är en inkonsekvens i Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- När du skapar en ny virtuell dator (VM) med hjälp av Azure Stack-portalen och du väljer virtuella datorstorlek, kolumnen USD/månad visas med en **ej tillgänglig** meddelande. Den här kolumnen visas inte; Visar den virtuella datorn stöds prissättning kolumn inte i Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- När du använder den [ **Lägg till AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), måste du använda den **- OsUri** parameter som lagringskontot URI där disken har laddats upp. Om du använder den lokala sökvägen på disken kan misslyckas cmdleten med följande fel: *Tidskrävande åtgärden misslyckades med statusen ”misslyckades”*. 

<!--  2795678 – IS, ASDK --> 
- När du använder portalen för att skapa virtuella datorer (VM) i en premium VM-storlek (DS, Ds_v2, FS, FSv2), skapas den virtuella datorn i ett standardlagringskonto. Du skapar i ett standardlagringskonto påverkar inte samma funktioner, IOPs, eller fakturering. 

   Du kan ignorera varningen där det står: *Du har valt att använda en standardisk med en storlek som har stöd för premiumdiskar. Detta kan påverka operativsystemets prestanda och rekommenderas inte. Överväg att använda premium storage (SSD) i stället.*

<!-- 2967447 - IS, ASDK --> 
- Upplevelse VM scale set (VMSS) när du skapar innehåller 7.2 CentOS-baserade som ett alternativ för distribution. Välj en annan OS för din distribution eller använder en Azure Resource Manager-mall som anger en annan CentOS-avbildning som har hämtats innan den distribueras från marketplace av operatorn eftersom avbildningen inte är tillgänglig på Azure Stack.  

<!-- 2724873 - IS --> 
- När du använder PowerShell-cmdlets **Start AzsScaleUnitNode** eller **Stop-AzsScaleunitNode** för att hantera skalningsenheter, det första försöket att starta eller stoppa skalningsenheten kan misslyckas. Om cmdleten misslyckas på den första körningen, kör du cmdlet en gång. Den andra körningen bör fungera för att slutföra åtgärden. 

<!-- TBD - IS ASDK --> 
- Om det tar för lång tid att etablera ett tillägg på en VM-distribution, bör användarna låta etablering timeout-värde istället för att försöka stoppa processen för att frigöra eller ta bort den virtuella datorn.  

<!-- 1662991 IS ASDK --> 
- Linux VM-diagnostik stöds inte i Azure Stack. Distributionen misslyckas när du distribuerar en Linux VM med VM-diagnostik aktiverat. Distributionen misslyckas också om du aktiverar den grundläggande Linux VM-mätvärden via diagnostikinställningar.  

<!-- 2724961- IS ASDK --> 
- När du registrerar den **Microsoft.Insight** resursprovidern i inställningarna för prenumeration och skapa en virtuell Windows-dator med Guest OS diagnostiska aktiverat, CPU-procent diagrammet på översiktssidan för virtuell dator inte visar mätvärden.

   Gå till fönstret mått för att hitta mätvärden, till exempel CPU-procent diagrammet för den virtuella datorn, och visar alla stöds Windows VM gäst-mått.

<!-- 3507629 - IS, ASDK --> 
- Hanterade diskar skapar två nya [compute kvottyper](azure-stack-quota-types.md#compute-quota-types) att begränsa den maximala kapaciteten på hanterade diskar som kan etableras. Som standard tilldelas 2 048 GiB för varje typ av kvot för hanterade diskar. Men du kan stöta på följande problem:

   - Av kvoter som skapats före uppdateringen 1808, visa kvot på Managed Disks 0 värden i administratörsportalen kan även om 2 048 GiB har allokerats. Du kan öka eller minska värdet utifrån dina faktiska behov och den nyligen ange kvotvärde åsidosätter 2048 GiB standard.
   - Om du uppdaterar kvotvärdet till 0, motsvarar det att standardvärdet 2 048 GiB. Som en lösning kan du ange kvotvärdet till 1.

<!-- TBD - IS ASDK --> 
- Efter att ha tillämpat 1809 uppdatera, följande problem kan uppstå när du distribuerar virtuella datorer med hanterade diskar:

   - Om prenumerationen har skapats innan uppdateringen gjordes 1808, distribution av virtuella datorer med Managed Disks kan misslyckas med felmeddelandet internt. Följ dessa steg för varje prenumeration för att lösa problemet:
      1. I klient-portalen går du till **prenumerationer** och hitta prenumerationen. Klicka på **Resursprovidrar**, klicka sedan på **Microsoft.Compute**, och klicka sedan på **Omregistrera**.
      2. Under samma prenumeration, gå till **åtkomstkontroll (IAM)**, och kontrollera att den **AzureStack-DiskRP-klient** roll visas.
   2. Om du har konfigurerat en miljö med flera organisationer kan kan distribuera virtuella datorer i en prenumeration som är associerade med en gäst-katalog misslyckas med ett internt felmeddelande. Lös felet genom att följa stegen i [i den här artikeln](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) att konfigurera om var och en av dina gäst-kataloger.

- En dator med Ubuntu 18.04 skapas med SSH-auktorisering aktiverat kan inte du använda SSH-nycklar för att logga in. Som en lösning kan du använda för åtkomst till virtuell dator för Linux-tillägget för att implementera SSH-nycklar när du har etablerat eller använder lösenordsbaserad autentisering.

### <a name="networking"></a>Nätverk  

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


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Användarna måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.


### <a name="usage"></a>Användning  

<!-- TBD - IS ASDK --> 
- Offentliga IP-adress mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* stämpel som visar när posten skapades. För närvarande kan använda du inte dessa data för att utföra redovisas korrekt användning av offentlig IP-adress.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned Azure Stack 1809 uppdateringspaketet från [här](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Nästa steg

- Underhåll principen för integrerade Azure Stack-system och vad du måste göra för att behålla ditt system i ett läge som stöds finns i [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).  
- Om du vill använda detta privilegierad slutpunkt (program) för att övervaka och återuppta uppdateringar, se [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).  
- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).  
- Mer information om hur du tillämpar uppdateringar med Azure Stack finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).  
