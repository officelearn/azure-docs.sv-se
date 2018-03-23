---
title: Azure-stacken 1711 Update | Microsoft Docs
description: Lär dig mer om vad som finns i 1711 uppdateringen för Azure-stacken integrerat system, kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fd57699a329fbccdbefc73dae7d473070cd831ea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-1711-update"></a>Azure-stacken 1711 uppdatering

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i det här uppdateringspaketet kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i kända problem direkt relaterad till uppdateringen och kända problem med build (efter installationen).

> [!IMPORTANT]
> Det här uppdateringspaketet gäller endast för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure-stacken 1711 uppdatera versionsnumret är **171201.3**.

## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Förutsättningar

- Du måste först installera Azure-stacken [1710 uppdatera](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) innan du installerar den här uppdateringen.

- Granska användning av **CloudAdmin** uppdatera 1711 som kontonamn innan du installerar. Från och med version 1711, *CloudAdmin* är ett reserverat kontonamn och inte bör anges manuellt. När du uppdaterar till version 1711 uppdateringen tar du bort befintliga instanser av konto för distribution (kallas vanligtvis AzureStackAdmin). Om du med namnet konto för distribution *CloudAdmin*, uppdatering till 1711 tar bort den. 

  *CloudAdmin* är det inbyggda kontot att ansluta till den [ *Privilegierade endpoint* ](azure-stack-privileged-endpoint.md) (program). Borttagningen av det här kontot kan resultera i en utelåsning av detta program om det finns redan ett annat användarkonto som är medlem i gruppen CloudAdmin. 

  Om du använde CloudAdmin som namnet på kontot för distribution, lägga till en ny CloudAdmin användare ditt program innan du startar uppdateringen 1711 att undvika att låsas slut på stackutrymme Azure. Om du vill lägga till en ny CloudAdmin användare, kör du cmdlet **ny CloudAdminUser** på detta program.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Den här uppdateringen innehåller följande förbättringar och korrigeringar för Azure-stacken.

#### <a name="new-features"></a>Nya funktioner

- Stöd för publicerar lösningsmallar
- Uppdateringar i Azure-stacken diagram loggnings- och felhantering
- Möjligheten att aktivera eller inaktivera värdar
- Användare kan nu aktivera virtuella Windows-datorer automatiskt
- Tillagda Privilegierade endpoint PowerShell-cmdlet för att hämta BitLocker återställningsnycklar för bevarande
- Stöd för att uppdatera offlineavbildningar vid uppdatering av infrastruktur
- Aktivera infrastrukturen för säkerhetskopiering med tjänsten Aktivera säkerhetskopiering

#### <a name="fixes"></a>Korrigeringar

- Fast konkurrenstillstånd i DNS under fältet utbytbara enhet (FRU) och även uppdaterade klustret loggning
- Korrigering för omstart möjligheten för inaktivera-värden i fältet utbytbara enhet (FRU)
- Olika andra prestanda, säkerhet och stabilitet korrigeringar

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 nya funktioner och korrigeringar

