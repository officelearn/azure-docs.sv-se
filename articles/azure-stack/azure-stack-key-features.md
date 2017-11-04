---
title: Viktiga funktioner koncept i Azure-stacken | Microsoft Docs
description: "Läs mer om viktiga funktioner och koncept i Azure-stacken."
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: fd16748e1369b8abcab38ce1945f72c681c344b8
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Viktiga funktioner och koncept i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Om du har använt Microsoft Azure-stacken kan dessa villkor och funktionsbeskrivning vara användbart.

## <a name="personas"></a>Personer
Det finns två varianter av användare för Microsoft Azure-stacken, operatorn molnet (provider) och klienten (konsument).

* En **moln operatorn** kan konfigurera Azure-stacken och hantera erbjudanden, planer, tjänster, kvoter och priser för att tillhandahålla resurser för sina klienter.  Molnoperatörer också hantera kapacitet och svara på aviseringar.  
* En **klient** (kallas även för en användare) förbrukar tjänster som molnadministratören erbjuder. Klienter kan etablera, övervaka och hantera tjänster som de har prenumererat på, till exempel Web Apps, lagring och virtuella datorer.

## <a name="portal"></a>Portalen
Metoder för att interagera med Microsoft Azure-stacken är administratörsportal, användarportalen och PowerShell.

Azure Stack-portaler har varje backas upp av separata instanser av Azure Resource Manager.  En operator för molnet använder administratörsportalen att hantera Azure-stacken och att exempelvis skapa klient erbjudanden.  Användarportalen (kallas även klientportalen) tillhandahåller en Självbetjäning för förbrukningen av molnresurser, t.ex. virtuella datorer, lagringskonton och Web Apps. Mer information finns i [med hjälp av Azure-stacken administratörs- och portaler](azure-stack-manage-portals.md).

## <a name="identity"></a>Identitet 
Azure-stacken använder Azure Active Directory (AAD) eller Active Directory Federation Services (AD FS) som en identitetsleverantör.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory är Microsofts molnbaserade, flera innehavare identitetsleverantör.  De flesta hybridscenarion använder Azure Active Directory som identitet Arkiv.

### <a name="active-directory-federation-services"></a>Active Directory Federation Services
Du kan välja att använda Active Directory Federation Services (AD FS) för frånkopplade distributioner av Azure-stacken.  Azure stacken, resursproviders och andra program fungerar på samma sätt med AD FS och Azure Active Directory. Azure-stacken innehåller sin egen instans av AD FS och Active Directory och en Active Directory Graph API. Azure-stacken Development Kit har stöd för följande AD FS-scenarier:

- Logga in på distributionen med hjälp av AD FS.
- Skapa en virtuell dator med hemligheter i Nyckelvalvet
- Skapa ett valv för lagring/åtkomst till hemligheter
- Skapa anpassade RBAC-roller i prenumerationen
- Tilldela användare till RBAC-roller för resurser
- Skapa systemomfattande RBAC-roller med hjälp av Azure PowerShell
- Logga in som en användare via Azure PowerShell
- Skapa tjänst säkerhetsobjekt använda dem för att logga in på Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regioner, tjänster, planer, erbjudanden och prenumerationer
Tjänster som levereras till klienter som använder regioner, prenumerationer, erbjudanden och planer i Azure-stacken. Klienter kan prenumerera på flera erbjudanden. Erbjudanden som kan ha en eller flera planer och planer kan ha en eller flera tjänster.

![](media/azure-stack-key-features/image4.png)

Exempel hierarkin för en klient prenumerationer erbjudanden, var och en med olika planer och tjänster.

### <a name="regions"></a>Regioner
Azure Stack-regioner är en grundläggande del av hantering och skala. En organisation kan ha flera regioner med tillgängliga resurser i varje region. Regioner kan också ha olika Tjänsterbjudanden som är tillgängliga. I Azure-stacken Development Kit en enskild region stöds och heter automatiskt *lokala*.

### <a name="services"></a>Tjänster
Microsoft Azure-stacken gör det möjligt för leverantörer att leverera en mängd olika tjänster och program, till exempel virtuella datorer, SQL Server-databaser, SharePoint, Exchange och mer.

### <a name="plans"></a>Planer
Planer är grupperingar av en eller flera tjänster. Som en leverantör, kan du skapa planer för att erbjuda dina klienter. Dina klienter prenumererar i sin tur på dina erbjudanden för att använda de planer och tjänster som ingår i dem.

Varje tjänst som lagts till i en plan kan konfigureras med kvotinställningar som hjälper dig att hantera din kapacitet. Kvoter kan innehålla begränsningar, till exempel VM, RAM-minne och processor gränser och tillämpas per användare prenumeration. Kvoter kan skiljas åt av platsen. En plan som innehåller beräknings-tjänster från Region A kan till exempel ha en kvot på två virtuella datorer, 4 GB RAM-minne och 10 CPU-kärnor.

