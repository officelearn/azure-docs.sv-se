---
title: Förstå säkra, Azure-hanterade arbetsstationer - Azure Active Directory
description: Lär dig mer om säkra, Azure-hanterade arbetsstationer och förstå varför de är viktiga.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672666"
---
# <a name="understand-secure-azure-managed-workstations"></a>Förstå säkra, Azure-hanterade arbetsstationer

Säkrade, isolerade arbetsstationer är av avgörande betydelse för säkerheten för känsliga roller som administratörer, utvecklare och kritiska tjänstoperatörer. Om klientarbetsstationssäkerheten äventyras kan många säkerhetskontroller och säkerheter misslyckas eller vara ineffektiva.

Det här dokumentet förklarar vad du behöver för att skapa en säker arbetsstation, ofta känd som en paw (privileged access workstation). Artikeln innehåller också detaljerade instruktioner för att ställa in inledande säkerhetskontroller. Den här vägledningen beskriver hur molnbaserad teknik kan hantera tjänsten. Den är beroende av säkerhetsfunktioner som introducerades i Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory och Microsoft Intune.

> [!NOTE]
> I den här artikeln förklaras begreppet en säker arbetsstation och dess betydelse. Om du redan är bekant med konceptet och vill hoppa till distributionen besöker du [Distribuera en säker arbetsstation](howto-azure-managed-workstation.md).

## <a name="why-secure-workstation-access-is-important"></a>Varför säker åtkomst till arbetsstationer är viktigt

Det snabba införandet av molntjänster och förmågan att arbeta var som helst har skapat en ny exploateringsmetod. Genom att utnyttja svaga säkerhetskontroller på enheter där administratörer arbetar kan angripare få åtkomst till privilegierade resurser.

