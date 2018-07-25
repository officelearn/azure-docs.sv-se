---
title: Om Azure Lab Services | Microsoft Docs
description: Lär dig hur Lab Services kan göra det enkelt att skapa, hantera och skydda labb med virtuella datorer som kan användas av utvecklare, testare, lärare, elever och andra.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: cb957346cb0e003e9aff19026ed81495abf432c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049385"
---
# <a name="an-introduction-to-azure-lab-services"></a>En introduktion till Azure Lab Services
Med Azure Lab Services kan du snabbt konfigurera en miljö för ditt team (till exempel en utvecklingsmiljö, en testmiljö eller en labbmiljö i klassrummet) i molnet. En labbägare skapar ett labb, etablerar Windows- eller Linux-datorer, installerar nödvändig programvara och verktyg och gör dem tillgängliga för labbanvändare. Labbanvändarna ansluter till virtuella datorer (VM) i labbet och använder dem för sitt dagliga arbete, kortare projekt eller för att göra klassrumsövningar. När användarna börjar använda resurser i labbet, kan en labbadministratör analysera kostnaden och användningen för flera labb och ange övergripande principer för att optimera din organisations eller ditt teams kostnader.

> [!IMPORTANT]
> **Azure DevTest Labs** utökas med nya typer av labb (Azure Lab Services)! 
> 
> Med Azure Lab Services kan du skapa hanterade labb, till exempel labb i klassrummet. Tjänsten hanterar all infrastrukturhantering för ett hanterat labb, från att skapa virtuella datorer till att hantera fel och skala infrastrukturen. De hanterade labben finns för närvarande i en förhandsversion. När förhandsversionen löper ut, sammanförs de nya labbtyperna och befintliga DevTest Labs under det nya gemensamma samlingsnamnet Azure Lab Services där alla typer av labb kommer fortsätta att utvecklas. 

## <a name="key-capabilities"></a>De viktigaste funktionerna
Azure Lab Services stöder följande viktiga funktioner: 

- **Snabb och flexibel konfiguration av ett labb**. Med Azure Lab Services kan labbägare snabbt konfigurera ett labb efter behov. Tjänsten erbjuder möjligheten till full hantering av allt Azure-infrastrukturarbete för hanterade labb eller så kan labbägarna själva hantera och anpassa infrastrukturen inom ramarna för labbägarens prenumeration. Tjänsten tillhandahåller inbyggd skalning och motståndskraftig infrastruktur för labb som tjänsten hanterar åt dig. 
- **Förenklad upplevelse för labbanvändare**. I ett hanterat labb, som ett klassrumslabb, kan labbanvändarna registrera sig för ett labb med en registreringskod och få åtkomst till labbet när som helst för att använda labbresurserna. I ett labb som skapats i DevTest Labs, kan labbägaren ge behörighet till labbanvändarna för att skapa och få åtkomst till virtuella datorer, hantera och återanvända datadiskar och konfigurera återanvändbara hemligheter.  
- **Kostnadsoptimering och analys**. En labbägare kan ställa in labbscheman för automatisk start och avstängning av virtuella datorer. Labbägaren kan ställa in ett schema för att specificera de tider när labbets virtuella datorer ska vara tillgängliga för användarna, ställa in användningsprinciper per användare eller per labb för att optimera kostnaden och analysera användnings- och aktivitetstrender för ett labb. För hanterade labb, som klassrumslabb, finns för närvarande en mindre deluppsättning av alternativ för kostnadsoptimering och -analys tillgängliga. 
- **Inbäddad säkerhet**. En labbägare kan konfigurera ett privat virtuellt nätverk och ett undernät för ett labb och aktivera en delad offentlig IP-adress. Labbanvändarna får säker åtkomst till resurserna via det virtuella nätverk som konfigurerats med ExpressRoute eller VPN för plats-till-plats (för närvarande tillgängligt endast i DevTest Labs).
- **Integration i dina arbetsflöden och verktyg**. Med Azure Lab Services kan du integrera labb i din organisations webbplats och hanteringssystem. Du kan automatiskt etablera miljöer inifrån dina verktyg för kontinuerlig integration/kontinuerlig distribution (för närvarande tillgängligt endast i DevTest Labs).

