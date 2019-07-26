---
title: Leverera ett koncept bevis – Azure DevTest Labs | Microsoft Docs
description: Lär dig att leverera ett koncept bevis så Azure DevTest Labs kan integreras i en företags miljö.
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
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501551"
---
# <a name="delivering-a-proof-of-concept"></a>Att leverera ett koncept bevis 

Ett av de viktigaste scenarierna för Azure DevTest Labs är att aktivera utvecklings-och testnings miljöer i molnet. Exempel:

* Skapa skriv bord för utvecklare i molnet,
* Konfigurera miljöer för testning,
* Aktivera åtkomst till virtuella datorer och andra Azure-resurser
* Konfigurera ett begränsat områden för utvecklare att lära sig och experimentera.

DevTest Labs innehåller också principen guardrails och kostnads kontroller som gör det möjligt för företaget att tillhandahålla "självbetjänings Azure" till utvecklare som följer företagets principer för säkerhet, regler och efterlevnad. 

Alla företag har olika krav för hur Azure DevTest Labs kan införlivas i deras miljö. I den här artikeln beskrivs de vanligaste stegen som företag ofta måste utföra för att säkerställa ett välkomst koncept, vilket är det första steget mot en lyckad distribution av slut punkt till slut punkt. 

## <a name="getting-started"></a>Komma igång 

För att komma igång med att leverera ett koncept bevis. Det är viktigt att ägna lite tid åt att lära dig mer om Azure och DevTest Labs.  Här är några start resurser: 

