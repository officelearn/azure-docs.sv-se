---
title: Förstå säkra, Azure-hanterade arbets stationer – Azure Active Directory
description: Lär dig mer om säkra, Azure-hanterade arbets stationer och förstå varför de är viktiga.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00e5e4a9751a03fe961fe02fec28f41b49e5ba43
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175154"
---
# <a name="understand-secure-azure-managed-workstations"></a>Förstå säkra, Azure-hanterade arbets stationer

Skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Om säkerheten för en klient arbets Station komprometteras kan många säkerhets kontroller och säkerhets garantier gå sönder eller vara ineffektiva.

Det här dokumentet förklarar vad du behöver för att skapa en säker arbets Station, ofta kallat en privilegie rad åtkomst arbets Station (PAW). Artikeln innehåller också detaljerade instruktioner för hur du konfigurerar de första säkerhets kontrollerna. Den här vägledningen beskriver hur Cloud-baserad teknik kan hantera tjänsten. Den förlitar sig på säkerhetsfunktioner som introducerades i Windows 10RS5, Microsoft Defender Avancerat skydd (ATP), Azure Active Directory och Intune.

> [!NOTE]
> Den här artikeln förklarar begreppet säker arbets Station och dess betydelse. Om du redan är bekant med konceptet och vill hoppa över till distributionen kan du gå till [distribuera en säker arbets Station](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Varför säker åtkomst till arbets Station är viktigt

Snabbt införande av moln tjänster och möjligheten att arbeta från var som helst har skapat en ny exploaterings metod. Genom att utnyttja svaga säkerhets kontroller på enheter där administratörer fungerar kan angripare få åtkomst till privilegierade resurser.

Angrepp med privilegie rad missbruk och leverans kedja är bland de fem främsta metoderna som angripare använder för att bryta organisationer. De är också den andra oftast identifierade taktiker i incidenter som rapporteras i 2018 enligt [Verizon-Threat-rapporten](https://enterprise.verizon.com/resources/reports/dbir/)och [säkerhets informations rapporten](https://aka.ms/sir).

De flesta angripare följer de här stegen:

1. Rekognosering för att hitta ett sätt i, ofta särskilt för en bransch.
1. Analys för att samla in information och identifiera det bästa sättet att infiltrera en arbets station som uppfattas som lågt värde.
1. Persistence att söka efter ett sätt att flytta [senare](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltrering av konfidentiella och känsliga data.

Under rekognosering brukar angripare ofta infiltrera enheter som verkar för låg risk eller undervärderar. De använder dessa sårbara enheter för att hitta en möjlighet för lateral förflyttning och för att hitta administrativa användare och enheter. När de får åtkomst till privilegierade användar roller, identifierar angriparen data med hög värde och stjäla data.

![Typiskt kompromiss mönster](./media/concept-azure-managed-workstation/typical-timeline.png)

Det här dokumentet beskriver en lösning som kan hjälpa till att skydda dina dator enheter från sådana laterala angrepp. Lösningen isolerar hantering och tjänster från mindre värdefulla produktivitets enheter, vilket bryter kedjan innan enheten som har åtkomst till känsliga moln resurser kan infiltrated. Lösningen använder inbyggda Azure-tjänster som ingår i Microsoft 365 Enterprise-stacken:

* Intune för enhets hantering och en säker lista över program och URL: er
* Autopilot för enhets installation, distribution och uppdatering
* Azure AD för användar hantering, villkorlig åtkomst och Multi-Factor Authentication
* Windows 10 (aktuell version) för hälsoattestering av enhets hälsa och användar upplevelse
* Defender ATP för Cloud-hanterad slut punkts skydd, identifiering och svar
* Azure AD PIM för hantering av privilegie rad åtkomst till resurser för auktorisering och just-in-Time (JIT)

## <a name="who-benefits-from-a-secure-workstation"></a>Vem fördelar från en säker arbets Station?

Alla användare och operatörer har nytta av en säker arbets Station. En angripare som komprometterar en dator eller enhet kan personifiera alla cachelagrade konton. När du är inloggad på enheten kan de även använda autentiseringsuppgifter och tokens. Den här risken gör det viktigt att säkra enheter som används för privilegierade roller, inklusive administrativa rättigheter. Enheter med privilegierade konton är mål för att flytta och eskalera attacker. Dessa konton kan användas för olika till gångar, till exempel:

* Administratör för lokala eller molnbaserade system
* Arbets station för utvecklare för kritiska system
* Konto administratör för sociala medier med hög exponering
* Mycket känslig arbets Station, till exempel en SWIFT-betalningsterminal
* Arbets station som hanterar affärs hemligheter

För att minska risken bör du implementera utökade säkerhets kontroller för privilegierade arbets stationer som använder dessa konton. Mer information finns i [distributions guiden för Azure Active Directory Feature](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365-översikt](https://aka.ms/o365secroadmap)och [skydd av Privileged Access Planning](https://aka.ms/sparoadmap).

## <a name="why-use-dedicated-workstations"></a>Varför ska jag använda dedikerade arbets stationer?

Även om det är möjligt att lägga till säkerhet till en befintlig enhet, är det bättre att börja med en säker grund. Om du vill att din organisation ska kunna upprätthålla hög säkerhets nivå börjar du med en enhet som du vet är säker och implementerar en uppsättning kända säkerhets kontroller.

Ett ökande antal angrepps vektorer via e-post och Webbs ökning gör det mer svårt att vara säker på att en enhet kan vara betrodd. Den här guiden förutsätter att en dedikerad arbets Station är isolerad från standard produktivitet, surfning och e-post. Borttagning av produktivitet, webb läsning och e-post från en enhet kan ha negativ inverkan på produktiviteten. Den här säkerheten är dock vanligt vis acceptabel för scenarier där jobb aktiviteter inte uttryckligen kräver det och risken för en säkerhets incident är hög.

> [!NOTE]
> Webb läsning här avser allmän åtkomst till godtyckliga webbplatser som kan vara en hög risk aktivitet. Sådan bläddring skiljer sig tydligt från att använda en webbläsare för att få åtkomst till ett litet antal välkända administrativa webbplatser för tjänster som Azure, Office 365, andra moln leverantörer och SaaS-program.

Inne slutnings strategier ökar säkerheten genom att öka antalet och typen av kontroller som avskräcker en angripare från att få åtkomst till känsliga till gångar. Den modell som beskrivs i den här artikeln använder en hanterad behörighets design och begränsar administrativa privilegier till vissa enheter.

## <a name="supply-chain-management"></a>Hantering av leverans kedja

Nödvändig för en säker arbets Station är en lösning för leverans kedja där du använder en betrodd arbets station som kallas "rot för förtroende". I den här lösningen använder roten av förtroende [Microsoft autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) -teknik. För att skydda en arbets station gör autopilot det möjligt för dig att utnyttja Microsoft-OEM-optimerade Windows 10-enheter. Dessa enheter kommer in i ett känt tillstånd från tillverkaren. I stället för att återställa en potentiellt osäker enhet kan autopilot omvandla en Windows-enhet till ett "affärs klart" läge. Den tillämpar inställningar och principer, installerar appar och ändrar även utgåvan av Windows 10. Till exempel kan autopiloten ändra en enhets Windows-installation från Windows 10 Pro till Windows 10 Enterprise så att den kan använda avancerade funktioner.

![Skydda arbets Stations nivåer](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Enhets roller och profiler

Den här vägledningen hänvisar till flera säkerhets profiler och roller som kan hjälpa dig att skapa säkrare lösningar för användare, utvecklare och IT-personal. Dessa profiler balanserar användbarhet och risker för vanliga användare som kan dra nytta av en förbättrad eller säker arbets Station. De inställnings konfigurationer som anges här baseras på bransch godkända standarder. Den här vägledningen visar hur du skärper Windows 10 och minskar riskerna med enhets-eller användar problem. Det gör det genom att använda princip och teknik för att hjälpa dig att hantera säkerhetsfunktioner och risker.
![säkra arbets Stations nivåer](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Låg säkerhet** – en hanterad, standard arbets Station ger en välgrundad utgångs punkt för de flesta hem-och små företags användning. Dessa enheter är registrerade i Azure AD och hanteras med Intune. Den här profilen tillåter användare att köra program och bläddra på alla webbplatser. En lösning mot skadlig kod som [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) ska vara aktive rad.

* **Förbättrad säkerhet** – den skyddade lösningen på ingångs nivå är lämplig för hemmaanvändare, små företags användare och allmänna utvecklare.

   Den förbättrade arbets stationen är ett princip-baserat sätt för att öka säkerheten för låg säkerhets profil. Det är ett säkert sätt att arbeta med kunddata samtidigt som du använder produktivitets verktyg som e-post och Webbs ökning. Du kan använda gransknings principer och Intune för att övervaka en förbättrad arbets station för användar beteende och profil användning. Du distribuerar den förbättrade arbets Stations profilen med skriptet windows10 (1809) och drar nytta av avancerat skydd mot skadlig kod med hjälp av [Avancerat skydd (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Hög säkerhet** – det mest effektiva sättet att minska angrepps ytan på en arbets Station är att ta bort möjligheten att själv administrera arbets stationen. Att ta bort lokala administrativa rättigheter är ett steg som förbättrar säkerheten, men det kan påverka produktiviteten om den implementeras felaktigt. Profilen för hög säkerhet bygger på den förbättrade säkerhets profilen med en betydande ändring: borttagning av den lokala administratören. Den här profilen är avsedd för användare med hög profil: chefer, löner och känsliga data användare, god kännare för tjänster och processer.

   Användaren med hög säkerhet kräver en mer kontrollerad miljö och kan fortfarande utföra aktiviteter, till exempel e-post och Webbs ökning i en enkel-till-användning-upplevelse. Användarna förväntar sig att funktioner som cookies, favoriter och andra genvägar ska fungera. Dessa användare kan dock inte kräva möjlighet att ändra eller Felsöka enheten. De behöver inte heller installera driv rutiner. Profilen för hög säkerhet distribueras med hjälp av skriptet High Security-windows10 (1809).

* **Specialiserade** – angripare riktar sig till utvecklare och IT-administratörer eftersom de kan ändra system av intresse för angripare. Den specialiserade arbets stationen expanderar principerna för arbets stationen hög säkerhet genom att hantera lokala program och begränsa webbplatser. Det begränsar också möjligheterna till hög risk produktivitet, till exempel ActiveX, Java, webb läsar-plugin-program och andra Windows-kontroller. Du distribuerar den här profilen med SecurityBaseline-skriptet DeviceConfiguration_NCSC-windows10 (1803).

* **Säkra** – en angripare som äventyrar ett administratörs konto kan orsaka betydande affärs skada genom stöld av data, data förändringar eller avbrott i tjänsten. I detta härdade tillstånd aktiverar arbets stationen alla säkerhets kontroller och principer som begränsar direkt styrningen av lokal program hantering. En säker arbets Station har inga produktivitets verktyg, så det är svårare att kompromissa med enheten. Den blockerar den vanligaste vektorn för nätfiske-attacker: e-post och sociala medier.  Den skyddade arbets stationen kan distribueras med SecurityBaseline-skriptet Secure Workstation-windows10 (1809).

   ![Skyddad arbets Station](./media/concept-azure-managed-workstation/secure-workstation.png)

   En säker arbets Station ger en administratör med en strikt arbets station som har klarat program kontroll och Application Guard. Arbets stationen använder Credential Guard, Device Guard och sårbarhet Guard för att skydda värden från skadliga beteenden. Alla lokala diskar krypteras också med BitLocker.

* **Isolerade** – det anpassade scenariot offline representerar det extrema slutet av spektrumet. Det finns inga installations skript för det här fallet. Du kan behöva hantera en verksamhets kritisk funktion som kräver ett äldre operativ system som inte stöds eller som inte har korrigerats. Till exempel en produktions linje med hög värde eller ett system för livs support. Eftersom säkerhet är kritiskt och moln tjänster inte är tillgängliga, kan du hantera och uppdatera dessa datorer manuellt eller med en isolerad Active Directory skogs arkitektur, till exempel den förbättrade säkerhets administrations miljön (ESAE). I dessa fall bör du överväga att ta bort all åtkomst förutom grundläggande hälso kontroller för Intune och ATP.

  * [Krav för nätverks kommunikation i Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Krav för nätverks kommunikation i ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Nästa steg

[Distribuera en säker Azure-hanterad arbets Station](howto-azure-managed-workstation.md).
