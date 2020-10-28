---
title: Översikt över Azure attestering
description: En översikt över Microsoft Azure attestering, en lösning för att intyga betrodda körnings miljöer (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 2ee906b406f5fd09fc870626f1905541a4270c66
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670535"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (förhandsversion)

Microsoft Azure attestering (för hands version) är en lösning för att intyga betrodda körnings miljöer (TEEs) som [Intel® Software Guard-tillägg](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) Enclaves och [virtualiseringsbaserad säkerhet](/windows-hardware/design/device-experiences/oem-vbs) (VBS) enclaves. Enklaven-attestering är en process för att verifiera att en enklaven är säker och pålitlig.

Attestering är en process för att demonstrera att binärfiler för program vara korrekt instansierades på en betrodd plattform. Fjärranslutna förlitande parter kan sedan få förtroende för att endast sådan avsedd program vara körs på betrodd maskin vara. Azure-attestering är en enhetlig kund riktad tjänst och ramverk för attestering.

Azure-attestering möjliggör nyskapande säkerhets paradigm, till exempel [Azures konfidentiella data behandling](../confidential-computing/overview.md) och intelligent Edge-skydd. Kunder har begärt möjligheten att oberoende kontrol lera platsen för en dator, position för en virtuell dator (VM) på den datorn och miljön där enclaves körs på den virtuella datorn. Azure-attesteringen ger dig befogenhet att göra detta och många ytterligare kund förfrågningar.

Azure-attesteringen tar emot bevis från beräknings enheter, förvandlar dem till en uppsättning anspråk, validerar dem mot konfigurerbara principer och ger kryptografiska bevis för anspråksmedvetna program (till exempel förlitande parter och gransknings myndigheter).

## <a name="use-cases"></a>Användningsfall

Azure-attestering tillhandahåller omfattande attesterings tjänster för flera miljöer och olika användnings fall.

### <a name="sgx-attestation"></a>SGX-attestering

SGX syftar på isolering av maskin varu klass, vilket stöds på vissa modeller för Intel-processorer. SGX gör det möjligt att köra kod i sanerade utrymmen som kallas SGX-enclaves. Åtkomst-och minnes behörigheter hanteras sedan av maskin vara för att säkerställa en minimal attack yta med korrekt isolering.

Klient program kan utformas för att dra nytta av SGX-enclaves genom att delegera säkerhets känsliga uppgifter som ska äga rum inuti dessa enclaves. Dessa program kan sedan använda Azure-attestering för att rutinmässigt upprätta förtroende i enklaven och dess möjlighet att komma åt känsliga data.

### <a name="vbs-attestation"></a>VBS-attestering

VBS är en programvarubaserad arkitektur för ett enklaven minnes skydd baserat på Hyper-V. Den förhindrar värd administratörs kod, samt lokala och moln tjänst administratörer från att komma åt data i en VBS-enklaven eller påverka dess körning.

I likhet med SGX-tekniken stöder Azure attestering verifierar VBS-enclaves mot konfigurerade principer och utfärdar en certifierings instruktion som bevis på giltighet.

### <a name="open-enclave"></a>Öppna enklaven
[Open enklaven](https://openenclave.io/sdk/) (OE) är en samling bibliotek som är riktade till att skapa en enda enhetlig enclaving-abstraktion för utvecklare för att skapa tee-baserade program. Den erbjuder en universell säker app-modell som minimerar plattforms information. Microsoft visar den som en viktig steg-vid-bricka mot democratizing maskinvarubaserade enklaven-tekniker som SGX och ökar sin upptagning på Azure.

OE standardiserar särskilda krav för verifiering av enklaven-bevis. Detta kvalificerar OE som en mycket anpassningsbar förbrukare konsument för Azure-attestering.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure-attestering kan köras i en TEE

Azure-attestering är avgörande för konfidentiella beräknings scenarier, eftersom det utför följande åtgärder:

- Kontrollerar om enklaven-beviset är giltigt.
- Utvärderar enklaven-beviset mot en kunddefinierad princip.
- Hanterar och lagrar principer för innehavaradministration.
- Genererar och signerar en token som används av förlitande parter för att interagera med enklaven.

Azure-attestering har skapats för att köras i två typer av miljöer:
- Azure-attestering som körs i en SGX-aktiverad TEE.
- Azure-attestering körs i en icke-TEE.

Kunder med Azure-attestering har utgivit ett krav för att Microsoft ska kunna utföra åtgärder från den betrodda beräknings basen (TCB). Detta är att förhindra att Microsoft-entiteter som VM-administratörer, värd administratörer och Microsoft-utvecklare kan ändra begäran om attestering, principer och Azure-attestering – utfärdade tokens. Azure-attestering är också utformat för att köras i TEE, där funktionerna i Azure-attestering som offert validering, token-generering och Token-signering flyttas till en SGX-enklaven.

## <a name="why-use-azure-attestation"></a>Varför använda Azure-attestering

Azure-attestering är det bästa valet för att intyga TEEs eftersom det ger följande fördelar: 

- Enhetligt ramverk för att intyga flera TEEs, till exempel SGX enclaves och VBS enclaves
- Tjänst för flera innehavare som gör det möjligt att konfigurera anpassade leverantörer och principer för utfärdande av token
- Erbjuder standardproviders som kan attestera utan konfiguration från användare
- Skyddar dess data vid användning med implementering i en SGX-enklaven
- Hög tillgänglig tjänst som erbjuder Serviceavtal (SLA)

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Support för affärs kontinuitet och haveri beredskap (BCDR)

[Verksamhets kontinuitet och haveri beredskap](../best-practices-availability-paired-regions.md) (BCDR) för Azure-attestering gör det möjligt att minska tjänst störningar som orsakas av betydande tillgänglighets problem eller katastrof händelser i en region.

Nedan visas de regioner som för närvarande stöds av BCDR
- USA, östra 2 => tillsammans med centrala USA.
- Centrala USA => paras ihop med USA, östra 2.

Kluster som distribueras i två regioner kommer att fungera oberoende under normala förhållanden. Om det uppstår ett fel eller avbrott i en region sker följande:

- Azure attestering BCDR ger sömlös redundans där kunder inte behöver göra något extra steg för att återställa
- [Azure-Traffic Manager](../traffic-manager/index.yml) för regionen identifierar att hälso avsökningen är försämrad och växlar slut punkten till kopplad region
- Befintliga anslutningar fungerar inte och får ett internt Server fel eller tids gräns problem
- Alla kontroll Plans åtgärder kommer att blockeras. Kunder kommer inte att kunna skapa attesterings leverantörer och uppdatera principer i den primära regionen
- Alla data Plans åtgärder, inklusive attesterings anrop, fortsätter att fungera i primär region

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [grundläggande koncept för Azure-attestering](basic-concepts.md)
- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)