- [14 november 2017 – KB4048953 (OS-version 14393.1884) ](https://support.microsoft.com/help/4048953)

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

Det här avsnittet innehåller kända problem som kan uppstå under installationen av uppdateringen 1711.


1. **Symptom:** Azure Stack kan se följande fel under uppdateringen: *”name: installera uppdateringen”., ”beskrivning”: ”installera uppdateringen på värdar och Infra virtuella datorer”., ”errorMessage” ”: Skriv LiveUpdate om du av rollen ' Virtuella datorer utlöses ett undantag: \n\nThere är inte tillräckligt med utrymme på disk.\n\nat <ScriptBlock>, <No file>: line22 ”,” status ”:” Error ”,” startTimeUtc ””: 2017-11-10T16:46:59.123Z ”,” endTimeUtc ””: 2017-11-10T19:20:29.669Z ”,” steg ”: []”*
    2. **Orsak:** problemet beror på brist på ledigt diskutrymme på en eller flera virtuella datorer som är en del av Azure Stack-infrastrukturen
    3. **Lösning:** kontakta Microsofts kundservice och Support (CSS) för att få hjälp.
<br><br>
2. **Symptom:** Azure Stack kan se följande fel under uppdateringen:*undantag som anropar ”ExtractToFile” med ”3” argument ”: processen kan inte komma åt filen ' <\\<machineName>-ERCS01\C$ \ Programmet Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll >'*
    1. **Orsak:** problemet orsakas när återupptar en uppdatering från portalen som tidigare var återupptas med Privilegierade slutpunkt (program).
    2. **Lösning:** kontakta Microsofts kundservice och Support (CSS) för att få hjälp.
<br><br>
3. **Symptom:** Azure Stack kan se följande fel under uppdateringen:*”typ 'CheckHealth' av rollen 'VirtualMachines” utlöses ett undantag: \n\nVirtual datorn hälsokontrollen för <machineName>-ACS01 som producerade den Följande fel. \nThere gick inte att hämta information om Virtuellt från värdar. Undantag information: \nGet-VM: åtgärden på datorn ”Node03' misslyckades: det WS-Management-tjänsten kan inte behandla begäran. WMI-\nservice eller WMI-providern returnerade ett okänt fel: HRESULT 0x8004106c ”.*
    1. **Orsak:** problemet orsakas av ett problem i Windows Server som är avsedd att åtgärdas i efterföljande uppdateringar för Windows server.
    2. **Lösning:** kontakta Microsofts kundservice och Support (CSS) för att få hjälp.
<br><br>
4. **Symptom:** Azure Stack kan se följande fel under uppdateringen:*”typ” DefenderUpdate' av rollen 'URP' utlöste ett undantagsfel: misslyckade komma version från \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{filnamn} .exe efter 60 försök att kopiera-AzSDefenderFiles C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: rad 262 ”*
    1. **Orsak:** problemet orsakas av en misslyckad eller ofullständig bakgrund hämtning av definitionsuppdateringar för Windows Defender.
    2. **Lösning:** Kontrollera försöker fortsätta uppdateringen när upp till 8 timmar har gått sedan den första uppdateringen försök.

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Det här avsnittet innehåller efter installationen kända problem med build **20171201.3**.

#### <a name="portal"></a>Portalen

- Det kanske inte går att visa beräkning eller lagring resurser i administratörsportalen. Detta anger att ett fel inträffade under installationen av uppdateringen och att uppdateringen felaktigt rapporterades som slutförd. Om det här problemet inträffar kan du kontakta Microsoft CSS om hjälp.
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.
- När du visar egenskaperna för en resursgrupp i **flytta** är inaktiverat. Det är förväntat. Flytta resursgrupper mellan prenumerationer stöds inte för närvarande.
- För alla arbetsflöden där du väljer en prenumeration, resursgrupp eller plats i en listrutan, kan en eller flera av följande problem uppstå:

   - Du kan se en tom rad längst upp i listan. Du måste fortfarande kunna markera ett objekt som förväntat.
   - Om listan över objekt i den nedrullningsbara listrutan är kort kan kanske du inte visa något av objektnamn.
   - Om du har flera prenumerationer för användaren, får den nedrullningsbara resurslistan vara tom.

        > [!NOTE]
        > Du kan skriva namnet på den prenumeration eller resursgrupp (om du vet att den) Undvik de två sista problem, eller kan du använda PowerShell i stället.

- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.
- Det går inte att visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan kontrollera du behörigheterna med hjälp av PowerShell.

#### <a name="health-and-monitoring"></a>Hälsa och övervakning

- Om du startar om en infrastruktur för rollinstansen får ett meddelande som anger att omstarten misslyckades. Men omstarten faktiskt lyckades.

#### <a name="marketplace"></a>Marketplace
- När du försöker lägga till objekt i stacken för Azure marketplace med hjälp av den **Lägg till från Azure** alternativet inte alla objekt kan vara synlig för hämtning.
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.

#### <a name="compute"></a>Compute
- Användare möjlighet att skapa en virtuell dator med geo-redundant lagring. Den här konfigurationen gör att skapa en virtuell dator misslyckas.
- Du kan konfigurera en virtuell dator tillgänglighetsuppsättning endast med en feldomän på en och en uppdateringsdomän i en.
- Det finns inga marketplace-upplevelse för att skapa virtuella datorer. Du kan skapa en skala som anges med hjälp av en mall.
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

#### <a name="networking"></a>Nätverk
- Du kan inte skapa en belastningsutjämnare med en offentlig IP-adress med hjälp av portalen. Som en tillfällig lösning kan använda du PowerShell för att skapa belastningsutjämnaren.
- När du skapar en Utjämning av nätverksbelastning måste du skapa en regel för network adress translation (NAT). Om du inte får ett fel när du försöker lägga till en NAT-regel när belastningsutjämnaren har skapats.
- Du kan koppla en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med den IP-adressen. Disassociation visas ska fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny. För närvarande måste du bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.
- Azure Stack-operatörer kanske inte kan distribuera, ta bort, ändra Vnet eller Nätverkssäkerhetsgrupper. Det här problemet visas främst på efterföljande update försök av samma paket. Detta beror på ett paketering problem med en uppdatering som för närvarande är under undersökningen.
- Internt Load Balancing (ILB) hanterar felaktigt MAC-adresser för backend-virtuella datorer som bryter Linux-instanser.

#### <a name="sqlmysql"></a>SQL/MySQL
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU.
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

#### <a name="identity"></a>Identitet

I Azure Active Directory Federation Services (ADFS) distribuerade miljöer, den **azurestack\azurestackadmin** inte längre är standard providern Prenumerationens ägare. I stället för att logga in på den **administrationsportalen / adminmanagement endpoint** med den **azurestack\azurestackadmin**, du kan använda den **azurestack\cloudadmin** kontot, så att hantera och använda standard providern prenumerationen.

> [!IMPORTANT]
> Även om den **azurestack\cloudadmin** konto är ägare till prenumerationen Standard Provider i ADFS distribuerade miljöer, har inte behörighet att RDP till värden. Fortsätta att använda den **azurestack\azurestackadmin** konto eller det lokala administratörskontot för att logga in, komma åt och hantera värden efter behov.

#### <a name="infrastructure-backup-sevice"></a>Infrastruktur för säkerhetskopiering katalogtjänsten
<!-- 1974890-->

- **Pre-1711 säkerhetskopior stöds inte för återställning i molnet.**  
  Pre-1711 säkerhetskopior är inte kompatibla med recovery för molnet. Du måste uppdatera till 1711 först och aktiverar säkerhetskopiering. Om du redan har aktiverat säkerhetskopiering måste du gör en säkerhetskopia när du har uppdaterat till 1711. Pre-1711 säkerhetskopior bör tas bort.

- **Aktiverar infrastruktur för säkerhetskopiering på ASDK är endast avsedd för testning.**  
  Infrastruktur säkerhetskopieringar kan användas för att återställa med flera noder lösningar. Du kan aktivera infrastrukturen för säkerhetskopiering på ASDK men det går inte att testa återställning.

Mer information finns i [säkerhetskopia och återställa data för Azure-stacken med Backup-tjänsten infrastruktur](azure-stack-backup-infrastructure-backup.md).

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned uppdateringspaketet Azure Stack 1711 från [här](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Mer information

Microsoft tillhandahåller ett sätt att övervaka och återuppta uppdateringar med hjälp av detta Privilegierade slutpunkt (program) installeras med uppdatering 1711.

- Finns det [övervaka uppdateringar i Azure-stacken använder Privilegierade endpoint-dokumentationen](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Se också

- Se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md) en översikt över uppdateringshantering i Azure-stacken.
- Se [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md) för mer information om hur du installerar uppdateringar med Azure-stacken.
