---
title: Viktig information för Microsoft Azure-stacken Development Kit | Microsoft Docs
description: Förbättringar av korrigeringar och kända problem med Azure-stacken Development Kit.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 1a8cbdef8f3d8a5aa4aeab0e51275933160360c2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure-stacken Development Kit viktig information
Dessa versionsanmärkningar innehåller information om förbättringar, korrigeringar och kända problem i Azure-stacken Development Kit. Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](.\.\azure-stack-updates.md#determine-the-current-version).

> Hålla dig uppdaterad med vad är nytt i ASDK genom att prenumerera på den [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-201803291"></a>Skapa 20180329.1

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Nya funktioner och korrigeringar som släppts för Azure-stacken integrerade system version 1803 gäller för Azure-stacken Development Kit. Finns det [nya funktioner](.\.\azure-stack-update-1803.md#new-features) och [fast problem](.\.\azure-stack-update-1803.md#fixed-issues) avsnitt i Azure-stacken 1803 uppdatera viktig information för information.  
> [!IMPORTANT]    
> Några av de objekt som visas i den **nya funktioner** och **fast problem** avsnitt är endast relevant för Azure-stacken integrerat system.

### <a name="changes"></a>Ändringar
- Ett sätt att ändra tillståndet för ett nyskapat erbjudande från *privata* till *offentliga* eller *inaktiverade* har ändrats. Mer information finns i [skapa ett erbjudande](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- Möjlighet [att öppna en ny supportförfrågan i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - Azure-stacken Development Kit använda https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- När du visar egenskaperna för en resurs eller en resursgrupp, den **flytta** är inaktiverat. Det är förväntat. Flytta resurser eller resursgrupper mellan resursgrupper eller prenumerationer stöds inte för närvarande.
 
- Du ser en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.

- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.

- På instrumentpanelen i administrationsportal panelen Update kan inte visa information om uppdateringar. Klicka på ikonen Uppdatera det för att lösa problemet.

-   Du kan se en kritisk varning för komponenten Microsoft.Update.Admin i administrationsportal. Varningens namn, beskrivning och reparation alla visas som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

    Den här aviseringen kan ignoreras. 



#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.
 
#### <a name="compute"></a>Compute
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- När du skapar virtuella datorer på Azure-stacken användarportalen visar portalen ett felaktigt antal datadiskar som kan kopplas till DS-serien VM. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- När en VM-avbildning misslyckas skapas kan en misslyckade objekt som du inte kan ta bort läggas till bladet VM-avbildningar beräkning.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

-  Om det tar för lång tid att etablera ett tillägg på en distribution av Virtuella datorer, bör användarna kan etablering timeout-värde i stället för att stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Nätverk
- Under **nätverk**, om du klickar på **anslutning** att ställa in en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.

- När en virtuell dator som associeras med en offentlig IP-adress, kan du koppla den virtuella datorn från att IP-adress. Disassociation verkar fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny.



- Azure-stacken stöder en enda *lokal nätverksgateway* per IP-adress. Detta gäller över alla klient-prenumerationer. Efter att skapa den första gateway nätverksanslutningen, efterföljande försök att skapa en gateway för lokala nätverksresurser med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server inställning av *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna är inte pushas till virtuella datorer i det virtuella nätverket.
 
- Azure-stacken stöder inte att lägga till ytterligare nätverksgränssnitt i en VM-instans när den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste ha definierats vid tidpunkten för distribution.



#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- Det kan ta upp till en timme innan användarna kan skapa databaser i en ny SQL eller MySQL SKU.

- Den databas som värd för servrar måste vara dedikerade för användning av resource provider och arbetsbelastningar. Du kan inte använda en instans som används av några andra konsumenten, inklusive Apptjänster.

#### <a name="app-service"></a>App Service
- Användare måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

- För att skala ut infrastruktur (anställda, hantering, frontend roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.
 
#### <a name="usage"></a>Användning  
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub
- När du använder den *anropa webrequest* PowerShell-cmdlet för att ladda ned Azure-stacken verktyg från Github, du får ett felmeddelande:     
    -  *anropa webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Detta fel uppstår på grund av en senaste utfasningen för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standardinställning för PowerShell). Mer information finns i [svaga kryptografiska standarder borttagning meddelande](https://githubengineering.com/crypto-removal-notice/).

  Lös problemet genom att lägga till `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` till början av skriptet för att tvinga PowerShell-konsolen att använda TLSv1.2 när laddas ned från GitHub-lagringsplatser.






## <a name="build-201803021"></a>Skapa 20180302.1

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar
Finns det [nya funktioner och korrigeringar](.\.\azure-stack-update-1802.md#new-features-and-fixes) avsnittet av Azure-stacken 1802 update viktig information om Azure-stacken integrerat system.

> [!IMPORTANT]    
> Några av de objekt som visas i den **nya funktioner och korrigeringar** avsnittet gäller bara för Azure-stacken integrerat system.


### <a name="known-issues"></a>Kända problem
 
#### <a name="portal"></a>Portalen
- Möjlighet [att öppna en ny supportförfrågan i listrutan](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) från inom administratören portalen är inte tillgänglig. I stället använder du följande länk:     
    - Azure-stacken Development Kit använda https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- När du visar egenskaperna för en resurs eller en resursgrupp, den **flytta** är inaktiverat. Det är förväntat. Flytta resurser eller resursgrupper mellan resursgrupper eller prenumerationer stöds inte för närvarande.
 
- Du ser en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.

- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.

- Du kan inte visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan du använda PowerShell för att kontrollera behörigheter.

- På instrumentpanelen i administrationsportal panelen Update kan inte visa information om uppdateringar. Klicka på ikonen Uppdatera det för att lösa problemet.

-   Du kan se en kritisk varning för komponenten Microsoft.Update.Admin i administrationsportal. Varningens namn, beskrivning och reparation alla visas som:  
    - *FEL - mall för FaultType ResourceProviderTimeout saknas.*

    Den här aviseringen kan ignoreras. 

- I både administrationsportal och användarportalen, bladet översikt misslyckas att läsa in när du väljer bladet översikt för lagringskonton som skapats med en äldre API-version (exempel: 2015-06-15). 

  Som en tillfällig lösning kan använda PowerShell för att köra den **Start ResourceSynchronization.ps1** skript för att återställa åtkomsten till information om lagringskonto. [Skriptet är tillgänglig från GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts), måste köras med administratörsbehörighet för tjänsten på development kit värden om du använder ASDK.  

- Den **tjänstens hälsa** bladet inte laddas. När du öppnar bladet tjänstens hälsa i administratören eller användaren portal, Azure stacken visas ett fel och läses inte in information. Detta är förväntat. Även om du kan markera och öppna tjänstens hälsa, funktionen är inte tillgänglig ännu, men de kommer att genomföras i en framtida version av Azure-stacken.

#### <a name="health-and-monitoring"></a>Hälsa och övervakning
I administrationsportalen för Azure-stacken kan du se en kritisk varning med namnet **väntande externa certifikatet upphör att gälla**.  Den här aviseringen kan ignoreras och påverkar driften av Azure-stacken Development Kit. 


#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.
 
#### <a name="compute"></a>Compute
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

- Azure-stacken stöder endast fasta typ av virtuella hårddiskar. Vissa bilder som erbjuds via marketplace Azure stacken använder dynamiska virtuella hårddiskar, men de har tagits bort. Ändra storlek på en virtuell dator (VM) med en dynamisk disk som är ansluten till den lämnar den virtuella datorn i ett felaktigt tillstånd.

  Om du vill undvika det här problemet måste du ta bort den virtuella datorn utan att ta bort den Virtuella datorns disk, en VHD-blobben i ett lagringskonto. Sedan konvertera den virtuella Hårddisken från en dynamisk disk till en fast disk och skapa den virtuella datorn.

- När du skapar virtuella datorer på Azure-stacken användarportalen visar portalen ett felaktigt antal datadiskar som kan kopplas till DS-serien VM. DS-serien virtuella datorer kan hantera så många datadiskar som Azure-konfiguration.

- När en VM-avbildning misslyckas skapas kan en misslyckade objekt som du inte kan ta bort läggas till bladet VM-avbildningar beräkning.

  Skapa en ny VM-avbildning som en lösning med en dummy VHD som kan skapas med Hyper-V (nya VHD-sökvägen C:\dummy.vhd-fast - SizeBytes 1 GB). Den här processen ska åtgärda problemet som förhindrar att ta bort objektet misslyckades. Därefter, 15 minuter efter skapar avbildningen av dummy har bort den.

  Du kan sedan försöker redownload VM-avbildning som tidigare har misslyckats.

-  Om det tar för lång tid att etablera ett tillägg på en distribution av Virtuella datorer, bör användarna kan etablering timeout-värde i stället för att stoppa processen för att frigöra eller ta bort den virtuella datorn.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Nätverk
- Under **nätverk**, om du klickar på **anslutning** att ställa in en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.

- När en virtuell dator som associeras med en offentlig IP-adress, kan du koppla den virtuella datorn från att IP-adress. Disassociation verkar fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn.

  För närvarande måste du använda endast nya offentliga IP-adresser för nya virtuella datorer som du skapar.

  Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny.

-   Funktionen för IP-vidarebefordran är visas på portalen, men att aktivera IP-vidarebefordring har ingen effekt. Den här funktionen stöds inte ännu.

- Azure-stacken stöder en enda *lokal nätverksgateway* per IP-adress. Detta gäller över alla klient-prenumerationer. Efter att skapa den första gateway nätverksanslutningen, efterföljande försök att skapa en gateway för lokala nätverksresurser med samma IP-adress blockeras.

- I ett virtuellt nätverk som har skapats med en DNS-Server inställning av *automatisk*, ändra till en anpassad DNS-servern misslyckas. De uppdaterade inställningarna är inte pushas till virtuella datorer i det virtuella nätverket.
 
- Azure-stacken stöder inte att lägga till ytterligare nätverksgränssnitt i en VM-instans när den virtuella datorn har distribuerats. Om den virtuella datorn kräver mer än ett nätverksgränssnitt, måste ha definierats vid tidpunkten för distribution.



#### <a name="sql-and-mysql"></a>SQL- och MySQL 
- Det kan ta upp till en timme innan användarna kan skapa databaser i en ny SQL eller MySQL SKU.

- Den databas som värd för servrar måste vara dedikerade för användning av resource provider och arbetsbelastningar. Du kan inte använda en instans som används av några andra konsumenten, inklusive Apptjänster.

#### <a name="app-service"></a>App Service
- Användare måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

- För att skala ut infrastruktur (anställda, hantering, frontend roller), måste du använda PowerShell enligt beskrivningen i viktig information för beräkning.
 
#### <a name="usage"></a>Användning  
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub
- När du använder den *anropa webrequest* PowerShell-cmdlet för att ladda ned Azure-stacken verktyg från Github, du får ett felmeddelande:     
    -  *anropa webrequest: begäran avbröts: Det gick inte att skapa säkra SSL/TLS-kanalen.*     

  Detta fel uppstår på grund av en senaste utfasningen för GitHub om Tlsv1 och Tlsv1.1 kryptografiska (standardinställning för PowerShell). Mer information finns i [svaga kryptografiska standarder borttagning meddelande](https://githubengineering.com/crypto-removal-notice/).

  Lös problemet genom att lägga till `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` till början av skriptet för att tvinga PowerShell-konsolen att använda TLSv1.2 när laddas ned från GitHub-lagringsplatser.




## <a name="build-201801032"></a>Build 20180103.2

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

- Finns det [nya funktioner och korrigeringar](.\.\azure-stack-update-1712.md#new-features-and-fixes) avsnittet av Azure-stacken 1712 uppdatering viktig information om Azure-stacken integrerat system.

    > [!IMPORTANT]
    > Några av de objekt som visas i den **nya funktioner och korrigeringar** avsnittet gäller bara för Azure-stacken integrerat system.

### <a name="known-issues"></a>Kända problem
 
#### <a name="deployment"></a>Distribution
- Du måste ange en tidsserver efter IP-adress under distributionen.

#### <a name="infrastructure-management"></a>Infrastrukturhantering
- Aktivera inte infrastrukturen för säkerhetskopiering på den **infrastruktur säkerhetskopiering** bladet.
- Baseboard management controller (BMC) IP-adress och modell visas inte i viktig information för en enhet skala-nod. Detta beteende förväntas i Azure-stacken Development Kit.

#### <a name="portal"></a>Portalen
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.
- När du visar egenskaperna för en resursgrupp i **flytta** är inaktiverat. Det är förväntat. Flytta resursgrupper mellan prenumerationer stöds inte för närvarande.
-  För alla arbetsflöden där du väljer en prenumeration, resursgrupp eller plats i en listrutan, kan en eller flera av följande problem uppstå:

   - Du kan se en tom rad längst upp i listan. Du måste fortfarande kunna markera ett objekt som förväntat.
   - Om listan över objekt i den nedrullningsbara listrutan är kort kan kanske du inte visa något av objektnamn.
   - Om du har flera prenumerationer för användaren, får den nedrullningsbara resurslistan vara tom. 

   Du kan skriva namnet på den prenumeration eller resursgrupp (om du vet att den) Undvik de två sista problem, eller kan du använda PowerShell i stället.

- Du ser en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.
- Om den **komponenten** användaren klickar på från någon **infrastrukturrollen** meddela den resulterande **översikt** bladet försöker läsa in och misslyckas. Dessutom den ** översikt ** bladet har ingen timeout.
- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.
- Det går inte att visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan kontrollera du behörigheterna med hjälp av PowerShell.
- Den **tjänstens hälsa** bladet inte laddas. När du öppnar bladet tjänstens hälsa i administratören eller användaren portal, Azure stacken visas ett fel och läses inte in information. Detta är förväntat. Även om du kan markera och öppna tjänstens hälsa, funktionen är inte tillgänglig ännu, men de kommer att genomföras i en framtida version av Azure-stacken.
#### <a name="marketplace"></a>Marketplace
- Vissa marketplace-objekt tas bort i den här versionen på grund av problem för kompatibilitet. Dessa ska aktiveras igen efter ytterligare verifiering.
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.
 
#### <a name="compute"></a>Compute
- Användare möjlighet att skapa en virtuell dator med geo-redundant lagring. Den här konfigurationen gör att skapa en virtuell dator misslyckas. 
- Du kan konfigurera en virtuell dator tillgänglighetsuppsättning endast med en feldomän på en och en uppdateringsdomän i en.
- Det finns inga marketplace-upplevelse för att skapa virtuella datorer. Du kan skapa en skala som anges med hjälp av en mall.
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.

#### <a name="networking"></a>Nätverk
- Du kan inte skapa en belastningsutjämnare med en offentlig IP-adress med hjälp av portalen. Som en tillfällig lösning kan använda du PowerShell för att skapa belastningsutjämnaren.
- När du skapar en Utjämning av nätverksbelastning måste du skapa en regel för network adress translation (NAT). Om du inte får ett fel när du försöker lägga till en NAT-regel när belastningsutjämnaren har skapats.
- Under **nätverk**, om du klickar på **anslutning** att ställa in en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.
- Du kan koppla en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med den IP-adressen. Disassociation visas ska fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny. För närvarande måste du bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.
- Azure Stack-operatörer kanske inte kan distribuera, ta bort, ändra Vnet eller Nätverkssäkerhetsgrupper. Det här problemet visas främst på efterföljande update försök av samma paket. Detta beror på ett paketering problem med en uppdatering som för närvarande är under undersökningen.
- Internt Load Balancing (ILB) hanterar felaktigt MAC-adresser för backend-virtuella datorer som släpper paket till backend-nätverket när du använder Linux-instanser.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.
 
#### <a name="usage"></a>Användning  
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.

#### <a name="identity"></a>Identitet

I Azure Active Directory Federation Services (ADFS) distribuerade miljöer, den **azurestack\azurestackadmin** inte längre är standard providern Prenumerationens ägare. I stället för att logga in på den **administrationsportalen / adminmanagement endpoint** med den **azurestack\azurestackadmin**, du kan använda den **azurestack\cloudadmin** kontot, så att hantera och använda standard providern prenumerationen.

> [!IMPORTANT]
> Även den **azurestack\cloudadmin** konto är ägare till prenumerationen Standard Provider i ADFS distribuerade miljöer, har inte behörighet att RDP till värden. Fortsätta att använda den **azurestack\azurestackadmin** konto eller det lokala administratörskontot för att logga in, komma åt och hantera värden efter behov.




## <a name="build-201711221"></a>Skapa 20171122.1

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

- Finns det [nya funktioner och korrigeringar](.\.\azure-stack-update-1711.md#new-features-and-fixes) avsnittet av Azure-stacken 1711 update viktig information om Azure-stacken integrerat system.

    > [!IMPORTANT]
    > Några av de objekt som visas i den **nya funktioner och korrigeringar** avsnittet gäller bara för Azure-stacken integrerat system.

### <a name="known-issues"></a>Kända problem

 
#### <a name="deployment"></a>Distribution
- Du måste ange en tidsserver efter IP-adress under distributionen.
- Från och med version 1711, **CloudAdmin** är ett reserverat kontonamn och bör inte manuellt anges när du distribuerar i development kit. 

#### <a name="infrastructure-management"></a>Infrastrukturhantering
- Aktivera inte infrastrukturen för säkerhetskopiering på den **infrastruktur säkerhetskopiering** bladet.
- Baseboard management controller (BMC) IP-adress och modell visas inte i viktig information för en enhet skala-nod. Detta beteende förväntas i Azure-stacken Development Kit.

#### <a name="portal"></a>Portalen
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.
- När du visar egenskaperna för en resursgrupp i **flytta** är inaktiverat. Det är förväntat. Flytta resursgrupper mellan prenumerationer stöds inte för närvarande.
-  För alla arbetsflöden där du väljer en prenumeration, resursgrupp eller plats i en listrutan, kan en eller flera av följande problem uppstå:

   - Du kan se en tom rad längst upp i listan. Du måste fortfarande kunna markera ett objekt som förväntat.
   - Om listan över objekt i den nedrullningsbara listrutan är kort kan kanske du inte visa något av objektnamn.
   - Om du har flera prenumerationer för användaren, får den nedrullningsbara resurslistan vara tom. 

   Du kan skriva namnet på den prenumeration eller resursgrupp (om du vet att den) Undvik de två sista problem, eller kan du använda PowerShell i stället.

- Du ser en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.
- Om den **komponenten** användaren klickar på från någon **infrastrukturrollen** meddela den resulterande **översikt** bladet försöker läsa in och misslyckas. Dessutom den **översikt** bladet har ingen timeout.
- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.
- Det går inte att visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan kontrollera du behörigheterna med hjälp av PowerShell.
- Den **tjänstens hälsa** bladet inte laddas. När du öppnar bladet tjänstens hälsa i administratören eller användaren portal, Azure stacken visas ett fel och läses inte in information. Detta är förväntat. Även om du kan markera och öppna tjänstens hälsa, funktionen är inte tillgänglig ännu, men de kommer att genomföras i en framtida version av Azure-stacken.

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
- Under **nätverk**, om du klickar på **anslutning** att ställa in en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.
- Du kan koppla en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med den IP-adressen. Disassociation visas ska fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny. För närvarande måste du bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.
- Azure Stack-operatörer kanske inte kan distribuera, ta bort, ändra Vnet eller Nätverkssäkerhetsgrupper. Det här problemet visas främst på efterföljande update försök av samma paket. Detta beror på ett paketering problem med en uppdatering som för närvarande är under undersökningen.
- Internt Load Balancing (ILB) hanterar felaktigt MAC-adresser för backend-virtuella datorer som släpper paket till backend-nätverket när du använder Linux-instanser.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

    > [!NOTE]
    > Visa enskilda [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) och [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) konfigurera artiklar om version kompatibilitet.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

#### <a name="usage"></a>Användning  
- Offentlig IP-adress användning mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.

#### <a name="identity"></a>Identitet

I Azure Active Directory Federation Services (ADFS) distribuerade miljöer, den **azurestack\azurestackadmin** inte längre är standard providern Prenumerationens ägare. I stället för att logga in på den **administrationsportalen / adminmanagement endpoint** med den **azurestack\azurestackadmin**, du kan använda den **azurestack\cloudadmin** kontot, så att hantera och använda standard providern prenumerationen.

> [!IMPORTANT]
> Även den **azurestack\cloudadmin** konto är ägare till prenumerationen Standard Provider i ADFS distribuerade miljöer, har inte behörighet att RDP till värden. Fortsätta att använda den **azurestack\azurestackadmin** konto eller det lokala administratörskontot för att logga in, komma åt och hantera värden efter behov.