* [Förstå Azure Portal](https://azure.microsoft.com/features/azure-portal/)
* [Grunderna för DevTest Labs](devtest-lab-overview.md)
* [Scenarier som stöds av DevTest Labs](devtest-lab-guidance-get-started.md)
* [Dokumentation om DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Introduktion till Azure-nätverk](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Förutsättningar 

Om du vill slutföra en pilot eller ett koncept bevis med DevTest Labs krävs det några krav: 

* **Azure-prenumeration**: Företag har ofta en befintlig [Enterprise-avtal](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) på plats som ger åtkomst till Azure och en befintlig eller ny prenumeration kan användas för DevTest Labs. Alternativt kan en [Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) användas under piloten (med de kostnads fria Azure-krediterna). Om inget av dessa alternativ är tillgängligt kan ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) skapas och användas. Om det finns en Enterprise-avtal på plats är användningen av en [Enterprise dev/test-prenumeration](https://azure.microsoft.com/offers/ms-azr-0148p/) ett bra alternativ för att få åtkomst till Windows 10/Windows 8,1-klient operativ system och rabatterat pris för utveckling och testning av arbets belastningar. 
* **Azure Active Directory klient**:  För att kunna hantera användare (till exempel lägga till användare eller lägga till labb ägare) måste dessa användare vara en del av den [Azure Active Directory klient organisationen](https://azure.microsoft.com/services/active-directory/) som används i Azure-prenumerationen för piloten. Företag konfigurerar ofta [Hybrid identiteter](../active-directory/hybrid/whatis-hybrid-identity.md) så att användarna kan utnyttja sin lokala identitet i molnet, men detta krävs inte för DevTest Labs-piloten. 

## <a name="scoping-of-the-pilot"></a>Omfånget för piloten 

Det är mycket viktigt att planera en korrekt pilot innan du påbörjar implementeringen. I förväg vet du att resurserna inte stannar på obestämd tid, ställer in lämpliga förväntningar för användare av piloten. 

> [!IMPORTANT]
> Vi kan inte framhäva tillräckligt mycket av att klara pilot-och inställnings förväntan.

Det finns viktiga frågor som ska besvaras innan piloten påbörjas: 

* Vad vill du veta och vad ser det ut som för piloten? 
* Vilka arbets belastningar eller scenarier kommer att omfattas av piloten? Det är viktigt att endast definiera en liten uppsättning för att se till att piloten kan komma att omfångs och slutföras snabbt. 
* Vilka resurser måste vara tillgängliga i labbet? Exempel: anpassade avbildningar, Marketplace-avbildningar, principer, nätverkstopologi osv. 
* Vilka är slutanvändare/team som kommer att ingå i piloten för att verifiera erfarenheten?  
* Vad är pilotens varaktighet? Välj en tidsram som justeras bra till planerat omfång, till exempel två veckor eller en månad. 
* Vad händer när piloten är slutförd, vad händer med de tilldelade resurserna som användes under piloten? Planerar du att ta bort pilot resurserna?
   
   Om vi kan planera för att ta bort de virtuella datorerna och labben i slutet av piloten kan vi helt enkelt konfigurera en enda prenumeration för piloten och göra allt det där, medan vi löser problemet med att skala distributionen parallellt. 

Det finns en allmän tendens att göra piloten "perfekt" så att den motsvarar det slutliga tillstånd som kommer att ske när tjänsten distribueras på företaget. Detta är ett falskt antagande. Närmare att du kommer till "perfekt" desto mer du behöver göra *innan* du börjar använda piloten. Syftet med piloten är att fatta rätt beslut om att skala upp och ut ur den slutliga tjänsten. 

Pilotens fokus bör vara att välja de absoluta minimi kraven på arbets belastningar och beroenden för att kunna besvara frågan om Azure DevTest Labs är rätt tjänst för ditt företag. Vi rekommenderar att du väljer den enklaste arbets belastningen med minst beroenden för att se till att det går snabbare och rent klart. Om detta inte är möjligt är nästa bästa alternativ att välja en mest representativ arbets belastning som visar potentiella komplicerade risker, så att framgång i pilot fasen kan replikeras i den skalbara fasen. 

I följande exempel visas ett välkänt koncept bevis. 

## <a name="example-proof-of-concept-plan"></a>Exempel: koncept bevis plan 

Det här avsnittet innehåller ett exempel som du kan använda för att omfånget för ett koncept bevis för piloten för DevTest Labs. 

> [!TIP]
> Vi rekommenderar starkt att du inte hoppar över exemplet som beskrivs i det här avsnittet för att minimera risken för att skapa ett projekt för haveri. 

### <a name="overview"></a>Översikt 

Vi planerar att utveckla en ny miljö i Azure baserat på DevTest Labs för leverantörer som ska användas som isolerad miljö från företags nätverket. För att avgöra om lösningen uppfyller kraven kommer vi att utveckla ett koncept bevis för att validera slut punkt till slut punkt och har inkluderat flera leverantörer för att testa och kontrol lera upplevelsen. 

### <a name="outcomes"></a>Resultat 

När vi skapar ett POC-koncept fokuserar vi först på resultatet (Vad försöker vi uppnå) – de visas här.  I slutet av koncept beviset förväntas vi: 

* En komplett lösning för att utnyttja gäst konton i Azure Active Directory (Azure AD) för att få åtkomst till en isolerad miljö i Azure som har de resurser som krävs för att de ska vara produktiva. 
* Eventuella spärrnings problem som påverkar en bredare skalnings användning och införande räknas upp och förstås.
* De personer som sysslar med att utveckla POC-beviset har en god förståelse för all kod. De kan också förstå vad som är viktigt och är tryggare införande.

### <a name="open-questions--prerequisites"></a>Öppna frågor & krav 

* Har vi en prenumeration som vi har skapat som vi kan använda för det här projektet? 
* Har vi en Azure AD-klient och en global Azure AD-administratör identifieras som kan ge hjälp & vägledning för Azure AD-relaterade frågor? 
* En plats för att samar beta för de personer som arbetar med projektet: 

   * Källkod och skript (som Azure databaser) 
   * Dokument (t. ex. team eller SharePoint)  
   * Konversationer (t. ex. Microsoft-team) 
   * Arbets objekt (till exempel Azure-kort) 
* Vilka är de resurser som krävs för leverantörer? Detta inkluderar både lokalt på de virtuella datorerna och andra nödvändiga servrar, program som är tillgängliga i nätverket. 
* Kommer de virtuella datorerna att vara anslutna till en domän i Azure? I så fall, kommer detta att Azure AD Domain Services eller något annat? 
* Har vi fått de team/leverantörer som har identifierats som är målet för koncept beviset? Vilka är kunderna för miljön?
* Vilken Azure-region kommer vi att använda för koncept beviset? 
* Har vi en lista över tjänster som leverantörerna får använda via DevTest Labs förutom IaaS (VM)? 
* Hur planerar vi för att utbilda leverantörer/användare om att använda labbet? 

### <a name="proof-of-concept-solution-components"></a>Bevis på koncept lösnings komponenter 

Vi förväntar oss att lösningen ska ha följande komponenter: 

* En uppsättning DevTest Labs i Azure för olika leverantörs team.
* Labben är anslutna till en nätverks infrastruktur som uppfyller kraven.
* Leverantörerna har till gång till labben via Azure AD och beviljar roll tilldelningar till labbet.
* Ett sätt för leverantörer att kunna ansluta till sina resurser. Mer specifikt är det en plats-till-plats-VPN som möjliggör åtkomst till virtuella datorer direkt utan offentliga IP-adresser.
* En uppsättning artefakter som behandlar den program vara som krävs av leverantörerna på de virtuella datorerna.

## <a name="additional-planning-and-design-decisions"></a>Ytterligare planerings-och design beslut 

Innan du släpper en fullständig DevTest Labs-lösning finns det några viktiga planerings-och design beslut som kan fattas. Erfarenheten av att arbeta med koncept bevis kan hjälpa dig att fatta besluten. Ytterligare överväganden omfattar: 

* **Prenumerations sto pol Ogin**: Kraven på företags nivå för resurser i Azure kan utökas utöver de [tillgängliga kvoterna i en enda prenumeration](https://docs.microsoft.com/azure/azure-subscription-service-limits), vilket kräver flera Azure-prenumerationer och/eller tjänst begär Anden för att öka den inledande prenumerations gränsen. Det är viktigt att du bestämmer dig för att distribuera resurser över prenumerationer, en värdefull resurs är [prenumerations besluts guiden](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) eftersom det är svårt att flytta resurser till en annan prenumeration senare. Ett DevTest-labb kan till exempel inte flyttas till en annan prenumeration när den har skapats.  
* **Nätverkstopologi**: [Standard nätverks infrastrukturen](../app-service/networking-features.md) som skapas automatiskt av DevTest Labs kanske inte räcker för att uppfylla kraven och begränsningarna för företags användare. Det är vanligt att se [ExpressRoute anslutna virtuella nätverk](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), [hubb och eker](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) för anslutning mellan prenumerationer och även Tvingad [routning](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) som garanterar lokal anslutning. Med DevTest Labs kan befintliga virtuella nätverk anslutas till labbet så att de kan användas för att skapa nya virtuella datorer i labbet. 
* **Fjärråtkomst till Virtual Machines**: Det finns många alternativ för att fjärrans luta till de virtuella datorer som finns i DevTest Labs. Det enklaste sättet att använda offentliga IP-adresser eller delade offentliga IP-adresser är de inställningar som är [tillgängliga i labbet](devtest-lab-shared-ip.md). Om dessa alternativ inte räcker, är det också ett alternativ som visas i [DevTest Labs Enterprise-referens arkitektur](devtest-lab-reference-architecture.md) och som beskrivs ytterligare i [dokumentationen för DevTest Labs Remote Desktop Gateway](configure-lab-remote-desktop-gateway.md). Företag kan också använda Express Route eller en plats-till-plats-VPN för att ansluta sina DevTest Labs till sitt lokala nätverk. Detta möjliggör direkt fjärr skrivbords anslutning eller SSH-anslutningar till Virtual Machines baserat på deras privata IP-adress utan någon exponering för Internet. 
* **Hanterings behörigheter**: De två nyckel behörigheter som ofta används i DevTest Labs är ["ägare" och "labb användare"](devtest-lab-add-devtest-user.md). Det är viktigt att bestämma innan du utvärderar DevTest Labs i stort sett vem som ska anförtros varje nivå av åtkomst i labbet. En gemensam modell är budget ägaren (till exempel grupp ledare) som labb ägare och grupp medlemmar som labb användare. Detta gör det möjligt för den person (grupp ledare) som ansvarar för budgeten att justera princip inställningarna för att hålla teamet i budget.  

## <a name="completing-the-proof-of-concept"></a>Slutför koncept beviset 

När förväntad information har täckts är det dags att packa upp och slutföra piloten. Detta är den tid det tar att samla in feedback från användarna, avgöra om piloten lyckades och bestämma om organisationen ska gå vidare vid en skalnings lansering av DevTest Labs i företaget. Det är också en bra stund att överväga att automatisera distributionen av DevTest Labs och tillhör ande resurser för att säkerställa konsekvens i hela skalan. 

## <a name="next-steps"></a>Nästa steg 

* [Dokumentation om DevTest Labs Enterprise](devtest-lab-guidance-prescriptive-adoption.md)
* [Referens arkitektur för ett företag](devtest-lab-reference-architecture.md)
* [Skala upp din DevTest Labs-distribution](devtest-lab-guidance-orchestrate-implementation.md)
* [Dirigera implementeringen av Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
