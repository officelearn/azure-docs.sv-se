---
title: Anledningen till att säkra arbetsstationer som är viktiga - Azure Active Directory
description: Lär dig varför organisationer bör skapa säker Azure-hanterade arbetsstationer
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
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357051"
---
# <a name="building-secure-workstations"></a>Att skapa säkra arbetsstationer

Säker isolerade arbetsstationer är ytterst viktiga för att skydda känsliga roller som administratörer, utvecklare och operatörer av kritiska tjänster. Många andra säkerhetskontroller och garanterar kommer att misslyckas eller har ingen effekt om den underliggande arbetsstation klientsäkerheten har komprometterats.

Det här dokumentet beskriver vad som krävs för att skapa en säker klienten arbetsstation med detaljerade steg för steg-instruktioner, inklusive hur du ställer in startar säkerhetskontroller. Den här typen av arbetsstationer ibland kallas för en arbetsstation för privilegierad åtkomst (PAW), som den här referensen används, och bygger på. Vägledningen men ser för molnbaserade teknik för att hantera tjänsten och introducerar säkerhetsfunktioner som introducerades med början i Windows 10RS5, Microsoft Defender ATP, Azure Active Directory och Intune.

## <a name="why-securing-workstation-access-is-important"></a>Varför säkerhetsåtkomst arbetsstation är viktigt

Snabba beslut om cloud services och möjlighet att arbeta från valfri plats har skapat en ny metod för utnyttjande. Angripare utnyttjar svaga säkerhetsåtgärder på enheter där administratörer fungerar och kan få åtkomst till Privilegierade resurser.

