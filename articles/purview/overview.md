---
title: Introduktion till Azure avdelningens kontroll (för hands version)
description: Den här artikeln innehåller en översikt över Azure-avdelningens kontroll, inklusive dess funktioner och de problem som den löser. Med Azure avdelningens kontroll kan alla användare registrera, identifiera, förstå och använda data källor.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: e7ad561cf9ce9983f41fa166eb744c90f126e01e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555977"
---
# <a name="what-is-azure-purview"></a>Vad är Azure avdelningens kontroll?

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

Azure avdelningens kontroll är en ny moln tjänst som används av data användare. Du kan använda Azure-avdelningens kontroll för att centralt hantera data styrning över din datafastighet, och sträcker sig över både moln-och lokal miljöer. Med den här tjänsten kan affärsanalytiker söka efter relevanta data med hjälp av meningsfulla affärs villkor.

Med avdelningens kontroll UI eller Apache Atlas API, kan tekniska användare visa metadata och härkomst för data till gångar i en central katalog.

Sak expert experter, data ägare och säkerhets administratörer kan kommentera data till gångar för att dela sin kommun kunskap. De gör detta med användar gränssnittet eller i skala genom automatisk klassificering och automatiskt märkta principer, baserat på metadata och innehålls granskning.

## <a name="discovery-challenges-for-data-consumers"></a>Det är svårt för datakonsumenterna att hitta rätt

Traditionellt är det att identifiera företags data källor som har en ekologisk process baserat på kommun kunskaper. För företag som vill ha det mest värdefulla värdet från sina informations till gångar visar den här metoden många utmaningar:

* Eftersom det inte finns någon central plats för att registrera data källor kan användarna vara medvetna om en data källa, om de inte kommer in i kontakt med den som en del av en annan process.
* Om inte användarna känner till platsen för en data källa kan de inte ansluta till data med hjälp av ett klient program. Dataförbrukningsupplevelser gör att användarna måste känner till anslutningssträngen eller sökvägen.
* Den avsedda användningen av data är dold för användare om de inte känner till platsen för en data källas dokumentation. Data källor och dokumentation kan finnas på flera platser och förbrukas via olika typer av upplevelser.
* Om användare har frågor om en informationsresurs måste de hitta experten eller teamet som ansvarar för data och prata med dem offline. Det finns ingen uttrycklig anslutning mellan data och experter som har perspektiv på sin användning.
* Om användarna inte förstår processen för att begära åtkomst till data källan kan de inte komma åt dessa data genom att identifiera data källan och dess dokumentation.

## <a name="discovery-challenges-for-data-producers"></a>Det är svårt för dataproducenterna att hitta rätt

Datakonsumenterna står inför vissa svårigheter, medan användarna som ansvarar för att producera och upprätthålla informationstillgångar står inför andra svårigheter:

* Det är ofta en onödig ansträngning att kommentera datakällor med beskrivande metadata. Klientprogram ignorerar vanligtvis beskrivningar som lagrats i datakällan.
* Det kan vara svårt att skapa dokumentation för data källor och det är ett kontinuerligt ansvar att hålla dokumentationen synkroniserad med data källor. Användare kanske inte litar på dokumentation som uppfattas som inaktuell.
* Det är komplicerat och tidskrävande att skapa och upprätthålla dokumentationen för en datakälla. Det är ofta ännu svårare att göra dokumentationen lättillgänglig för alla som använder datakällan.
* Det är en ständig utmaning att begränsa tillgången till datakällan och samtidigt se till att datakonsumenterna vet hur de begär tillgång.

När sådana utmaningar kombineras utgör de en betydande barriär för företag som vill uppmuntra och främja användning och förståelse av företags data.

## <a name="discovery-challenges-for-security-administrators"></a>Identifierings problem för säkerhets administratörer

Användare som är ansvariga för att säkerställa säkerheten för deras organisations data kan ha alla utmaningar som anges ovan som data konsumenter och producenter, samt följande ytterligare utmaningar:

* En organisations data växer ständigt, lagras och delas i nya riktningar. Uppgiften att upptäcka, skydda och övervaka känsliga data är en som aldrig tar slut. Du vill se till att organisationens innehåll delas med rätt personer, program och med rätt behörigheter.
* Att förstå risk nivåerna i din organisations data kräver simhopp djup i ditt innehåll, som söker efter nyckelord, RegEx-mönster och/eller känsliga data typer. Känsliga data typer kan innehålla kreditkorts nummer, person nummer eller bank konto nummer, för att ge några. Du måste ständigt övervaka alla data källor för känsligt innehåll, så att även den minsta mängden data förlust kan vara avgörande för din organisation.
* Att se till att din organisation fortsätter att följa företagets säkerhets principer är en utmaning när innehållet växer och ändras, och när dessa krav och principer uppdateras för att ändra digitala verkliga funktioner. Säkerhets administratörer är ofta inloggade med att garantera data säkerhet på snabbast möjliga tid.

## <a name="azure-purview-advantages"></a>Fördelar med Azure avdelningens kontroll

Azure avdelningens kontroll har utformats för att lösa problemen som nämns i föregående avsnitt och för att hjälpa företag att få ut det mesta värdet från sina befintliga informations till gångar. Katalogen gör att data källor enkelt kan identifieras och förstås av de användare som hanterar data.

Azure avdelningens kontroll tillhandahåller en molnbaserad tjänst där du kan registrera data källor. Under registreringen finns data kvar på den befintliga platsen, men en kopia av dess metadata läggs till i Azure-avdelningens kontroll, tillsammans med en referens till data käll platsen. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

När du har registrerat en data källa kan du sedan utöka dess metadata. Antingen lägger användaren som registrerade data källan eller någon annan användare i företaget metadata. Alla användare kan kommentera en data källa genom att tillhandahålla beskrivningar, taggar eller andra metadata för att begära åtkomst till data källan. Dessa beskrivande metadata kompletterar strukturella metadata, till exempel kolumn namn och data typer, som har registrerats från data källan.

Huvudsyftet med registrering av källorna är att identifiera och förstå datakällor och deras användningsområden. Företagsanvändare behöver data för business intelligence, programutveckling, datavetenskap eller andra uppgifter där rätt data krävs. De använder identifierings upplevelsen för Data Catalog för att snabbt hitta data som matchar deras behov, förstå data för att utvärdera dess lämplighet och använda data genom att öppna data källan i det verktyg som du väljer.

På samma gång kan användarna bidra till katalogen genom att tagga, dokumentera och kommentera datakällor som redan har registrerats. De kan också registrera nya data källor, som sedan identifieras, förstås och konsumeras av community-användarens katalog.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure avdelningens kontroll finns i [skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
