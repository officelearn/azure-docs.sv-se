---
title: Planera din Azure Communication Services-telefoni och SMS-lösning
titleSuffix: An Azure Communication Services concept document
description: Lär dig att effektivt planera din användning av telefonnummer och telefoni.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947922"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planera din telefoni- och SMS-lösning

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

I det här dokumentet beskrivs olika telefoni planer och siffer typer som Azure Communication Services har att erbjuda. Vi går igenom besluts flödena för att hjälpa dig välja en röst tjänst leverantör, telefonnummer typer, planer och funktioner som gjorts tillgängliga via kommunikations tjänster.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Om telefonnummer i Azure Communications Services

Azure Communication Services gör att du kan använda telefonnummer för att placera och ta emot telefonsamtal och SMS-meddelanden. De här telefonnumren kan användas för att konfigurera uppringarens ID för utgående samtal som har lagts till av tjänsten.
  
Om du inte har ett befintligt telefonnummer som ska importeras till kommunikations tjänst lösningen är det enklaste sättet att börja med att skaffa ett nytt telefonnummer från Azure kommunikations tjänster på några minuter.

Om du har ett befintligt telefonnummer som du vill fortsätta använda i din lösning (till exempel 1 – 800 – företag) kan du Porta telefonnumret från den befintliga providern till kommunikations tjänster.

Följande diagram hjälper dig att navigera bland de tillgängliga alternativen:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagram över hur du fattar ett beslut angående dina telefonnummer.":::

Nu ska vi granska de telefonnummer typer och-funktioner som är tillgängliga via kommunikations tjänsterna. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Microsoft Direct-erbjudandet om telefonnummer och funktioner

Azure Communication Services ger en utmärkt flexibilitet för utvecklare. På de flesta telefonnummer kan du konfigurera en "a la carte"-uppsättning planer. Vissa utvecklare behöver bara en inkommande uppringnings plan. vissa kan välja inkommande uppringnings-och utgående SMS-planer. Dessa planer kan väljas när du lånar och/eller portar dina telefonnummer i kommunikations tjänsterna.

Vilka planer som är tillgängliga beror på vilken typ av land och telefonnummer du arbetar i. Diagrammet nedan visar besluts flödet: tillgängliga planer beror på vilket land och vilken typ av telefonnummer du arbetar i.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Diagram över hur du fattar ett beslut angående dina telefonnummer."::: -->

Innan du kan välja en telefonnummer typ ska vi gå igenom den internationella telefonens nummer plan.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Valfri läsning. Internationell offentlig telekommunikations nummer plan (E. 164)

> [!NOTE]
> Vi rekommenderar att du läser igenom den här informationen även om du är bekant med telefonnumret för E. 164-telefonnumret för att bättre förstå de siffer typer och funktioner som erbjuds av Azure Communication Services Direct-erbjudandet.

Den internationella offentliga telekommunikations nummer planen definieras i International Telecommunication Union (ITU) rekommendationen E. 164. Tecken som tal är begränsade till högst 15 siffror.

Telefonnumret består av

-   Prefixet "+"
-   Ett internationellt uppringnings-eller lands nummer (en, två eller tre siffror) 
-   *(Valfritt)* En nationell mål kod eller en siffer plan som vanligt vis kallas för ett rikt nummer. Längden på den här koden är beroende av landet. I USA är det tre siffror. I Australien och nya Zeeland är det en siffra. Tyskland, Japan, Mexiko och vissa andra länder har varierande längder för rikt koderna. I Tyskland kan rikt numret till exempel vara var som helst från två till fem siffror, och i Japan kan den vara var som helst från en till fem siffror.
-   Ett abonnent nummer

> [!NOTE]
> Klassificeringen ovan stämmer inte helt med ITU-E. 164-standarden och är avsedd att ge en förenklad beskrivning. Abonnent numret är till exempel indelat i standard. Om du är intresse rad av att lära dig mer om den internationella nummer planen är [itu-E. 164-standarden](https://www.itu.int/rec/T-REC-E.164) ett utmärkt ställe att börja på.  

Här följer några exempel som hjälper dig att bättre förstå nummer planen:

Ett regionalt telefonnummer i USA:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Diagram över hur du fattar ett beslut angående dina telefonnummer.":::

Ett regionalt telefonnummer i Kanada:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Diagram över hur du fattar ett beslut angående dina telefonnummer.":::

Ett avgiftsfritt nummer i Nordamerika region:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Diagram över hur du fattar ett beslut angående dina telefonnummer.":::

Ett mobiltelefon nummer i Storbritannien:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Diagram över hur du fattar ett beslut angående dina telefonnummer.":::

Nu ska vi granska vissa telefonnummer typer som är tillgängliga i Azure Communication Services.

## <a name="phone-number-types-in-azure-communication-services"></a>Telefonnummer typer i Azure Communication Services

Microsoft erbjuder regionala och kostnads fria planer för kort koder och mobiltelefon nummer i länder där det är tillämpligt.

I tabellen nedan sammanfattas dessa telefonnummer typer: 

| Typ av telefonnummer | Exempel                              | Lands tillgänglighet    | Vanligt användnings fall                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regional          | + 1 (geografiskt rikt nummer) XXX XX XX  | USA, Kanada, Puerto Rico | Tilldela telefonnummer till användare i dina program eller tilldela till IVR-system (Interactive Voice Response) och robotar |
| Avgiftsfritt         | + 1 (avgiftsfrit rikt *nummer*) xxx xx xx | USA, Kanada, Puerto Rico | Tilldela till IVR-system (Interactive Voice Response)/robotar, SMS-program                                        |

## <a name="plans"></a>Planer 

Nu ska vi titta på de funktioner som du kan aktivera för dina telefonnummer. De här funktionerna varierar beroende på land på grund av myndighets krav. Azure Communication Services erbjuder följande funktioner:

- **Envägs utgående SMS**, användbart för aviseringar och tvåfaktorautentisering.
- **Tvåvägs inkommande och utgående SMS**, ett fördefinierat paket som gör att SMS kan skickas och tas emot som en del av en plan.
- **PSTN-anrop**kan du välja en inkommande uppringning och använda anroparens ID för att placera utgående samtal.

## <a name="next-steps"></a>Nästa steg

### <a name="quickstarts"></a>Snabbstarter

- [Hämta ett telefonnummer](../../quickstarts/telephony-sms/get-phone-number.md)
- [Placera ett samtal](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Skicka ett SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Konceptuell dokumentation

- [Begrepp inom röst- och videosamtal](../voice-video-calling/about-call-types.md)
- [Samtals flöden och SMS-flöden](../call-flows.md)
- [Prissättning](../pricing.md)