När du skapar ett erbjudande tjänstadministratören kan innehålla en **Basplan**. Dessa basen planer ingår som standard när en klient som prenumererar på det erbjudandet. När en användare prenumererar (och prenumerationen skapas) har användaren åtkomst till alla providrar som anges i dessa basen planer (med motsvarande kvoter).

Administratören kan även inkludera **tillägg planer** i ett erbjudande. Tillägget planer ingår inte som standard i prenumerationen. Tillägget är ytterligare scheman (kvoter) tillgängliga i ett erbjudande som en prenumeration ägare kan lägga till sina prenumerationer.

### <a name="offers"></a>Erbjudande
Erbjudanden är grupper med en eller flera serviceplaner providers presentera för klienter att köpa (prenumerera på). Erbjuder Alpha kan till exempel innehålla planera en som innehåller en uppsättning tjänster för beräkning och planera B som innehåller en uppsättning tjänster för lagring och nätverk.

Ett erbjudande levereras med en uppsättning basen planer och administratörer kan skapa tillägg planer som klienter kan lägga till prenumerationen.

### <a name="subscriptions"></a>Prenumerationer
En prenumeration är hur klienter köpa erbjudandena. En prenumeration är en kombination av en klient med ett erbjudande. En klient kan ha flera erbjudanden prenumerationer. Varje prenumeration gäller endast ett erbjudande. En klient prenumerationer fastställa vilka tjänster eller det planer för att få åtkomst till.

Prenumerationer kan organisera och åtkomst till molnresurser och tjänster.

För administratören skapas en prenumeration på Default leverantör under distributionen. Den här prenumerationen kan användas för att hantera Azure-stacken, distribuera ytterligare resursproviders och skapa planer och erbjudanden för klienter. Det bör inte användas för att köra kundens arbetsbelastningar och program. 

## <a name="azure-resource-manager"></a>Azure Resource Manager
Med Azure Resource Manager kan arbeta du med din för infrastrukturresurser i en mallbaserade deklarativ modell.   Det ger ett enda gränssnitt som du kan använda för att distribuera och hantera dina komponenter. Fullständig information och anvisningar finns i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Resursgrupper
Resursgrupper är samlingar av resurser, tjänster och program, och varje resurs har en typ, till exempel virtuella datorer, virtuella nätverk, offentliga IP-adresser, lagringskonton och webbplatser. Varje resurs måste finnas i en resursgrupp och så resursgrupper logiskt att ordna resurser, exempelvis av arbetsbelastningen eller platsen.  I Microsoft Azure-stacken hanteras resurser, till exempel planer och erbjuder också i resursgrupper.
 
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Du kan skapa en mall (i JSON-format) som definierar distributionen och konfigurationen av ditt program med Azure Resource Manager. Den här mallen kallas för en Azure Resource Manager-mall och tillhandahåller en deklarativ metod för att definiera distributionen. Genom att använda en mall kan du distribuera ditt program flera gånger under applivscykeln och vara säker på att dina resurser distribueras konsekvent.

## <a name="resource-providers-rps"></a>Resursproviders (RPs)
Resursproviders är webbtjänster som utgör grunden för alla Azure-baserade IaaS och PaaS-tjänster. Azure Resource Manager förlitar sig på olika RPs att ge åtkomst till tjänster.

Det finns fyra grundläggande RPs: nätverk, lagring, beräkning och KeyVault. Var och en av dessa RPs hjälper dig att konfigurera och styra sina respektive resurser. Administratörer kan också lägga till nya anpassade providers.

### <a name="compute-rp"></a>Beräkna RP
Beräkna Resource Provider (CRP) gör att Azure Stack-klienter kan skapa sina egna virtuella datorer. CRP omfattar möjligheten att skapa virtuella datorer samt tillägg för virtuell dator. Tjänsten Virtual Machine-tillägg ger IaaS-funktioner för Windows och Linux virtuella datorer.  Du kan använda CRP exempelvis för att etablera en virtuell Linux-dator och köra Bash-skript under distributionen för att konfigurera den virtuella datorn.

### <a name="network-rp"></a>Nätverket RP
Nätverket Resource Provider (NRP) ger en uppsättning funktioner för Programvarudefinierad nätverksfunktion (SDN) och nätverk funktionen virtualisering (NFV) för det privata molnet.  Du kan använda NRP för att skapa resurser som programvara belastningen belastningsutjämnare, offentliga IP-adresser, nätverkssäkerhetsgrupper, virtuella nätverk.

