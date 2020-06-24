---
title: Överordnade inställningar i Azure SignalR service
description: Introduktion till överordnade inställningar och protokoll för överordnade meddelanden
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 7434e8796ddcd89968a0ffa0328a823d635f51c9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988719"
---
# <a name="upstream-settings"></a>Överordnade inställningar

Överordnad är en funktion som gör det möjligt för signal tjänsten att skicka meddelanden och anslutnings händelser till en uppsättning slut punkter i Server lös läge. Överordnade kan användas för att anropa Hub-metoden från klienter i ett Server fritt läge och låta slut punkter bli aviserade när klient anslutningar är anslutna eller frånkopplade.

> [!NOTE]
> Endast Server fritt läge kan konfigurera överordnade inställningar.

## <a name="upstream-settings-details"></a>Information om överordnade inställningar

Överordnade inställningar består av en lista över ordnings känsliga objekt. Varje objekt består av ett `URL Template` , som anger var meddelanden skickas till, en uppsättning `Rules` och `Authentication` konfigurationer. När den angivna händelsen inträffar, markeras objekt `Rules` ett i ordning och meddelanden skickas till det första matchande objektets överordnade URL.

### <a name="url-template-settings"></a>Inställningar för URL-mall

URL: en kan vara parameterstyrda för att stödja olika mönster. Det finns tre fördefinierade parametrar:

|Fördefinierad parameter|Beskrivning|
|---------|---------|
|)| Hubb är ett begrepp för Signalerare. Ett nav är en isolerad enhet, omfattningen av användare och meddelanden som levereras är begränsad till en hubb|
|kategori| Kategorin kan vara något av följande värden: <ul><li>**anslutningar**: anslutnings livs längds händelser. Utlöses när en klient anslutning ansluts eller kopplas från. Inklusive *anslutna* och *frånkopplade* händelser</li><li>**meddelanden**: utlöses när klienter anropar en nav-metod. Inklusive alla andra händelser utom de i kategorin *anslutningar*</li></ul>|
|händelse| I kategorin *meddelanden* är händelsen *målet* i [anrops meddelandet](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) som klienter skickar. För kategorin *anslutningar* används endast *anslutna* och *frånkopplade* .|

Dessa fördefinierade parametrar kan användas i `URL Pattern` parametrarna och kommer att ersättas med ett angivet värde när du utvärderar den överordnade URL: en. T.ex. 
```
http://host.com/{hub}/api/{category}/{event}
```
När en klient anslutning i hubben Chat är ansluten, skickas ett meddelande till URL: en:
```
http://host.com/chat/api/connections/connected
```
När en klient i hubb `chat` anropar Hub `broadcast` -metoden skickas ett meddelande till URL: en:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rules-settings"></a>Regel inställningar

Du kan ange regler för *nav regler*, *kategori regler* och *händelse regler* separat. Matchnings regeln stöder tre format. Ta *händelse regler* som exempel:
- Använd asterisk (*) för att matcha alla händelser.
- Användarens kommatecken (,) för att ansluta till flera händelser. Matchar till exempel `connected, disconnected` händelser som *är anslutna* och *frånkopplade*.
- Använd det fullständiga händelse namnet för att matcha händelsen. Matchar till exempel `connected` den *anslutna* händelsen.

### <a name="authentication-settings"></a>Autentiseringsinställningar

Du kan konfigurera *autentisering* för varje överordnat inställnings objekt separat. När den konfigurerade *autentiseringen*anges en token i det överordnade meddelandets *Authentication* -huvud. Aktuell, signal tjänst har stöd för följande autentiseringstyp
- Inga
- ManagedIdentity

När du väljer *ManagedIdentity*måste du aktivera hanterad identitet i signaler-tjänsten i förväg och välja en *resurs*. Mer information finns i [använda hanterade identiteter för Azure SignalR-tjänsten](howto-use-managed-identity.md) .

## <a name="create-upstream-settings"></a>Skapa överordnade inställningar