> [!NOTE]
> Azure Lab Services stöder för närvarande endast virtuella datorer som skapas med Azure Marketplace-avbildningar. Om du vill använda anpassade avbildningar eller skapa andra PaaS-resurser i en labbmiljö kan du använda DevTest Labs. Mer information finns i dokumentationen om att [skapa en anpassad avbildning i DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) och att [skapa labbmiljöer med hjälp av Resource Manager-mallar](devtest-lab-create-environment-from-arm.md). 

## <a name="scenarios"></a>Scenarier
Här följer några scenarier som Azure Lab Services stödjer: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Konfigurera ett ändringsbart labb i molnet för ditt klassrum  

- Skapa ett hanterat klassrumslabb. Du berättar bara för tjänsten exakt vad du behöver, så skapar och hanterar den labbets infrastruktur åt dig så att du kan fokusera på att undervisa din klass istället för att lägga tid på labbets tekniska detaljer. 
- Förse eleverna med ett labb med virtuella datorer som är konfigurerat med exakt det som behövs för en lektion. Ge varje elev ett begränsat antal timmar för användning av de virtuella datorerna för skolarbete.  
- Flytta din skolas fysiska datorlabb till molnet. Skala automatiskt antalet virtuella datorer endast efter den maximala användning och de kostnadströsklar som du anger för labbet. 
- Ta bort labbet med en enda klickning när du är klar. 

### <a name="use-devtest-labs-for-development-environments"></a>Använda DevTest Labs för utvecklingsmiljöer 
Azure DevTest Labs kan användas för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för utvecklingsdatorer för utvecklare. I det här scenariot har DevTest Labs följande fördelar: 

- Ger utvecklare möjligheten att snabbt etablera sina utvecklingsdatorer på begäran.
- Etablerar Windows- och Linus-miljöer med återanvändbara mallar och artefakter.
- Utvecklare kan enkelt anpassa sina utvecklingsdatorer när så behövs.
- Administratörer kan kontrollera kostnaderna genom att säkerställa att utvecklare inte kan få fler virtuella datorer än de behöver för utvecklingen och att de stängs av när inte används. 

Mer information finns i [Använda DevTest Labs för utveckling](devtest-lab-developer-lab.md). 

### <a name="use-devtest-labs-for-test-environments"></a>Använda DevTest Labs för testmiljöer
Du kan använda Azure DevTest Labs för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för datorer för testare. I det här scenariot har DevTest Labs följande fördelar:

- Testarna kan testa den senaste versionen av sitt program genom att snabbt etablera Windows- och Linux-miljöer med återanvändningsbara mallar och artefakter.
- Testarna kan skala upp sin belastningstestning genom att etablera flera testagenter.
- Administratörerna kan kontrollera kostnaderna genom att säkerställa att testarna inte kan få fler virtuella datorer än vad de behöver för testningen och att de stängs av när inte används.

Mer information finns i [Använda DevTest Labs för testning](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Användarprofiler
Den här artikeln beskriver olika användarprofiler i Azure Lab Services. 

### <a name="lab-account-owner"></a>Labbkontoägare
Vanligtvis fungerar en IT-administratör för organisationens molnresurser som ansvarar för Azure-prenumerationen som labbkontoägare och utför följande uppgifter:   

- Konfigurerar ett labbkonto för organisationen.
- Hanterar och konfigurerar principer för alla labb.
- Ger behörigheter till personer i organisationen för att skapa ett labb under labbkontot.

### <a name="lab-creator"></a>Labbskapare 
Vanligtvis en användare som en utvecklingsansvarig, en lärare, en hackathon-värd eller en lärare online som skapar labb under ett labbkonto. En labbskapare utför följande uppgifter: 

- Skapar ett labb.
- Skapar virtuella maskiner i labbet. 
- Installerar rätt programvara på virtuella datorer.
- Anger vem som kan få åtkomst till labbet.
- Förser labbanvändarna med en länk till labbet.

### <a name="lab-user"></a>Labbanvändare
En labbanvändare utför följande uppgifter:

- Använder registreringslänken som denne får från labbskaparen för att registrera sig för labbet. 
- Ansluter till en virtuell dator i labbet och använder den för utveckling, testning eller skolarbete. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](classroom-labs/tutorial-setup-classroom-lab.md)
- [Konfigurera ett labb](tutorial-create-custom-lab.md)
