---
title: "Viktig information för Microsoft Azure-stacken Development Kit | Microsoft Docs"
description: "Förbättringar av korrigeringar och kända problem med Azure-stacken Development Kit."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2017
ms.author: andredm
ms.openlocfilehash: 6abbad8a086571702fd2e9d4d5d172189bb3c339
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure-stacken Development Kit viktig information

*Gäller för: Azure stacken Development Kit*

Dessa versionsanmärkningar innehåller information om förbättringar, korrigeringar och kända problem i Azure-stacken Development Kit. Om du inte vet vilken version du kör, kan du [använda portalen för att kontrollera](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201711221"></a>Skapa 20171122.1

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

- Finns det [nya funktioner och korrigeringar](azure-stack-update-1711.md#new-features-and-fixes) avsnittet av Azure-stacken 1711 update viktig information om Azure-stacken integrerat system.

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
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.
 
#### <a name="usage-and-billing"></a>Användning och fakturering
- Offentliga IP-adress mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.

#### <a name="identity"></a>Identitet

I Azure Active Directory Federation Services (ADFS) distribuerade miljöer, den **azurestack\azurestackadmin** inte längre är standard providern Prenumerationens ägare. I stället för att logga in på den **administrationsportalen / adminmanagement endpoint** med den **azurestack\azurestackadmin**, du kan använda den **azurestack\cloudadmin** kontot, så att hantera och använda standard providern prenumerationen.

> [!IMPORTANT]
> Även den **azurestack\cloudadmin** konto är ägare till prenumerationen Standard Provider i ADFS distribuerade miljöer, har inte behörighet att RDP till värden. Fortsätta att använda den **azurestack\azurestackadmin** konto eller det lokala administratörskontot för att logga in, komma åt och hantera värden efter behov.


## <a name="build-201710201"></a>Skapa 20171020.1

### <a name="improvements-and-fixes"></a>Förbättringar och korrigeringar

Om du vill se en lista över förbättringar och korrigeringar i den 20171020.1 versionen finns i [förbättringar och korrigeringar](azure-stack-update-1710.md#improvements-and-fixes) avsnittet av 1710 viktig information om Azure-stacken integrerat system. Några av de objekt som visas i avsnittet ”ytterligare kvalitet förbättringar och korrigeringar” är endast relevant för integrerade system.

Dessutom har följande korrigeringar gjorts:
- Ett problem har åtgärdats där Compute-resursprovidern visas ett okänt tillstånd.
- Ett problem har åtgärdats där kvoter kan inte visas i administratörsportalen när du skapar dem och senare försök att visa mer information.

### <a name="known-issues"></a>Kända problem

#### <a name="powershell"></a>PowerShell
- Version av PowerShell-modulen AzureRM 1.2.11 levereras med en lista över bryta ändringar. Information om hur du uppgraderar från 1.2.10 version, finns det [Migreringsguide](https://aka.ms/azspowershellmigration).
 
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
- I den **-aktivering krävs** varning aviseringsinformation inte klicka på länken till den **AzureBridge** komponent. Om du gör det **översikt** bladet kommer misslyckat försök att läsa in, och går inte ut.
- I administratörsportalen, kan du se en **gick inte att hämta hyresgäster** fel i den **meddelanden** område. Du kan ignorera det här felet.
- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.
- Det går inte att visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan kontrollera du behörigheterna med hjälp av PowerShell.
 
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
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.
 
#### <a name="usage-and-billing"></a>Användning och fakturering
- Offentliga IP-adress mätaren användningsdata visar samma *EventDateTime* värde för varje post i stället för den *TimeDate* tidsstämpel som visar när posten har skapats. För närvarande kan du använda informationen för att utföra redovisas korrekt användning av offentlig IP-adress.

## <a name="build-201709283"></a>Skapa 20170928.3

### <a name="known-issues"></a>Kända problem

#### <a name="powershell"></a>PowerShell
- Version av PowerShell-modulen AzureRM 1.2.11 levereras med en lista över bryta ändringar. Information om hur du uppgraderar från 1.2.10 version, finns det [Migreringsguide](https://aka.ms/azspowershellmigration).

#### <a name="deployment"></a>Distribution
- Du måste ange en tidsserver efter IP-adress under distributionen.

 #### <a name="infrastructure-management"></a>Infrastrukturhantering
- Aktivera inte infrastrukturen för säkerhetskopiering på den **infrastruktur säkerhetskopiering** bladet.
- Compute-resursprovidern visar ett okänt tillstånd.
- Baseboard management controller (BMC) IP-adress och modell visas inte i viktig information för en enhet skala-nod. Detta beteende förväntas i Azure-stacken Development Kit. 
   
#### <a name="portal"></a>Portalen
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.
- När du visar egenskaperna för en resursgrupp i **flytta** är inaktiverat. Det är förväntat. Flytta resursgrupper mellan prenumerationer stöds inte för närvarande.
- Du ser en **-aktivering krävs** varning om att registrera din Azure-stacken Development Kit. Det är förväntat.
- I den **-aktivering krävs** varning aviseringsinformation inte klicka på länken till den **AzureBridge** komponent. Om du gör det **översikt** bladet kommer misslyckat försök att läsa in, och går inte ut.
- Kvoter kan inte visas i administratörsportalen när du skapar dem och försök sedan att senare visa mer information. Som en lösning i **tjänster och kvoter**, klickar du på **Lägg till**, och Lägg till en ny post.
- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.
- Det går inte att visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan kontrollera du behörigheterna med hjälp av Powershell.
  
#### <a name="marketplace"></a>Marketplace
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.
 
#### <a name="compute"></a>Compute
- Användare möjlighet att skapa en virtuell dator med geo-redundant lagring. Den här konfigurationen gör att skapa en virtuell dator misslyckas.
- Du kan konfigurera en virtuell dator tillgänglighetsuppsättning endast med en feldomän på en och en uppdateringsdomän i en.
- Det finns inga marketplace-upplevelse för att skapa virtuella datorer. Du kan skapa en skala som anges med hjälp av en mall.

#### <a name="networking"></a>Nätverk
- Du kan inte skapa en belastningsutjämnare med en offentlig IP-adress med hjälp av portalen. Som en tillfällig lösning kan använda du PowerShell för att skapa belastningsutjämnaren.
- När du skapar en Utjämning av nätverksbelastning måste du skapa en regel för network adress translation (NAT). Om du inte får ett fel när du försöker lägga till en NAT-regel när belastningsutjämnaren har skapats.
- Under **nätverk**, om du klickar på **anslutning** att ställa in en VPN-anslutning **VNet-till-VNet** har listats som en möjlig anslutningstyp. Välj inte det här alternativet. För närvarande endast den **plats-till-plats (IPsec)** stöds.
- Du kan koppla en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med den IP-adressen. Disassociation visas ska fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (kallas ibland ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny. För närvarande måste du bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.

#### <a name="sqlmysql"></a>SQL/MySQL
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.

#### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.