### <a name="create-upstream-settings-via-azure-portal"></a>Skapa överordnade inställningar via Azure Portal

1. Gå till Azure SignalR-tjänsten.
2. Klicka i *inställningarna* bland och växla *tjänst läge* till *Server*lös. De *överordnade inställningarna* visas som nedan:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Överordnade inställningar":::

3. Fylla i URL: er till *uppströms URL-mönster*. sedan kommer inställningar som *Hub-regler* att Visa standardvärdet.
4. Om du vill ange inställningar som *Hubbs regler*, *händelse regler*, *kategori regler* och *inströms-autentisering*klickar du på värdet för *Hub-regler*. En sida som gör att du kan redigera inställningarna visas på det sätt som visas nedan:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Överordnade inställningar":::

5. Om du vill ange en *överordnad autentisering*kontrollerar du först att du har aktiverat hanterad identitet och väljer sedan *Använd hanterad identitet* under *överordnad autentisering*. Beroende på dina behov kan du välja valfria alternativ under *resurs-ID för autentisering*. Mer information finns i [så här aktiverar du hanterad identitet](howto-use-managed-identity.md) .

### <a name="create-upstream-settings-via-arm-template"></a>Skapa överordnade inställningar via ARM-mallen

Ange egenskapen i egenskapen för att skapa överordnade inställningar med en [Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) `upstream` `properties` . Följande kodfragment visar hur du ställer in `upstream` egenskapen för att skapa och uppdatera överordnade inställningar.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="signalr-serverless-protocol"></a>Signalerar Server lös protokoll

Signal tjänsten skickar meddelanden till slut punkter som följer följande protokoll.

### <a name="method"></a>Metod

POST

### <a name="request-header"></a>Begärandehuvud

|Name |Beskrivning|
|---------|---------|
|X-ASRS-Connection-ID |Anslutnings-ID för klient anslutningen|
|X-ASRS – hubb |Hubben som klient anslutningen tillhör|
|X-ASRS – kategori |Den kategori som meddelandet tillhör|
|X-ASRS – händelse |Händelsen som meddelandet tillhör|
|X-ASRS – signatur |En HMAC som används för verifiering. Se [signatur](#signature) för information.|
|X-ASRS-User-Claims |En grupp med anspråk för klient anslutningen|
|X-ASRS-User-ID |Användar identiteten för klienten som skickar meddelandet|
|X-ASRS – klient-fråga |Frågan på begäran när klienter ansluter till tjänsten|
|Autentisering |En valfri token när du använder *ManagedIdentity* |

### <a name="request-body"></a>Begärandetext

#### <a name="connected"></a>Ansluten

Innehålls typ: Application/JSON

#### <a name="disconnected"></a>Frånkopplad

Innehålls typ:`application/json`

|Name  |Typ  |Description  |
|---------|---------|---------|
|Fel |sträng |Fel meddelandet om anslutningen har stängts. Tom när anslutningar stängs utan fel|

#### <a name="invocation-message"></a>Anrops meddelande

Innehålls typ: `application/json` eller`application/x-msgpack`

|Name  |Typ  |Beskrivning  |
|---------|---------|---------|
|InvocationId |sträng | En valfri sträng representerar ett anrops meddelande. Hitta information om [anrop](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Mål |sträng | Samma som *händelse* och samma som *målet* i [anrops meddelandet](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argument |Objekt mat ris |En matris som innehåller argument som ska användas för den metod som refereras till i mål. |

### <a name="signature"></a>Signatur

Tjänsten beräknar SHA256-koden för `X-ASRS-Connection-Id` värdet med hjälp av både den primära åtkomst nyckeln och den sekundära åtkomst nyckeln som `HMAC` nyckeln, och kommer att ställa in den i `X-ASRS-Signature` rubriken när du gör HTTP-förfrågningar till den överordnade:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Nästa steg

- [Använda hanterade identiteter för Azure SignalR service](howto-use-managed-identity.md)
- [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](signalr-concept-serverless-development-config.md)
