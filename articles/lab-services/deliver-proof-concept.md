---
title: Leverera ett konceptbevis - Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du levererar ett konceptbevis så att Azure DevTest Labs kan införlivas i en företagsmiljö.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643295"
---
# <a name="deliver-a-proof-of-concept"></a>Leverera ett konceptbevis 

Ett av de viktigaste scenarierna för Azure DevTest Labs är att aktivera utvecklings- och testmiljöer i molnet. Exempel på rekommendationer:

* Skapa utvecklarskrivbord i molnet.
* Konfigurera miljöer för testning.
* Aktivera åtkomst till virtuella datorer och andra Azure-resurser.
* Konfigurera ett sandlådeområde där utvecklare kan lära sig och experimentera.

DevTest Labs tillhandahåller också policyskyddsräcken och kostnadskontroller för att göra det möjligt för företaget att tillhandahålla "azure" som fungerar som egenföretagare och utvecklare som följer företagets säkerhets-, regel- och efterlevnadsprinciper. 

Varje företag har olika krav på hur Azure DevTest Labs kan införlivas i sin miljö. I den här artikeln beskrivs de vanligaste stegen som företag behöver slutföra för att säkerställa ett framgångsrikt konceptbevis. Ett proof of concept är det första steget mot en lyckad end-to-end-distribution. 

## <a name="getting-started"></a>Komma igång 

För att komma igång med att leverera ett proof of concept. Det är viktigt att spendera lite tid att lära sig mer om Azure och DevTest Labs.  Här är några startresurser: 

