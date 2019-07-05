---
title: Förstå säker, Azure-hanterade arbetsstationer - Azure Active Directory
description: Lär dig säker och Azure-hanterade arbetsstationer och förstå varför de är viktiga.
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
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491597"
---
# <a name="understand-secure-azure-managed-workstations"></a>Förstå säker, Azure-hanterade arbetsstationer

Säker, isolerat arbetsstationer är ytterst viktiga för att skydda känsliga roller som administratörer och utvecklare kritiska-operatorer. Om arbetsstation klientsäkerhet komprometteras så många säkerhetskontroller och garanterar att misslyckas eller vara ineffektiv.

Det här dokumentet förklarar vad du behöver för att skapa en säker arbetsstation, som ofta kallas en arbetsstation för privilegierad åtkomst (PAW). Artikeln innehåller också detaljerade anvisningar för att ställa in inledande säkerhetskontroller. Den här vägledningen beskriver hur molnbaserad teknik kan hantera tjänsten. Den är beroende av säkerhetsfunktioner som infördes i Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory och Intune.

> [!NOTE]
> Den här artikeln förklarar konceptet med en säker arbetsstation och dess betydelse. Om du redan är bekant med konceptet och vill gå vidare till distribution, besök [distribuera en säker arbetsstation](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Varför säker arbetsstation åtkomst är viktigt

Snabba beslut om cloud services och möjlighet att arbeta från valfri plats har skapat en ny utnyttjande-metod. Angripare kan få åtkomst till Privilegierade resurser genom att utnyttja svaga säkerhetsåtgärder på enheter där administratörer fungerar.

Privilegierad missbruka och strömförsörjning kedja attacker är bland de översta fem metoder som angripare använder för att bryta mot organisationer. De är också andra oftast har identifierats taktik i incidenter som rapporteras i 2018 enligt den [Verizon Threat rapporten](https://enterprise.verizon.com/resources/reports/dbir/), och [Security Intelligence Report](https://aka.ms/sir).

De flesta angripare så här:

1. Rekognosering att hitta ett sätt, ofta specifika för en bransch.
1. Analys och samla in information som du kan identifiera det bästa sättet att infiltrera en arbetsstation som uppfattas som lågt värde.
1. Persistence att leta efter ett sätt att flytta [sidled](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltrering av konfidentiell och känslig data.

Under rekognosering infiltrera angripare ofta enheter som verkar lågrisk eller under. De kan använda dessa sårbara enheter för att hitta en möjlighet för lateral förflyttning och för att hitta administrativa användare och enheter. När de får tillgång till Privilegierade roller identifiera angripare data med högt värde och har stjäla data.

![Mönster för vanliga kompromettering](./media/concept-azure-managed-workstation/typical-timeline.png)

Det här dokumentet beskriver en lösning som hjälper dig att skydda dina enheter från sådan lateral attacker. Lösningen isolerar hantering och tjänster från mindre värdefulla produktivitet enheter, större kedjan innan den enhet som har åtkomst till känsliga molnresurser kan vara infiltrated. Lösningen använder interna Azure-tjänster som ingår i Microsoft 365 Enterprise-stack:

* Intune för hantering av enheter och en säker lista över program och URL: er
* Autopilot för enhetskonfiguration, distribution och uppdatering
* Azure AD för hantering av användare, villkorlig åtkomst och Multi-Factor authentication
* Windows 10 (aktuell version) för enhetens hälsotillstånd attestering och användarupplevelse
* Defender ATP för cloud-hanterade endpoint protection, identifiering och svar
* Azure AD PIM för att hantera auktorisering och just-in-time (JIT) privilegierad åtkomst till resurser

## <a name="who-benefits-from-a-secure-workstation"></a>Som dra nytta av en säker arbetsstation?

Alla användare och operatörer ha nytta av när du använder en säker arbetsstation. En angripare som komprometterar en dator eller enhet kan personifiera alla cachelagrade konton. De kan också använda autentiseringsuppgifter och token när inloggad på enheten. Den här risken är det viktigt att säkra enheter som används för privilegierade roller, inklusive administrativa rättigheter. Enheter med Privilegierade konton är mål för lateral förflyttning och attacker för eskalering av privilegier. Dessa konton kan användas för en mängd olika tillgångar som:

* Administratör för den lokala eller molnbaserade system
* Utvecklararbetsstation för kritiska system
* Kontoadministratör för sociala medier med hög
* Mycket känslig arbetsstation, till exempel en terminal SWIFT betalning
* Arbetsstationen affärshemligheter för hantering

För att minska risken, bör du implementera utökade säkerhetskontroller för privilegierade arbetsstationer som gör att använda för dessa konton. Mer information finns i den [Distributionsguide för Azure Active Directory-funktionen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), [Office 365-översikt](https://aka.ms/o365secroadmap), och [skydda privilegierad åtkomst översikt](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Varför använda dedikerade arbetsstationer?

Det är möjligt att lägga till säkerhet i en befintlig enhet är det bättre att börja med en säker grund. Om du vill placera din organisation bäst lämpad att upprätthålla en högre nivå, börja med en enhet som du vet är säker och implementera en uppsättning kända säkerhetskontroller.

Ett växande antal angreppsmetoder via e-post och webbsurfning blir det allt svårt att se till att en enhet är betrodd. Den här handboken förutsätts att en dedikerad arbetsstation är isolerad från standard produktivitet, bläddra och e-post. Borttagning av produktivitet, webbsurfning och e-post från en enhet kan ha en negativ inverkan på produktiviteten. Det här skyddet är vanligtvis är godkänd för scenarier där jobbuppgifter inte uttryckligen ska användas och risken för en säkerhetsincident är hög.

> [!NOTE]
> Webbsurfning här avser allmän åtkomst till valfri webbplatser som kan vara en aktivitet med hög risk. Sådana surfning är helt olika från att använda en webbläsare för att få åtkomst till ett litet antal välkända administrativa webbplatser för tjänster som Azure, Office 365, andra molnleverantörer och SaaS-program.

Strategier för inneslutning öka säkerheten genom att öka antalet och typen av kontroller som hindra en angripare från att få åtkomst till känsliga tillgångar. Modellen som beskrivs i den här artikeln använder en nivåindelad behörighet design och begränsar administratörsbehörighet till specifika enheter.

## <a name="supply-chain-management"></a>Hantering av leverantörskedja

Viktigt för en säker arbetsstation är en lösning för leverans kedja där du använder en betrodda arbetsstationer som kallas ”rot för förtroendet”. För den här lösningen använder förtroenderoten [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) teknik. Om du vill skydda en arbetsstation kan Autopilot du utnyttja Microsoft OEM-optimerade Windows 10-enheter. Dessa enheter kommer i ett fungerande tillstånd från tillverkaren. I stället för avbildningen på en potentiellt osäkert enhet, kan Autopilot omvandla en Windows-enhet i ett ”business ready” tillstånd. Det gäller inställningar och principer, installerar appar och ändras även utgåvan av Windows 10. Till exempel kan Autopilot ändra Windows-installation för en enhet från Windows 10 Pro till Windows 10 Enterprise så att den kan använda avancerade funktioner.

![Säker arbetsstation nivåer](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Enheten roller och -profiler

Den här vägledningen refererar till flera Säkerhetsprofiler och roller som kan hjälpa dig att skapa mer säkra lösningar för användare, utvecklare och IT-personal. De här profilerna balans mellan användbarhet och risker för vanliga användare som kan dra nytta av en förbättrad eller säker arbetsstation. Inställningarna konfigurationer som anges här är baserat på branschstandarder accepteras. Den här vägledningen visar hur du förstärka Windows 10 och minska riskerna med enhet eller användare kompromettering. Detta sker med hjälp av principer och teknik för att hantera funktioner för säkerhet och risker.
![Säker arbetsstation nivåer](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Låg säkerhet** – en hanterad dator som är standard ger en bra utgångspunkt för de flesta hemnätverk och små företag. Dessa enheter är registrerade i Azure AD och hanteras med Intune. Den här profilen tillåter användare att köra alla program och bläddra någon webbplats. En lösning mot skadlig kod som [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) ska aktiveras.

* **Förbättrad säkerhet** – den här lösningen på ingångsnivå, skyddade är bra för hemanvändare, små företagsanvändare och allmän utvecklare.

   Förbättrad arbetsstationen är en principbaserad sätt att öka säkerheten för låg säkerhet. Det ger ett säkert sätt att arbeta med kunddata när du även använder produktivitetsverktyg som e-post och webbsurfning. Du kan använda granskningsprinciper och Intune för att övervaka en förbättrad arbetsstation för användaren beteende och profil. Du distribuerar profilen förbättrad arbetsstation med skriptet för Windows 10 (1809) och den drar nytta av avancerad skadlig kod skydd med [avancerade Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Hög säkerhet** – det mest effektiva sättet att minska risken för angrepp på en arbetsstation som är att ta bort möjligheten att self-administer arbetsstationen. Ta bort lokala administrativa rättigheter är en åtgärd som förbättrar säkerheten, men det kan påverka produktiviteten om implementerat felaktigt. Hög säkerhet som bygger på profilen för förbättrad säkerhet med en betydande ändring: borttagning av den lokala administratören. Den här profilen är utformad för höga profilanvändare: chefer, lönelistor och känsliga dataanvändare, godkännare för tjänster och processer.

   Hög säkerhet användaren kräver en mer kontrollerad miljö samtidigt som de kan göra aktiviteter, till exempel e-post och webbläsare i en enkel att använda-upplevelse. Användarna förväntar sig funktioner, till exempel cookies, Favoriter och andra genvägar ska fungera. Dessa användare kan dock inte kräva möjlighet att ändra eller felsöka enheten. De behöver också att installera drivrutiner. Hög säkerhet med hjälp av hög säkerhet - skript för Windows 10 (1809).

* **Specialiserade** – angripare riktar sig mot utvecklare och IT-administratörer eftersom de kan ändra system intressant för angriparen. Specialiserad arbetsstationen kan utökas med principer för arbetsstationen hög säkerhet genom att hantera lokala program och begränsa webbplatser. Det begränsar också med hög risk produktivitet funktioner, till exempel ActiveX-, Java, plugin-program för webbläsare och andra Windows-kontroller. Distribuera profilen med DeviceConfiguration_NCSC - Windows 10 (1803) SecurityBaseline skript.

* **Skyddad** – en angripare som komprometterar ett administratörskonto kan skada viktiga genom datastöld data borttagande av eller avbrott i tjänsten. I det här tillståndet som strikt kan arbetsstationen alla säkerhetskontroller och principer som begränsar direkt kontroll av hantering av lokala program. En säker arbetsstation har inga produktivitetsverktyg så enheten svårare att angripa. Den blockerar vanligaste vektor för nätfiskeattacker: e-post och sociala medier.  Säker arbetsstation kan distribueras med säkra-arbetsstation - Windows 10 (1809) SecurityBaseline skript.

   ![Säker arbetsstation](./media/concept-azure-managed-workstation/secure-workstation.png)

   En säker arbetsstation får administratörer en förstärkt dator som har tydliga programkontroll och application guard. Arbetsstationen använder credential guard och device guard exploit guard för att skydda värden från skadligt beteende. Alla lokala diskar är också krypterade med BitLocker.

* **Isolerade** – det här anpassade, offline-scenariot representerar extrem slutet av spektrumet. Inga installationsskript tillhandahålls för det här fallet. Du kan behöva hantera en affärskritisk funktion som kräver ett som inte stöds eller okorrigerade äldre operativsystem. Till exempel en produktionslinje för högt värde eller ett liv--stödsystem. Eftersom säkerhet är viktigt och molntjänster är inte tillgänglig, kan du hantera och uppdatera dessa datorer manuellt eller med en arkitektur för isolerade Active Directory-skogen, till exempel den förbättrad säkerhet Admin säkerhetsmiljö (ESAE). Överväg att ta bort all åtkomst förutom grundläggande hälsokontroller för Intune och ATP under dessa omständigheter.

  * [Krav för Intune nätverk kommunikation](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [ATP nätverk kommunikation krav](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Nästa steg

[Distribuera en säker arbetsstation för Azure-hanterade](howto-azure-managed-workstation.md).
