---
title: Viktiga funktioner och koncept i Azure Stack | Microsoft Docs
description: Läs mer om viktiga funktioner och koncept i Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 1b533c945fdcfc3d1072a7d8a513126ca3f1f72a
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303592"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Huvudfunktioner och koncept i Azure Stack
Om du inte har använt Microsoft Azure Stack kan dessa villkor och funktionsbeskrivning vara användbart.

## <a name="personas"></a>Profiler
Det finns två varianter av användare för Microsoft Azure Stack, operatorn och användaren.

* Ett Azure Stack **operatorn** kan konfigurera Azure Stack genom att hantera erbjudanden, planer, tjänster, kvoter och priser för att tillhandahålla resurser till deras klientorganisationer användare. Operatörer kan du också hantera kapacitet och svara på aviseringar.  
* Ett Azure Stack **användaren** (kallas även en klient) använder tjänster som erbjuder operatorn. Användare kan etablera, övervaka och hantera tjänster som de som prenumererar på, t.ex web apps, lagring och virtuella datorer.

## <a name="portal"></a>Portalen
De huvudsakliga sätt att interagera med Microsoft Azure Stack är administrationsportalen, användarportalen och PowerShell.

Azure Stack-portaler backas var och en av separata instanser av Azure Resource Manager. En operatör använder administrationsportalen att hantera Azure Stack och för att exempelvis skapa klient erbjudanden. Användarportalen (kallas även klientportalen) tillhandahåller en Självbetjäning för användning av molnresurser, t.ex. virtuella datorer, lagringskonton och web apps. Mer information finns i [med hjälp av Azure Stack administratörs- och portaler](azure-stack-manage-portals.md).

## <a name="identity"></a>Identitet 
Azure Stack använder Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) som en identitetsleverantör.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure AD är Microsofts molnbaserade, flera innehavare identitetsprovider. De flesta hybridscenarier använda Azure AD som Identitetslagret.

### <a name="active-directory-federation-services"></a>Active Directory Federation Services
Du kan välja att använda Active Directory Federation Services (AD FS) för frånkopplade distributioner av Azure Stack. Azure Stack-resursprovidrar och andra program fungerar ungefär på samma sätt med AD FS som de gör med Azure AD. Azure Stack innehåller en egen Active Directory-instans och en Active Directory Graph API. Azure Stack Development Kit har stöd för följande AD FS-scenarier:

- Logga in på distributionen med hjälp av AD FS.
- Skapa en virtuell dator med hemligheter i Key Vault
- Skapa ett valv för att lagra/komma åt hemligheter
- Skapa anpassad RBAC-roller i prenumerationen
- Tilldela användare till RBAC-roller på resurser
- Skapa systemomfattande RBAC-roller via Azure PowerShell
- Logga in som en användare via Azure PowerShell
- Skapa tjänstens huvudnamn använda dem för att logga in på Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regioner, tjänster, planer, erbjudanden och prenumerationer
I Azure Stack levereras tjänster till klienter med hjälp av regioner, prenumerationer, erbjudanden och planer. Klienter kan prenumerera på flera erbjudanden. Erbjudanden kan ha en eller flera planer och planer kan ha en eller flera tjänster.

![](media/azure-stack-key-features/image4.png)

Exempelhierarki för en klient erbjudanden, var och en med olika planer och tjänster.

### <a name="regions"></a>Regioner
Regionerna i Azure Stack är en grundläggande del av skalning och hantering. En organisation kan ha flera regioner med resurser som är tillgängliga i varje region. Regioner kan också ha olika service-erbjudanden. I Azure Stack Development Kit en enskild region stöds och heter automatiskt *lokala*.

### <a name="services"></a>Tjänster
Microsoft Azure Stack kan leverantörer att leverera en mängd olika tjänster och program, till exempel virtuella datorer, SQL Server-databaser, SharePoint, Exchange och mer.

### <a name="plans"></a>Planer
Prenumerationer är grupperingar av en eller flera tjänster. Som leverantör måste skapa du planer som du erbjuder dina klienter. Dina klienter prenumererar i sin tur på dina erbjudanden för att använda de planer och tjänster som ingår i dem.