* [Förstå Azure-portalen](https://azure.microsoft.com/features/azure-portal/)
* [Grunderna i DevTest Labs](devtest-lab-overview.md)
* [Scenarier som stöds av DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentation för DevTest Labs-företag](devtest-lab-guidance-prescriptive-adoption.md)
* [Intro till Azure-nätverk](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Krav 

För att framgångsrikt slutföra en pilot eller proof of concept med DevTest Labs, det finns några förutsättningar: 

* **Azure-prenumeration:** Företag har ofta ett befintligt [Enterprise-avtal](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) på plats som ger åtkomst till Azure och de kan använda en befintlig eller ny prenumeration för DevTest Labs. Alternativt kan företag använda en [Visual Studio-prenumeration](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) under pilotprojektet (dra nytta av de kostnadsfria Azure-krediterna). Om inget av dessa alternativ är tillgängligt kan ett företag skapa och använda ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ). Om det finns ett enterprise-avtal på plats är det ett bra alternativ att använda en [Enterprise Dev/Test-prenumeration](https://azure.microsoft.com/offers/ms-azr-0148p/) för att få tillgång till Windows 10/Windows 8.1-klientoperativsystem och rabatterade priser för utvecklings- och testningsarbetsbelastningar. 
* **Azure Active Directory-klient:** För att kunna hantera användare (till exempel lägga till användare eller lägga till labbägare) måste dessa användare vara en del av [Azure Active Directory-klienten](https://azure.microsoft.com/services/active-directory/) som används i Azure-prenumerationen för piloten. Ofta kommer företag att ställa in [hybrididentitet](../active-directory/hybrid/whatis-hybrid-identity.md) så att användarna kan använda sin lokala identitet i molnet, men detta krävs inte för DevTest Labs-piloten. 

## <a name="scoping-of-the-pilot"></a>Scoping av piloten 

Det är viktigt att planera en pilot innan du påbörjar implementeringen. Att veta i förväg att resurserna inte kommer att stanna kvar på obestämd tid ställer lämpliga förväntningar för användare av piloten. 

> [!IMPORTANT]
> Vi kan inte nog betona vikten av att skarpt scoping piloten och ställa förväntningar på framsidan.

Svara på dessa viktiga frågor innan du sparkar igång piloten: 

* Hur vill du lära dig, och hur ser framgång ut för piloten? 
* Vilka arbetsbelastningar eller scenarier kommer att täckas i pilotprojektet? Det är viktigt att definiera endast en liten uppsättning för att säkerställa att piloten kan scoped och slutföras snabbt. 
* Vilka resurser måste vara tillgängliga i labbet? Till exempel: anpassade avbildningar, marketplace-bilder, principer, nätverkstopologi. 
* Vilka är de användare och team som kommer att delta i piloten för att verifiera upplevelsen?  
* Hur länge ska piloten pågå? Välj en tidsram som anpassar sig väl till planerat scope, till exempel två veckor eller en månad. 
* När pilotprojektet är klart, vad händer med de tilldelade resurser som användes under pilotprojektet? Planerar du att kassera pilotresurserna? Du kanske tror:
   
   "Om vi kan planera att kasta bort virtuella maskiner och laboratorier i slutet av piloten, då kan vi ställa in en enda prenumeration för piloten och göra allt vårt arbete där medan vi lösa skala utbyggnaden frågor parallellt." 

Det finns en allmän tendens att göra piloten "perfekt" så det identiskt representerar vad det slutliga tillståndet kommer att vara efter tjänsten rullas ut på företaget. Detta är ett falskt antagande. Ju närmare du kommer till "perfekt", desto mer måste du slutföra *innan* du börjar på piloten. Syftet med piloten är att fatta rätt beslut om att skala upp och rulla ut den slutliga tjänsten. 

Pilotens fokus bör ligga på att välja de minsta nödvändiga arbetsbelastningarna och beroendena för att svara på frågan om Azure DevTest Labs är rätt tjänst för ditt företag. Vi rekommenderar att du väljer den enklaste arbetsbelastningen med minst beroenden för att säkerställa en snabb och ren framgång. Om det inte är möjligt väljer du en mest representativ arbetsbelastning som exponerar potentiella komplexiteter så att framgång i pilotfasen kan replikeras i utskalningsfasen. 

Följande exempel visar ett välscopet konceptbevis. 

## <a name="example-proof-of-concept-plan"></a>Exempel: proof-of-concept plan 

I det här avsnittet visas ett exempel som ska användas för att omfånga ett konceptbevis för piloten för DevTest Labs. 

> [!TIP]
> För att minimera risken för att projektet kan konfigureras för fel rekommenderar vi starkt att du inte hoppar över exemplet som beskrivs i det här avsnittet. 

### <a name="overview"></a>Översikt 

Vi planerar att utveckla en ny miljö i Azure baserat på DevTest Labs för leverantörer att använda som en isolerad miljö från företagsnätverket. För att avgöra om lösningen uppfyller kraven utvecklar vi ett konceptbevis för att validera lösningen från slutna till slutna. Vi har inkluderat flera leverantörer för att prova och verifiera upplevelsen. 

### <a name="outcomes"></a>Resultat 

När vi bygger ett konceptbevis fokuserar vi först på resultaten (vad försöker vi uppnå). I slutet av proof of concept förväntar vi oss: 

* En fungerande på sluten lösning för leverantörer att använda gästkonton i Azure Active Directory (Azure AD) för att komma åt en isolerad miljö i Azure. Miljön har de resurser som krävs för att de ska vara produktiva. 
* Eventuella blockeringsproblem som påverkar bredare användning och införande räknas upp och förstås.
* De individer som deltar i utvecklingen av proof of concept har en god förståelse för all kod. De förstår också säkerheter inblandade och är övertygade om bredare antagande.

### <a name="open-questions-and-prerequisites"></a>Öppna frågor och förutsättningar 

* Har vi skapat en prenumeration som vi kan använda för det här projektet? 
* Har vi identifierat en Azure AD-klient och en global Azure AD-administratör som kan ge hjälp och vägledning för Azure AD-relaterade frågor? 
* Har vi plats att samarbeta för de personer som arbetar med projektet? 

   * Källkod och skript (som Azure Repos) 
   * Dokument (som Microsoft Teams eller SharePoint)  
   * Konversationer (som Microsoft Teams) 
   * Arbetsobjekt (till exempel Azure Boards) 
* Vilka är de resurser som krävs för leverantörer? Detta inkluderar program som är tillgängliga i nätverket, både lokalt på de virtuella datorerna och på andra servrar som krävs. 
* Kommer de virtuella datorerna att anslutas till en domän i Azure? Om så är fallet, kommer detta att vara Azure Active Directory Domain Services (Azure AD DS) eller något annat? 
* Har vi identifierat det eller de leverantörer som kommer att vara föremål för proof of concept? Vilka kommer att vara kunder för miljön?
* Vilken Azure-region kommer vi att använda för proof of concept? 
* Har vi en lista över tjänster som leverantörerna får använda via DevTest Labs förutom IaaS (VMs)? 
* Hur planerar vi att utbilda leverantörer / användare på att använda labbet? 

### <a name="components-of-the-proof-of-concept-solution"></a>Komponenter i proof-of-concept-lösningen 

Vi förväntar oss att lösningen har följande komponenter: 

* Olika leverantörsteam använder en uppsättning labb i Azure.
* Labben är anslutna till en nätverksinfrastruktur som stöder kraven.
* Leverantörerna har åtkomst till labben via Azure AD och rolltilldelningar.
* Leverantörer har ett sätt att ansluta till sina resurser. En VPN från plats till plats gör det möjligt att komma åt virtuella datorer direkt utan offentliga IP-adresser.
* En uppsättning artefakter täcker den programvara som krävs som leverantörerna behöver på de virtuella datorerna.

## <a name="additional-planning-and-design-decisions"></a>Ytterligare planerings- och designbeslut 

Innan du släpper en fullständig DevTest Labs-lösning måste du fatta några viktiga planerings- och designbeslut. Erfarenheten av att arbeta med ett proof of concept kan hjälpa dig att fatta dessa beslut. Ytterligare övervägande omfattar: 

* **Prenumerationstopologi:** Kraven på företagsnivå för resurser i Azure kan sträcka sig längre än de [tillgängliga kvoterna inom en enda prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Detta kräver flera Azure-prenumerationer och/eller tjänstbegäranden för att öka de ursprungliga prenumerationsgränserna. Det är viktigt att bestämma i förväg hur resurser ska fördelas mellan prenumerationer. En värdefull resurs är [beslutsguiden](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) för prenumerationefteråt eftersom det är svårt att flytta resurser till en annan prenumeration senare. Ett labb kan till exempel inte flyttas till en annan prenumeration när det har skapats.  
* **Nätverkstopologi**: [Standardnätverksinfrastrukturen](../app-service/networking-features.md) som DevTest Labs automatiskt skapar kanske inte är tillräcklig för att uppfylla kraven och begränsningarna för företagsanvändarna. Det är vanligt att se [Azure ExpressRoute-anslutna virtuella nätverk,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) för anslutning mellan prenumerationer och till och med [tvingad routning](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) för att säkerställa endast lokal anslutning. DevTest Labs gör det möjligt för befintliga virtuella nätverk att anslutas till labbet för att aktivera deras användning när du skapar nya virtuella datorer i labbet. 
* **Fjärråtkomst för virtuella datorer:** Det finns många alternativ för att fjärransluta till de virtuella datorerna som finns i DevTest Labs. Det enklaste är att använda offentliga IPs eller delade offentliga IPs. Dessa är [inställningar som är tillgängliga i labbet](devtest-lab-shared-ip.md). Om dessa alternativ inte är tillräckliga är det också ett alternativ att använda en fjärråtkomstgateway. Det här alternativet visas i [DevTest Labs företagsreferensarkitektur](devtest-lab-reference-architecture.md) och beskrivs ytterligare i [dokumentationen för DevTest Labs-fjärrskrivbordsgateway](configure-lab-remote-desktop-gateway.md). Företag kan också använda ExpressRoute eller en plats-till-plats-VPN för att ansluta sina labb till sitt lokala nätverk. Det här alternativet möjliggör direkt fjärrskrivbord eller SSH-anslutningar till de virtuella datorerna baserat på deras privata IP-adress utan exponering för Internet. 
* **Hanteringsbehörigheter**: De två nyckelbehörigheter som vanligen används i DevTest Labs är [ägare och labbanvändare](devtest-lab-add-devtest-user.md). Det är viktigt att bestämma innan du rullar ut DevTest Labs brett som kommer att anförtros varje nivå av tillgång i labbet. En vanlig modell är budgetägaren (till exempel teamlead) som labbägare och gruppmedlemmar som labbanvändare. Den här modellen gör det möjligt för den person (teamlead) som ansvarar för budgeten att justera principinställningarna och hålla teamet inom budget.  

## <a name="completing-the-proof-of-concept"></a>Slutföra proof of concept 

Efter att de förväntade lärdomarna har täckts är det dags att slutföra piloten. Det är dags att samla in feedback från användarna, avgöra om piloten lyckades och bestämma om organisationen ska gå vidare på en skalningsutbyggnad av DevTest Labs i företaget. Det är också ett bra tillfälle att överväga att automatisera distribution av DevTest Labs och tillhörande resurser för att säkerställa konsekvens i hela skalan utbyggnaden. 

## <a name="next-steps"></a>Nästa steg 

* [Dokumentation för DevTest Labs-företag](devtest-lab-guidance-prescriptive-adoption.md)
* [Referensarkitektur för ett företag](devtest-lab-reference-architecture.md)
* [Skala upp distributionen av DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
* [Dirigera implementeringen av Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