### <a name="storage-rp"></a>Lagring RP
Lagring RP levererar fyra konsekvent Azure storage-tjänster: blob-, tabell-, kö- och kontohantering. Det erbjuder också en lagring administration molntjänst för att underlätta administrationen av provider i Azure-konsekvent lagringstjänster. Azure Storage ger flexibilitet för att lagra och hämta stora mängder Ostrukturerade data, till exempel dokument och mediafiler med Azure BLOB och strukturerade NoSQL-baserade data med Azure-tabeller. Mer information om Azure Storage finns [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
BLOB storage lagrar alla datauppsättning. En blobb kan bestå av vilka slags textdata eller binära data som helst, till exempel ett dokument, en mediefil eller ett installationsprogram. Table storage lagrar strukturerade datauppsättningar. Table Storage är en nyckel- och attributdatabas för NoSQL som möjliggör snabb utveckling och snabb tillgång till stora mängder data. Queue storage erbjuder tillförlitlig meddelandehantering för arbetsflödesbearbetning och för kommunikation mellan komponenter i molntjänster.

Varje blobb är underordnad en behållare. Behållare är också ett praktiskt sätt att tilldela säkerhetsprinciper till grupper med objekt. Ett lagringskonto kan innehålla ett obestämt antal behållare och en behållare kan innehålla ett obestämt antal blobbar, upp till lagringskontots kapacitetsgräns på 500 TB. Blob Storage erbjuder tre typer av blobbar: blockblobbar, tilläggsblobbar och sidblobbar (diskar). Blockblobbar är optimerade för direktuppspelning och lagring av molnobjekt och är ett bra alternativ för att lagra dokument, mediefiler, säkerhetskopior osv. Tilläggsblobbar påminner om blockblobbar, men är optimerade för tilläggsåtgärder. En tilläggsblobb kan bara uppdateras genom att ett nytt block läggs till i slutet. Tilläggsblobbar är praktiska i scenarier som loggning, där nya data bara behöver skrivas till slutet av blobben. Sidblobbar är optimerade för att representera IaaS-diskar och stöd för slumpmässiga skrivningar och kan vara upp till 1 TB. En nätverksansluten IaaS-disk för en virtuell Azure-dator är en VHD som lagras som en sidblobb.

#### <a name="table-storage"></a>Table Storage
Table storage är Microsofts NoSQL-nyckel och attributdatabas – den har en design utan scheman, vilket skiljer den från traditionella relationsdatabaser. Eftersom data lagras saknas scheman, är det enkelt att anpassa dina data som behov utvecklas. Table Storage är enkelt att använda, vilket gör det lätt för utvecklare att snabbt skapa program. Table Storage är en nyckel- och attributdatabas, vilket innebär att varje värde i en tabell lagras med ett typbestämt egenskapsnamn. Egenskapsnamnet kan användas för att filtrera och ange urvalskriterier. En uppsättning egenskaper och deras värden utgör en entitet. Två entiteter i samma tabell kan innehålla olika samlingar egenskaper eftersom lagring saknas tabellscheman, och dessa egenskaper kan vara av olika typer. Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

#### <a name="queue-storage"></a>Queue Storage
Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

### <a name="keyvault"></a>KeyVault
KeyVault RP ger hantering och granskning av hemligheter, till exempel lösenord och certifikat. Exempelvis kan en klient använda KeyVault RP ange administratörslösenord eller nycklar under distribution av Virtuella datorer.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan använda RBAC för att bevilja åtkomst till systemet till behöriga användare, grupper och tjänster genom att tilldela dem roller på en prenumeration, resursgrupp eller enskild resurs. Varje roll definierar den åtkomstnivå som en användare, grupp eller tjänst har över Microsoft Azure-stacken resurser.

Azure RBAC har tre grundläggande roller som gäller för alla typer av resurser: ägare, deltagare och läsare. Ägaren har fullständig åtkomst till alla resurser som bland annat att delegera åtkomst till andra. Deltagare kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra. Läsare kan bara visa befintliga Azure-resurser. Resten av rollerna i Azure RBAC kan hanteringen av specifika Azure-resurser. Till exempel virtuella deltagarrollen tillåter skapande och hantering av virtuella datorer men tillåter inte hantering av virtuellt nätverk eller undernät som den virtuella datorn ansluter till.

## <a name="usage-data"></a>Användningsdata
Microsoft Azure-stacken samlar in aggregerar användningsdata över alla resursproviders och skickar den till Azure för bearbetning av Azure handelsdepartement. Användningsdata samlas in på Azure-stacken kan granskas via ett REST-API. Det är en Azure-konsekvent klient-API som providern och delegerad Provider-API: er att hämta användningsdata över alla klient-prenumerationer. Den här informationen kan användas för att integrera med ett externt verktyg eller en tjänst för fakturerings- eller återbetalning. När du har bearbetats användning av Azure handelsdepartement, kan det visas i Azure portal för fakturering.

## <a name="in-development-build-of-azure-stack-development-kit"></a>I development version av Azure-stacken Development Kit
I development versioner kan tidiga brukare utvärdera den senaste versionen av Azure-stacken Development Kit. De är inkrementella versioner baserat på senaste huvudversion. Större versioner kommer att fortsätta frigörs några månaders mellanrum, släpp i development versioner periodvis mellan större versioner.

I development versioner ger följande fördelar:
- Felkorrigeringar
- Nya funktioner
- Andra förbättringar

## <a name="next-steps"></a>Nästa steg
[Kraven för distribution av Azure Stack](azure-stack-deploy.md)