Varje tjänst som lagts till i en plan kan konfigureras med kvotinställningar för att hantera Molnets kapacitet. Kvoter kan innehålla begränsningar, till exempel VM, RAM-minne och CPU-gränser och tillämpas per användarprenumeration. Kvoter kan delas efter plats. En plan som innehåller bearbetningstjänster från Region A kan till exempel ha en kvot på två virtuella datorer, 4 GB RAM-minne och 10 CPU-kärnor.

När du skapar ett erbjudande, tjänstadministratören kan innehålla en **Basplan**. Dessa basplaner ingår som standard när en klient som prenumererar på erbjudandet. När en användare prenumererar och prenumerationen har skapats, har användaren åtkomst till alla resursprovidrar som anges i dessa basplaner (med motsvarande kvoter).

Tjänstadministratören kan även inkludera **tilläggsplaner** i ett erbjudande. Tilläggsplaner ingår inte som standard i prenumerationen. Tilläggsplaner är ytterligare scheman (kvoter) tillgängliga i ett erbjudande som en Prenumerationens ägare kan lägga till prenumerationerna.

### <a name="offers"></a>Erbjudanden
Erbjudanden är grupper med en eller flera planer som leverantörer presenterar för klienterna att köpa (prenumerera). Erbjuder Alpha kan exempelvis innehålla Plan A som innehåller en uppsättning Beräkningstjänster och planera B som innehåller en uppsättning tjänster för lagring och nätverk.

Ett erbjudande som levereras med en uppsättning basplaner och administratörer kan skapa tilläggsplaner som klienter kan lägga till prenumerationen.

### <a name="subscriptions"></a>Prenumerationer
En prenumeration hör hur klienter köpa dina erbjudanden. En prenumeration är en kombination av en klient med ett erbjudande. En klient kan ha flera erbjudanden prenumerationer. Varje prenumeration gäller endast ett erbjudande. En klients prenumerationer avgör vilka prenumerationer/tjänster som de kan komma åt.

Prenumerationer att ordna och åtkomst till molnresurser och tjänster.