Enligt beskrivningen i den [Verizon Threat rapporten](https://enterprise.verizon.com/resources/reports/dbir/), och [Security Intelligence Report](https://aka.ms/sir) privilegierad missbruka och strömförsörjning kedja attacker är bland de fem främsta mekanismer som används för att bryta mot organisationer, och oftast upptäckte andra möjligheten i incidenter som rapporteras i 2018.

De flesta angripare följa sökvägen nedan:

* Börja med rekognosering, ofta specifika för en bransch, att hitta ett sätt i
* Analysera Insamlad information för att identifiera det bästa sättet att få åtkomst (intrånget) hos en arbetsstation upplevd lågt värde
* Persistence och titta på sätt att flytta [sidled](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* Stjäla konfidentiell och känslig data

Angripare infiltrera ofta enheter som verkar lågrisk eller under för rekognosering. Dessa sårbara enheter används sedan för att hitta en möjlighet för lateral förflyttning, hitta administrativa användare och enheter och identifiera hög värdefulla data som har stjäla information när de får dessa Privilegierade roller.

![Mönster för vanliga kompromettering](./media/concept-azure-managed-workstation/typical-timeline.png)

Det här dokumentet ger en lösning för att skydda dina enheter genom att isolera hantering och tjänster för att skydda mot lateral förflyttning eller attacker från mindre värdefulla produktivitet enheter. Designen hjälper till att minska möjligheten att utföra ett intrång genom att bryta ned kedjan innan intrång på den enhet som används för att hantera eller åtkomst till känsliga molnresurser. Lösningen beskrivs ska använda interna Azure-tjänster som ingår i Microsoft 365 Enterprise stack inklusive:

* Intune för hantering av enheter, inklusive program och URL: en lista över tillåtna
* Autopilot för installation av enheter och distribution och uppdatering 
* Azure AD för hantering av användare, villkorlig åtkomst och Multi-Factor authentication
* Windows 10 (aktuell version) för enhetens hälsotillstånd attestering och användarupplevelse
* Microsoft Defender Advanced Threat Protection (ATP) för endpoint protection, identifiering och svar med molnhantering
* Azure AD PIM för att hantera auktorisering, inklusive precis i tid JIT-privilegierad åtkomst till resurser

## <a name="who-benefit-from-using-a-secure-workstation"></a>Vem som kan dra nytta av en säker arbetsstation

Alla användare och operatörer dra nytta av en säker arbetsstation. En angripare som komprometterar en dator eller enhet kan göra flera saker, inklusive personifiera alla cachelagrade konton, och Använd autentiseringsuppgifter, och token som används på enheten medan de är inloggade på. Den här risken kan skydda de enheter som används för alla Privilegierade roller, inklusive administrativa rättigheter som är så viktigt enheter där ett privilegierat konto används är mål för lateral förflyttning och attacker för eskalering av privilegier. Dessa konton kan användas för en mängd olika tillgångar som:

* Administratörer av lokala och molnbaserade system
* Arbetsstationer för kritiska system
* Sociala konton administratör med hög exponering
* Mycket känslig arbetsstationer som SWIFT betalningsterminaler
* Hantering av affärshemligheter arbetsstationer

Microsoft rekommenderar att implementera upphöjd säkerhetskontroller för privilegierade arbetsstationer där dessa konton används för att minska risken. Mer information finns i den [Distributionsguide för Azure Active Directory-funktionen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365-översikt](https://aka.ms/o365secroadmap), och [skydda privilegierad åtkomst översikt](https://aka.ms/sparoadmap)).

## <a name="why-dedicated-workstations"></a>Varför dedikerade arbetsstationer

Det är möjligt att lägga till säkerhet i en befintlig enhet är det bättre att börja med en säker grund. Från och med en känd bra enhet och en uppsättning säkerhets kontroller placeras din organisation bäst lämpad att underhålla som ökat säkerhetsnivå. Med ständigt växande antal angreppsvinklar som tillåts av avslappnat e-post och webbläsare, är det allt svårt att se till att en enhet kan vara betrodda. Den här guiden fungerar under förutsättning en dedikerad arbetsstation separeras från standard produktivitet, bläddra, och e-poståtgärder har slutförts. Borttagning av produktivitet, webbsurfning och e-post från en enhet kan ha negativ inverkan på produktiviteten, men det här skyddet är vanligtvis är godkänd för scenarier där jobbuppgifter inte uttryckligen ska användas och risken för en säkerhetsincident är hög.

> [!NOTE]
> Webbsurfning här avser allmän åtkomst till valfri webbplatser, vilket är en hög risk som är helt olika från att använda en webbläsare för att få åtkomst till ett litet antal välkända administrativa webbplatser för tjänster som Azure, Office 365, andra molnleverantörer och SaaS program.

Strategier för inneslutning ger ökad säkerhetsgarantier genom att öka antalet och typen av kontroller som en angripare har att övervinna för att komma åt känsliga tillgångar. Modellen har utvecklats här ger inneslutning av administrativ behörighet till specifika enheter med hjälp av en modell för nivåindelade behörighet.

## <a name="supply-chain-management"></a>Hantering av leverantörskedja

Viktigt för en säker arbetsstation är en leverans kedja lösning där arbetsstation som du använder är betrodd, en ”förtroenderoten”. Den här lösningen tar upp roten för förtroende med den [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) teknik. För en säker arbetsstation ger Microsoft Autopilot möjlighet att utnyttja Microsoft OEM-optimerade Windows 10-enheter som tillhandahåller ett fungerande tillstånd från tillverkaren. I stället för avbildningen på en enhet som inte är betrodd, Microsoft Autopilot kan omvandla en Windows-enhet till tillståndet ”business ready” tillämpa inställningar och principer, installera appar, och även om du ändrar utgåvan av Windows 10 som används (till exempel från Windows 10 Pro till Windows 10 Enterprise, som stöd för avancerade funktioner).

![Säker arbetsstation nivåer](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Enheten roller och -profiler

I vägledningen åtgärdas flera Säkerhetsprofiler och roller för att uppnå en mer säker lösning för användare, utvecklare och IT-personalstyrka. De här profilerna har varit justerade för att ge stöd för vanliga användare i organisationer som kan dra nytta av en förbättrad eller säker arbetsstation när balans mellan användbarhet och risk. Vägledningen ger konfiguration av inställningar baserat på branschstandarder accepteras. Den här vägledningen för att illustrera en metod i Härdning av Windows 10 och minskar riskerna med enhet eller användare kompromettering med hjälp av principer och teknik för att hantera funktioner för säkerhet och risker.
![Säker arbetsstation nivåer](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Låg säkerhet** – en hanterad dator som är standard ger en bra utgångspunkt för de flesta hem och små företag. Dessa enheter är Azure AD-registrerade och hanteras av Intune. Profilen tillåter användare att köra alla program och bläddra någon webbplats. En lösning för program mot skadlig kod som [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) ska aktiveras.
* **Förbättrad säkerhet** – är en lösning för ingångsnivå skyddade bra för hemanvändare, småföretagsanvändare samt allmänna utvecklare.
   * Utökad arbetsstation ger en principbaserad innebär att förbättra säkerheten för låg säkerhet. Den här profilen tillåter ett säkert sätt att arbeta med kunddata och att kunna använda produktivitetsverktyg som kontrollerar e-post och webbsurfning. En utökad arbetsstation kan användas för att granska användarnas beteende och profilen användning av en arbetsstation genom att aktivera granskning och loggning till Intune. I den här profilen arbetsstationen aktiverar säkerhetskontroller och principer som beskrivs i innehållet och distribueras i förbättrad-arbetsstation - Windows 10 (1809)-skript. Distributionen också drar nytta av avancerad skadlig kod skydd med [avancerade Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **Hög säkerhet** – det mest effektiva sättet att minska risken för angrepp på en arbetsstation som är att ta bort möjligheten att self-administer arbetsstationen. Ta bort lokala administrativa rättigheter är ett steg som ger ökad säkerhet och kan påverka produktiviteten om implementerat felaktigt. Hög säkerhet som bygger på profilen för förbättrad säkerhet med en betydande ändring, borttagning av den lokala administratören. Den här profilen har utformats för att hjälpa till med användare som kan vara en hög profilen, till exempel en chef eller de användare som kan ha kontakta med känsliga data, till exempel löneuppgifter eller godkännande av tjänster och processer.
   * Användarprofilen hög säkerhet kräver en högre kontrollerad miljö när du fortfarande att kunna utföra sina produktivitet-aktivitet, t.ex e-post och webbsurfning samtidigt som en enkel att använda. Användarna förväntar sig funktioner, till exempel cookies, Favoriter och andra genvägar som är tillgängliga att fungera. Men dessa användare kan inte kräva möjlighet att ändra eller felsöka sina enheter och behöver inte installera drivrutiner. Hög säkerhet med hjälp av hög säkerhet - skript för Windows 10 (1809).
* **Specialiserade** – utvecklare och IT-administratörer är ett attraktivt mål för attacker eftersom dessa roller kan ändra system intressant för angriparen. Specialiserad arbetsstationen tar det arbete som distribuerats i arbetsstation med hög säkerhet och ytterligare emphases säkerheten genom att hantera lokala program, begränsa webbplatser på internet och att begränsa produktivitet funktioner som är hög risk, till exempel ActiveX, Java, webbläsare plugin-programmets och flera andra kända hög risk kontroller på en Windows-enhet. I den här profilen arbetsstationen aktiverar säkerhetskontroller och principer som beskrivs i innehållet och distribueras i DeviceConfiguration_NCSC - Windows 10 (1803) SecurityBaseline skript.
* **Skyddad** – en angripare som kan påverka ett administratörskonto kan vanligtvis skada viktiga genom datastöld data borttagande av eller avbrott i tjänsten. I det här tillståndet som strikt arbetsstationen ska aktivera alla säkerhetskontroller och principer som begränsar direkt kontroll av hantering av lokala program och produktivitetsverktyg tas bort. Därför kan äventyra enheten görs svårare som e-post och sociala medier är blockerade som speglar det vanligaste sättet nätfiskeattacker kan lyckas.  Säker arbetsstation kan distribueras med säkra-arbetsstation - Windows 10 (1809) SecurityBaseline skript.

   ![Säker arbetsstation](./media/concept-azure-managed-workstation/secure-workstation.png)

   En säker arbetsstation får administratörer en förstärkt dator som har tydliga programkontroll och application guard. Arbetsstationen använder autentiseringsuppgifter och enheten exploit guard för att skydda värden från skadligt beteende. Dessutom alla lokala diskar krypteras med Bitlocker-kryptering.

* **Isolerade** – den här anpassade offlinescenario representerar extrem slutet av spektrumet (inga installationsskript tillhandahålls för det här fallet). Organisationer kan behöva hantera en isolerade kritiska funktioner, till exempel en produktionslinje för högt värde eller liv supportsystem som kräver stöds inte/datorer utan äldre operativsystem. Eftersom säkerhet är viktigt och molntjänster är inte tillgänglig, kan organisationer manuellt hantera/uppdatera dessa datorer eller använda en arkitektur för isolerade Active Directory-skog (t.ex. den förbättrad säkerhet Admin säkerhetsmiljö (ESAE)) för att hantera dem. I dessa omständigheter som tar bort all åtkomst förutom grundläggande Intune och ATP bör hälsotillstånd kontrollerar övervägas.
   * [Krav för Intune nätverk kommunikation](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [ATP nätverk kommunikation krav](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Nästa steg

[Distribuera en säker arbetsstation för Azure-hanterade](howto-azure-managed-workstation.md)
