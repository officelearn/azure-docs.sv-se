---
title: Azure-stacken 1712 Update | Microsoft Docs
description: "Lär dig mer om vad som finns i 1712 uppdateringen för Azure-stacken integrerat system, kända problem och var du kan hämta uppdateringen."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: andredm
ms.openlocfilehash: fadd72d76862694af96b51d198b6693e104c05de
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="azure-stack-1712-update"></a>Azure-stacken 1712 uppdatering

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i det här uppdateringspaketet kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i kända problem direkt relaterad till uppdateringen och kända problem med build (efter installationen).

> [!IMPORTANT]
> Det här uppdateringspaketet gäller endast för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="build-reference"></a>Skapa referens

Azure-stacken 1712 uppdatera versionsnumret är **180106.1**. Om en kund har distribuerats **180103.2** tidigare, du behöver inte tillämpa **180106.1**.

## <a name="before-you-begin"></a>Innan du börjar

> [!IMPORTANT]
> Försök inte att skapa virtuella datorer under installationen 1712 uppdateringen. Se [hantera uppdateringar i Azure-stacken översikt](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) för mer information.

### <a name="prerequisites"></a>Förutsättningar

Du måste först installera Azure-stacken [1711 uppdatera](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) innan du installerar den här uppdateringen.

### <a name="post-update-steps"></a>Steg efter uppdateringen

Den här uppdateringen kräver också att du installerar uppdateringar av inbyggd programvara från OEM-partner när du har slutfört installationen av 1712 Azure Stack.

> [!NOTE]
> Hänvisa till webbplatsen OEM-partner att hämta uppdateringar.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Den här uppdateringen innehåller följande förbättringar och korrigeringar för Azure-stacken.

#### <a name="new-features"></a>Nya funktioner

- Nya användarupplevelsen när du skapar objekt från Marketplace
- AzureStack test-cmdlet för att validera moln för Azure-stacken tillgängliga via Privilegierade slutpunkt
- Möjligheten att registrera en frånkopplad distribution av Azure-stacken
- Övervaka aviseringar för certifikatet och användaren kontots giltighetstid
- Tillagda uppdatering BMCCredential cmdlet i program för BMC lösenord rotation
- Loggning av nätverk uppdateringar till stöd för loggning på begäran
- Stöd för avbildningsåterställning åtgärden för Virtual Machine skalor uppsättningar (VMSS)
- Aktivera helskärmsläge på ERCS VM för CloudAdmin inloggning
- Klienter kan aktivera virtuella Windows-datorer automatiskt

#### <a name="fixes"></a>Korrigeringar

- Korrigeringen om du vill visa driftstatus för noden i underhållsläge när du kör reparation
- Åtgärda till rätt offentlig IP användning poster tid/datum tidsstämpel
- Olika andra prestanda, stabilitet och säkerhetskorrigeringar
- TimeSource och Defender privilegierad felkorrigeringar för slutpunkt-modul

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 nya funktioner och korrigeringar

- [Januari, 3-2018 – KB4056890 (OS-version 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Uppdateringen innehåller programvara korrigeringar för branschomfattande säkerhetsproblem som beskrivs av [MSRC Security Advisory av 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).
 
### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

Det här avsnittet innehåller kända problem som kan uppstå under installationen av uppdateringen 1712.

1. **Symptom:** Azure Stack kan se följande fel under uppdateringen: *”typ” CheckHealth' av rollen 'VirtualMachines' utlöses ett undantag: \n\nVirtual datorn hälsokontrollen för - ACS01 gav följande fel. \nThere gick inte att hämta information om Virtuellt från värdar. Undantag information: \nGet-VM: åtgärden på datorn ”Node03' misslyckades: det WS-Management-tjänsten kan inte behandla begäran. WMI-\nservice eller WMI-providern returnerade ett okänt fel: HRESULT 0x8004106c ”.*
    1. **Orsak:** problemet orsakas av ett problem i Windows Server som är avsedd att åtgärdas i efterföljande uppdateringar för Windows server.
    2. **Lösning:** kontakta Microsofts kundservice och Support (CSS) för att få hjälp.
<br><br>
2. **Symptom:** Azure Stack kan se följande fel under uppdateringen:*”aktivera seed ringen VM misslyckades på noden värden Node03 med ett fel: [värden Node03] ansluta till fjärrservern värden Node03 misslyckades med den följande felmeddelande: WinRM-klienten tog emot en HTTP-server felstatus (500), men tjänsten remote innehöll inte övrig information om orsaken till felet ”.*
    1. **Orsak:** problemet orsakas av ett problem i Windows Server som är avsedd att åtgärdas i efterföljande uppdateringar för Windows server. 
    2. **Lösning:** kontakta Microsofts kundservice och Support (CSS) för att få hjälp.
<br><br>

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Det här avsnittet innehåller efter installationen kända problem med build **180106.1**.

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
- Du kan koppla en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med den IP-adressen. Disassociation visas ska fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny. För närvarande måste du bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.
- Azure Stack-operatörer kanske inte kan distribuera, ta bort, ändra Vnet eller Nätverkssäkerhetsgrupper. Det här problemet visas främst på efterföljande update försök av samma paket. Detta beror på ett paketering problem med en uppdatering som för närvarande är under undersökningen.
- Internt Load Balancing (ILB) hanterar felaktigt MAC-adresser för backend-virtuella datorer som bryter Linux-instanser.
 
#### <a name="sqlmysql"></a>SQL/MySQL
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

    > [!NOTE]
    > Du bör inte påverka din befintliga SQL- eller MySQL resource provider användare när du uppdaterar din Azure-stacken integrerade system till 1712 version. Du kan fortsätta använda din aktuella SQL eller MySQL resource provider versioner tills en ny Azure Stack-uppdatering är tillgänglig.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.

#### <a name="identity"></a>Identitet

I Azure Active Directory Federation Services (ADFS) distribuerade miljöer, den **azurestack\azurestackadmin** inte längre är standard providern Prenumerationens ägare. I stället för att logga in på den **administrationsportalen / adminmanagement endpoint** med den **azurestack\azurestackadmin**, du kan använda den **azurestack\cloudadmin** kontot, så att hantera och använda standard providern prenumerationen.

> [!IMPORTANT]
> Även om den **azurestack\cloudadmin** konto är ägare till prenumerationen Standard Provider i ADFS distribuerade miljöer, har inte behörighet att RDP till värden. Fortsätta att använda den **azurestack\azurestackadmin** konto eller det lokala administratörskontot för att logga in, komma åt och hantera värden efter behov.

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned uppdateringspaketet Azure Stack 1712 från [här](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Mer information

Microsoft tillhandahåller ett sätt att övervaka och återuppta uppdateringar med hjälp av detta Privilegierade slutpunkt (program) installeras med uppdatering 1712.

- Finns det [övervaka uppdateringar i Azure-stacken använder Privilegierade endpoint-dokumentationen](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 
## <a name="see-also"></a>Se också

- Se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md) en översikt över uppdateringshantering i Azure-stacken.
- Se [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md) för mer information om hur du installerar uppdateringar med Azure-stacken.