För administratören skapas en standard Providerprenumeration under distributionen. Den här prenumerationen kan användas för att hantera Azure Stack, distribuera ytterligare resursprovidrar och skapa planer och erbjudanden för klienter. Det bör inte användas för att köra kundens arbetsbelastningar och program. Från och med version 1804, komplettera två ytterligare prenumerationer den standard providerprenumeration; en prenumeration för Avläsning av programvara och en användning-prenumeration. Dessa tillägg underlätta att separera hanteringen av grundläggande infrastruktur, ytterligare resursprovidrar och arbetsbelastningar.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Med Azure Resource Manager kan arbeta du med dina resurser i infrastrukturen i en mallbaserade, deklarativa modell.   Det är ett gränssnitt som du kan använda för att distribuera och hantera dina lösningskomponenter. Fullständig information och riktlinjer finns i den [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Resursgrupper
Resursgrupper är samlingar av resurser, tjänster och program – och varje resurs har en typ, till exempel virtuella datorer, virtuella nätverk, offentlig IP-adresser, lagringskonton och webbplatser. Varje resurs måste vara i en resursgrupp och så resursgrupper logiskt att organisera resurser, till exempel av arbetsbelastning eller plats. I Azure Stack hanteras resurser, till exempel planer och erbjudanden också i resursgrupper.

Till skillnad från [Azure](../azure-resource-manager/resource-group-move-resources.md), du kan inte flytta Azure Stack-resurser mellan resursgrupper. När du visar egenskaperna för en resurs eller resursgrupp i Azure Stack-administrationsportalen, den *flytta* knappen är nedtonad och inte tillgänglig. Dessutom kan användningen av den **ändra resursgrupp** eller **ändra prenumerationen** åtgärder från resursgruppen eller resursen objektegenskaper stöds inte heller. Alla försökta flytta misslyckas.
 
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Med Azure Resource Manager kan du skapa en mall (i JSON-format) som definierar distributionen och konfigurationen av ditt program. Den här mallen kallas för en Azure Resource Manager-mall och tillhandahåller en deklarativ metod för att definiera distributionen. Genom att använda en mall kan du distribuera ditt program flera gånger under applivscykeln och vara säker på att dina resurser distribueras konsekvent.

## <a name="resource-providers-rps"></a>Resursproviders (RPs)
Resursproviders är webbtjänster som utgör grunden för alla Azure-baserade IaaS och PaaS-tjänster. Azure Resource Manager är beroende av olika RPs att ge åtkomst till tjänster.

Det finns fyra grundläggande RPs: Nätverk, lagring, beräkning och KeyVault. Var och en av dessa RPs hjälper dig att konfigurera och kontrollera dess respektive resurser. Administratörer kan också lägga till nya anpassade resursprovidrar.

### <a name="compute-rp"></a>Compute RP
Compute Resource Provider (CRP: N) gör att Azure Stack-klienter kan skapa sina egna virtuella datorer. CRP: N innehåller möjligheten att skapa virtuella datorer samt virtuella datorer, tillägg. Tjänsten VM-tillägget ger IaaS-funktioner för Windows och Linux-datorer.  Exempelvis kan du använda CRP: N att etablera en Linux-dator och kör Bash-skript under distributionen för att konfigurera den virtuella datorn.

### <a name="network-rp"></a>Nätverket RP
Nätverket Resource Provider (NRP) ger ett antal funktioner för Programvarudefinierad nätverksfunktion (SDN) och nätverk funktionen virtualisering (NFV) för det privata molnet.  Du kan använda NRP för att skapa resurser som software load balancers, offentliga IP-adresser, nätverkssäkerhetsgrupperna, virtuella nätverk.

### <a name="storage-rp"></a>Storage RP
Storage-RP levererar fyra Azure-konsekventa lagringstjänster: blob, tabell, kö och kontohantering. Den erbjuder även en storage administration molntjänst för att underlätta administration av tjänsten Azure-konsekventa Storage-tjänster. Azure Storage tillhandahåller flexibilitet för att lagra och hämta stora mängder Ostrukturerade data, till exempel dokument och mediefiler med Azure Blobs och strukturerade NoSQL-baserade data med Azure-tabeller. Mer information om Azure Storage finns i [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
BLOB storage lagrar alla datauppsättning. En blobb kan bestå av vilka slags textdata eller binära data som helst, till exempel ett dokument, en mediefil eller ett installationsprogram. Table storage lagrar strukturerade datauppsättningar. Table Storage är en nyckel- och attributdatabas för NoSQL som möjliggör snabb utveckling och snabb tillgång till stora mängder data. Queue storage erbjuder tillförlitlig meddelandehantering för arbetsflödesbearbetning och för kommunikation mellan komponenter i molntjänster.

Varje blobb är underordnad en behållare. Containrar är också ett praktiskt sätt att tilldela säkerhetsprinciper till grupper med objekt. Ett lagringskonto kan innehålla valfritt antal behållare och en behållare kan innehålla valfritt antal blobbar, upp till 500 TB kapacitetsgränsen för lagringskontot. Blob Storage erbjuder tre typer av blobbar: blockblobbar, tilläggsblobbar och sidblobbar (diskar). Blockblobbar är optimerade för direktuppspelning och lagring av molnobjekt och är ett bra alternativ för att lagra dokument, mediefiler, säkerhetskopior osv. Tilläggsblobbar påminner om blockblobbar, men är optimerade för tilläggsåtgärder. En tilläggsblobb kan bara uppdateras genom att ett nytt block läggs till i slutet. Tilläggsblobbar är praktiska i scenarier som loggning, där nya data bara behöver skrivas till slutet av blobben. Sidblobbar är optimerade för att representera IaaS-diskar och stöd för slumpmässiga skrivningar och kan vara upp till 1 TB. En nätverksansluten IaaS-disk för en virtuell Azure-dator är en VHD som lagras som en sidblobb.

#### <a name="table-storage"></a>Table Storage
Table storage är Microsofts NoSQL nyckel-/ attributlager – tjänsten har en design utan scheman, vilket skiljer den från traditionella relationsdatabaser. Eftersom data lagras brist scheman, är det enkelt att anpassa dina data efter behov av utvecklas. Table Storage är enkelt att använda, vilket gör det lätt för utvecklare att snabbt skapa program. Table Storage är en nyckel- och attributdatabas, vilket innebär att varje värde i en tabell lagras med ett typbestämt egenskapsnamn. Egenskapsnamnet kan användas för att filtrera och ange urvalskriterier. En uppsättning egenskaper och deras värden utgör en entitet. Två entiteter i samma tabell kan innehålla olika samlingar egenskaper eftersom Table storage brist scheman, och dessa egenskaper kan vara av olika typer. Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

#### <a name="queue-storage"></a>Queue Storage
Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

### <a name="keyvault"></a>KeyVault
KeyVault RP ger hantering och granskning av hemligheter som lösenord och certifikat. Exempelvis kan en klient använda KeyVault RP ange administratörers lösenord eller nycklar under distribution av virtuella datorer.

## <a name="high-availability-for-azure-stack"></a>Hög tillgänglighet för Azure Stack
För att uppnå hög tillgänglighet för ett system för produktion av flera virtuella datorer i Azure virtuella datorer är placerade i en [tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) som sprider ut dem över flera feldomäner och uppdateringsdomäner. I mindre skala i Azure Stack definieras en feldomän i en tillgänglighetsuppsättning som en nod i skalningsenheten.  

Infrastrukturen i Azure Stack är redan elastisk vid fel, medför den underliggande tekniken (redundanskluster) fortfarande vissa avbrott för virtuella datorer på en fysisk server som påverkas om det uppstår ett maskinvarufel. Azure Stack har stöd för att ha en tillgänglighetsuppsättning med högst tre feldomäner för att överensstämma med Azure.

- **Feldomäner**. Virtuella datorer placeras i en tillgänglighetsuppsättning kommer att fysiskt isolerade från varandra genom att sprida dem så jämnt som möjligt via flera feldomäner (Azure Stack-noder). Om det finns ett maskinvarufel, kommer virtuella datorer från den misslyckade feldomänen att startas om i andra feldomäner, men om möjligt sparas i separata feldomäner från andra virtuella datorer i samma tillgänglighetsuppsättning. När maskinvaran som är online igen, kommer du genomförs virtuella datorer för att upprätthålla hög tillgänglighet. 
 
- **Uppdateringsdomäner**. Uppdateringsdomäner är en annan Azure koncept som ger hög tillgänglighet i tillgänglighetsuppsättningar. En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas på samma gång. Virtuella datorer finns i samma uppdateringsdomän startas tillsammans under planerat underhåll. När klienter skapar virtuella datorer i en tillgänglighetsuppsättning, distribuerar Azure-plattformen automatiskt virtuella datorer mellan dessa uppdateringsdomäner. I Azure Stack, virtuella datorer är live migreras på andra online värdar i klustret innan deras underliggande värden har uppdaterats. Eftersom det inte finns inget klient-avbrott under en värduppdatering av, finns bara funktionen domän i Azure Stack mall kompatibilitet med Azure. 

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan använda RBAC för att ge systemåtkomst till behöriga användare, grupper och tjänster genom att tilldela dem roller på en prenumeration, resursgrupp eller enskild resurs. Varje roll definierar den åtkomstnivå som en användare, grupp eller tjänst har över Microsoft Azure Stack-resurser.

RBAC i Azure har tre grundläggande roller som gäller för alla typer av resurser: Ägare, deltagare och läsare. Ägare har fullständig åtkomst till alla resurser inklusive rätten att delegera åtkomst till andra. Deltagare kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra. Läsare kan bara visa befintliga Azure-resurser. Resten av RBAC-roller i Azure kan hanteringen av specifika Azure-resurser. Exempelvis kan rollen virtuell Datordeltagare kan skapandet och hanteringen av virtuella datorer, men tillåter inte hantering av det virtuella nätverket eller undernätet som den virtuella datorn ansluter till.

## <a name="usage-data"></a>Användningsdata
Microsoft Azure Stack samlar in och sammanställer användningsdata över alla resursproviders och skickar den till Azure för bearbetning av Azure commerce. Användningsdata som samlas in på Azure Stack kan ses via ett REST-API. Det finns en Azure-konsekventa klient-API som Provider och delegerad Provider API: er att hämta användningsdata för alla klient-prenumerationer. Dessa data kan användas för att integrera med ett externt verktyg eller en tjänst för fakturerings- eller återbetalning. När användning har behandlats av Azure commerce, kan det visas i på Azures faktureringsportal.

## <a name="next-steps"></a>Nästa steg
[Grundläggande om administration](azure-stack-manage-basics.md)