Privilegierade missbruk och supply chain-attacker är bland de fem bästa metoderna som angripare använder för att bryta mot organisationer. De är också den näst vanligaste upptäckta taktiken i incidenter som rapporterades under 2018 enligt [Verizon Threat-rapporten](https://enterprise.verizon.com/resources/reports/dbir/)– och [Security Intelligence Report](https://aka.ms/sir).

De flesta angripare följer dessa steg:

1. Spaning för att hitta en väg in, ofta specifika för en industri.
1. Analys för att samla in information och identifiera det bästa sättet att infiltrera en arbetsstation som uppfattas som lågt värde.
1. Uthållighet att leta efter ett sätt att flytta [i sidled](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltration av konfidentiella och känsliga uppgifter.

Under spaning infiltrerar angripare ofta enheter som verkar låg risk eller undervärderade. De använder dessa sårbara enheter för att hitta en möjlighet för laterala rörelser och för att hitta administrativa användare och enheter. När de har fått åtkomst till privilegierade användarroller identifierar angripare värdefulla data och framgångsrikt frisätter dessa data.

![Typiskt kompromissmönster](./media/concept-azure-managed-workstation/typical-timeline.png)

I det här dokumentet beskrivs en lösning som kan skydda dina datorenheter från sådana laterala attacker. Lösningen isolerar hantering och tjänster från mindre värdefulla produktivitetsenheter, vilket bryter kedjan innan enheten som har åtkomst till känsliga molnresurser kan infiltreras. Lösningen använder inbyggda Azure-tjänster som ingår i Microsoft 365 Enterprise-stacken:

* Intune för enhetshantering och en säker lista över program och webbadresser
* Autopilot för enhetsinstallation, distribution och uppdatering
* Azure AD för användarhantering, villkorlig åtkomst och multifaktorautentisering
* Windows 10 (aktuell version) för enhetshälsointyg och användarupplevelse
* Defender ATP för molnhanterad slutpunktsskydd, identifiering och svar
* Azure AD PIM för hantering av privilegierad åtkomst till resurser för auktorisering och just-in-time (JIT)
* Log Analytics och Sentinel för övervakning och avisering

## <a name="who-benefits-from-a-secure-workstation"></a>Vem drar nytta av en säker arbetsstation?

Alla användare och operatörer drar nytta av att använda en säker arbetsstation. En angripare som äventyrar en dator eller enhet kan personifiera alla cachelagrade konton. När de är inloggade på enheten kan de också använda autentiseringsuppgifter och token. Den här risken gör det viktigt att skydda enheter som används för privilegierade roller, inklusive administrativa rättigheter. Enheter med privilegierade konton är måltavlor för laterala rörelser och askaleringsattacker för privilegier. Dessa konton kan användas för en mängd olika tillgångar, till exempel:

* Administratör för lokala eller molnbaserade system
* Utvecklararbetsstation för kritiska system
* Kontoadministratör för sociala medier med hög exponering
* Mycket känslig arbetsstation, till exempel en SWIFT-betalningsterminal
* Arbetsstation hantering affärshemligheter

För att minska riskerna bör du implementera förhöjda säkerhetskontroller för privilegierade arbetsstationer som använder dessa konton. Mer information finns i [Azure Active Directory-funktionsdistributionsguiden,](../fundamentals/active-directory-deployment-checklist-p2.md) [Office 365-översikten](https://aka.ms/o365secroadmap)och [översikten Skydda privilegierad åtkomst](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Varför använda dedikerade arbetsstationer?

Det är möjligt att lägga till säkerhet i en befintlig enhet, men det är bättre att börja med en säker grund. Om du vill placera din organisation i den bästa positionen för att upprätthålla en hög säkerhetsnivå börjar du med en enhet som du vet är säker och implementerar en uppsättning kända säkerhetskontroller.

Ett växande antal attackvektorer via e-post och surfning gör det allt svårare att vara säker på att en enhet kan lita på. Den här guiden förutsätter att en dedikerad arbetsstation är isolerad från standardproduktivitet, surfning och e-post. Borttagning av produktivitet, surfning och e-post från en enhet kan ha en negativ inverkan på produktiviteten. Det här skyddet är dock vanligtvis godtagbart för scenarier där jobbaktiviteterna inte uttryckligen kräver det och risken för en säkerhetsincident är hög.

> [!NOTE]
> Med surfning här avses allmän tillgång till godtyckliga webbplatser som kan vara en högriskaktivitet. Sådan surfning skiljer sig tydligt från att använda en webbläsare för att komma åt ett litet antal välkända administrativa webbplatser för tjänster som Azure, Office 365, andra molnleverantörer och SaaS-program.

Inneslutningsstrategier skärpa säkerheten genom att öka antalet och typen av kontroller som avskräcker en angripare från att få åtkomst till känsliga tillgångar. Modellen som beskrivs i den här artikeln använder en nivåindelad behörighetsdesign och begränsar administrativa privilegier till specifika enheter.

## <a name="supply-chain-management"></a>Hantering av försörjningskedjan

Viktigt för en säker arbetsstation är en lösning i leveranskedjan där du använder en betrodd arbetsstation som kallas "roten till förtroende". Teknik som måste beaktas vid valet av roten av förtroende hårdvara bör omfatta följande teknik som ingår i moderna bärbara datorer: 

* [Betrodd plattformsmodul (TPM) 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker-diskkryptering](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI säker start](/windows-hardware/design/device-experiences/oem-secure-boot)
* [Drivrutiner och inbyggd programvara distribueras via Windows Update](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [Virtualisering och HVCI aktiverat](/windows-hardware/design/device-experiences/oem-vbs)
* [Drivrutiner och appar HVCI-klar](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O-skydd](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [Systemskydd](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [Modern Standby](/windows-hardware/design/device-experiences/modern-standby)

För den här lösningen kommer förtroenderot att distribueras med [Microsoft Autopilot-teknik](/windows/deployment/windows-autopilot/windows-autopilot) med maskinvara som uppfyller de moderna tekniska kraven. För att säkra en arbetsstation kan du med Autopilot utnyttja Microsoft OEM-optimerade Windows 10-enheter. Dessa enheter kommer i ett känt gott tillstånd från tillverkaren. I stället för att återskapa en potentiellt osäker enhet kan Autopilot omvandla en Windows-enhet till ett "affärsklart" tillstånd. Den tillämpar inställningar och principer, installerar appar och ändrar till och med upplagan av Windows 10. Autopilot kan till exempel ändra en enhets Windows-installation från Windows 10 Pro till Windows 10 Enterprise så att den kan använda avancerade funktioner.

![Säkra arbetsstationsnivåer](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Enhetsroller och profiler

Den här vägledningen refererar till flera säkerhetsprofiler och roller som kan hjälpa dig att skapa säkrare lösningar för användare, utvecklare och IT-personal. Dessa profiler balanserar användbarhet och risker för vanliga användare som kan dra nytta av en förbättrad eller säker arbetsstation. De inställningar konfigurationer som anges här är baserade på branschens accepterade standarder. Den här vägledningen visar hur du härdar Windows 10 och minskar riskerna med enhets- eller användarkompromiss. För att dra nytta av den moderna maskinvarutekniken och roten av förtroendeenheten använder vi [Device Health Attestation](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643), som är aktiverat från **profilen Hög säkerhet.** Den här funktionen finns för att säkerställa att angriparna inte kan vara beständiga under den tidiga uppstarten av en enhet. Det gör man genom att använda policy och teknik för att hantera säkerhetsfunktioner och säkerhetsrisker.
![Säkra arbetsstationsnivåer](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Grundläggande säkerhet** – En hanterad, standardarbetsstation är en bra utgångspunkt för de flesta hemma- och småföretag. Dessa enheter är registrerade i Azure AD och hanteras med Intune. Den här profilen tillåter användare att köra alla program och bläddra bland alla webbplatser. En anti-malware lösning som [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) bör aktiveras.

* **Förbättrad säkerhet** – Den här instegslösningen är bra för hemanvändare, småföretagare och allmänna utvecklare.

   Den förbättrade arbetsstationen är ett principbaserat sätt att öka säkerheten för den låga säkerhetsprofilen. Det ger ett säkert sätt att arbeta med kunddata samtidigt som produktivitetsverktyg som e-post och surfning. Du kan använda granskningsprinciper och Intune för att övervaka en förbättrad arbetsstation för användarbeteende och profilanvändning. Du distribuerar den förbättrade arbetsstationsprofilen med Windows10-skriptet (1809) och utnyttjar avancerat skydd mot skadlig kod med hjälp av [Advanced Threat Protection (ATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection).

* **Hög säkerhet** – Det mest effektiva sättet att minska en arbetsstations angreppsyta är att ta bort förmågan att själv administrera arbetsstationen. Att ta bort lokala administrativa rättigheter är ett steg som förbättrar säkerheten, men det kan påverka produktiviteten om den implementeras felaktigt. Den höga säkerhetsprofilen bygger på den förbättrade säkerhetsprofilen med en avsevärd förändring: borttagningen av den lokala administratören. Den här profilen är utformad för användare med hög profil: chefer, löner och användare av känsliga data, godkännare för tjänster och processer.

   Den högsäkerhetsanvändare kräver en mer kontrollerad miljö samtidigt som den kan göra aktiviteter som e-post och surfning i en enkel att använda upplevelse. Användarna förväntar sig att funktioner som cookies, favoriter och andra genvägar ska fungera. Dessa användare kanske dock inte behöver möjlighet att ändra eller felsöka sin enhet. De behöver inte heller installera drivrutiner. Den höga säkerhetsprofilen distribueras med skriptet Hög säkerhet – Windows10 (1809).

* **Specialized** – Angripare riktar in sig på utvecklare och IT-administratörer eftersom de kan ändra system av intresse för angriparna. Den specialiserade arbetsstationen utökar principerna för den högsäkerhetsarbetsstationen genom att hantera lokala program och begränsa webbplatser. Det begränsar också hög risk produktivitet kapacitet såsom ActiveX, Java, webbläsare plugins och andra Windows-kontroller. Du distribuerar den här profilen med skriptet DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.

* **Skyddad** – En angripare som äventyrar ett administrativt konto kan orsaka betydande affärsskador genom datastöld, dataändringar eller avbrott i tjänsten. I det här härdade tillståndet aktiverar arbetsstationen alla säkerhetskontroller och principer som begränsar direkt kontroll av lokal programhantering. En säker arbetsstation har inga produktivitetsverktyg så att enheten är svårare att kompromissa med. Det blockerar den vanligaste vektorn för nätfiskeattacker: e-post och sociala medier. Den säkrade arbetsstationen kan distribueras med securitybaseline-skriptet Secure Workstation - Windows10 (1809).

   ![Säker arbetsstation](./media/concept-azure-managed-workstation/secure-workstation.png)

   En säker arbetsstation ger en administratör en härdad arbetsstation som har tydlig programkontroll och programskydd. Arbetsstationen använder autentiseringsuppgifter, enhetsskydd och exploateringsskydd för att skydda värden från skadligt beteende. Alla lokala diskar krypteras också med BitLocker.

* **Isolerade** – Det här anpassade offlinescenariot representerar den extrema änden av spektrumet. Det finns inga installationsskript för det här fallet. Du kan behöva hantera en affärskritisk funktion som kräver ett äldre operativsystem som inte stöds eller inte har skickats. Till exempel en produktionslinje med högt värde eller ett livsuppehållande system. Eftersom säkerhet är kritiskt och molntjänster inte är tillgängliga kan du hantera och uppdatera dessa datorer manuellt eller med en isolerad Active Directory-skogsarkitektur, till exempel ESAE (Enhanced Security Admin Environment). Under dessa omständigheter bör du överväga att ta bort all åtkomst utom grundläggande Intune- och ATP-hälsokontroller.

   * [Krav på intune-nätkommunikation](/intune/network-bandwidth-use)
   * [ATP-nätkommunikationskrav](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Nästa steg

[Distribuera en säker Azure-hanterad arbetsstation](howto-azure-managed-workstation.md).
