---
title: Överordnade inställningar i Azure SignalR service
description: Få en introduktion till överordnade inställningar och protokoll för överordnade meddelanden.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: be7736d0c90d1c384e15e8c7dee29d016b052dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559447"
---
# <a name="upstream-settings"></a>Överordnade inställningar

Överordnad är en funktion som gör det möjligt för Azure SignalR-tjänsten att skicka meddelanden och anslutnings händelser till en uppsättning slut punkter i Server fritt läge. Du kan använda uppströms för att anropa en nav-metod från klienter i Server lös läge och låta slut punkter bli aviserade när klient anslutningar är anslutna eller frånkopplade.

> [!NOTE]
> Endast Server fritt läge kan konfigurera överordnade inställningar.

## <a name="details-of-upstream-settings"></a>Information om överordnade inställningar

Överordnade inställningar består av en lista över ordnings känsliga objekt. Varje objekt består av:

* En URL-mall som anger var meddelanden skickas till.
* En uppsättning regler.
* Konfigurationer för autentisering. 

När den angivna händelsen inträffar, kontrol leras ett objekts regler en i ordning. Meddelanden skickas till det första matchande objektets överordnade URL.

### <a name="url-template-settings"></a>Inställningar för URL-mall

Du kan Parameterisera webb adressen för att stödja olika mönster. Det finns tre fördefinierade parametrar:

|Fördefinierad parameter|Beskrivning|
|---------|---------|
|)| En hubb är ett koncept för Azure SignalR-tjänsten. En hubb är en isolerad enhet. Omfattningen av användare och meddelande leverans är begränsad till en hubb.|
|kategori| En kategori kan vara något av följande värden: <ul><li>**anslutningar**: anslutnings livs längds händelser. Den utlöses när en klient anslutning ansluts eller kopplas från. Den innehåller anslutna och frånkopplade händelser.</li><li>**meddelanden**: utlöses när klienter anropar en nav-metod. Den innehåller alla andra händelser, förutom de i kategorin **anslutningar** .</li></ul>|
|händelse| I kategorin **meddelanden** är en händelse målet i ett [anrops meddelande](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) som klienter skickar. För kategorin **anslutningar** används endast *anslutna* och *frånkopplade* .|

Dessa fördefinierade parametrar kan användas i URL-mönstret. Parametrarna ersätts med ett angivet värde när du utvärderar den överordnade URL: en. Ett exempel: 
```
http://host.com/{hub}/api/{category}/{event}
```
När en klient anslutning i hubben "chatt" är ansluten, skickas ett meddelande till denna URL:
```
http://host.com/chat/api/connections/connected
```
När en klient i "chatt"-hubben anropar Hub `broadcast` -metoden skickas ett meddelande till denna URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>Regel inställningar

Du kan ange regler för *nav regler*, *kategori regler*och *händelse regler* separat. Matchnings regeln stöder tre format. Ta händelse regler som exempel:
- Använd en asterisk (*) för att matcha alla händelser.
- Använd ett kommatecken (,) för att koppla flera händelser. Matchar till exempel `connected, disconnected` de anslutna och frånkopplade händelserna.
- Använd det fullständiga händelse namnet för att matcha händelsen. Matchar till exempel `connected` den anslutna händelsen.

### <a name="authentication-settings"></a>Autentiseringsinställningar

Du kan konfigurera autentisering för varje överordnat inställnings objekt separat. När du konfigurerar autentisering anges en token i `Authentication` rubriken för det överordnade meddelandet. För närvarande stöder Azure Signaling-tjänsten följande autentiseringstyper:
- `None`
- `ManagedIdentity`

När du väljer `ManagedIdentity` måste du aktivera en hanterad identitet i Azure SignalR-tjänsten i förväg och även ange en resurs. Mer information finns i [Managed identiteter för Azure SignalR service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Skapa överordnade inställningar via Azure Portal

1. Gå till Azure SignalR-tjänsten.
2. Välj **Inställningar** och växla **tjänst läge** till **Server**lös. De överordnade inställningarna visas:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Överordnade inställningar":::

3. Lägg till URL: er under **mönster för överordnad URL**. Sedan visas standardvärdet i inställningar som **Hub-regler** .
4. Om du vill ange inställningar för **Hubbs regler**, **händelse regler**, **kategori regler**och **överordnad autentisering**, väljer du värdet för **nav-regler**. En sida där du kan redigera inställningar visas:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Överordnade inställningar":::

5. Om du vill ange en **överordnad autentisering**, se till att du har aktiverat en hanterad identitet först. Välj sedan **Använd hanterad identitet**. Beroende på dina behov kan du välja valfria alternativ under **resurs-ID för autentisering**. Mer information finns i [Managed identiteter för Azure SignalR service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Skapa överordnade inställningar via Resource Manager-mall

Ange egenskapen i egenskapen för att skapa överordnade inställningar med hjälp av en [Azure Resource Manager mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) `upstream` `properties` . Följande fragment visar hur du ställer in `upstream` egenskapen för att skapa och uppdatera överordnade inställningar.

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

## <a name="serverless-protocols"></a>Server lös protokoll

Azure SignalR service skickar meddelanden till slut punkter som följer följande protokoll.

### <a name="method"></a>Metod

POST

### <a name="request-header"></a>Begärandehuvud

|Name |Beskrivning|
|---------|---------|
|X-ASRS-Connection-ID |Anslutnings-ID för klient anslutningen.|
|X-ASRS – hubb |Hubben som klient anslutningen tillhör.|
|X-ASRS – kategori |Den kategori som meddelandet tillhör.|
|X-ASRS – händelse |Händelsen som meddelandet tillhör.|
|X-ASRS – signatur |En hash-baserad meddelande verifierings kod (HMAC) som används för verifiering. Se [signatur](#signature) för information.|
|X-ASRS-User-Claims |En grupp med anspråk för klient anslutningen.|
|X-ASRS-User-ID |Användar identiteten för den klient som skickar meddelandet.|
|X-ASRS – klient-fråga |Frågan om begäran när klienterna ansluter till tjänsten.|
|Autentisering |En valfri token när du använder `ManagedIdentity` . |

### <a name="request-body"></a>Begärandetext

#### <a name="connected"></a>Ansluten

Innehålls typ: Application/JSON

#### <a name="disconnected"></a>Frånkopplad

Innehålls typ:`application/json`

|Name  |Typ  |Beskrivning  |
|---------|---------|---------|
|Fel |sträng |Fel meddelandet för en stängd anslutning. Tom när anslutningar stängs utan fel.|

#### <a name="invocation-message"></a>Anrops meddelande

Innehålls typ: `application/json` eller`application/x-msgpack`

|Name  |Typ  |Beskrivning  |
|---------|---------|---------|
|InvocationId |sträng | En valfri sträng som representerar ett anrops meddelande. Hitta information om [anrop](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Mål |sträng | Samma som händelsen och samma som målet i ett [anrops meddelande](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argument |Objekt mat ris |En matris som innehåller argument som ska tillämpas på den metod som anges i `Target` . |

### <a name="signature"></a>Signatur

Tjänsten beräknar SHA256-koden för `X-ASRS-Connection-Id` värdet genom att använda både den primära åtkomst nyckeln och den sekundära åtkomst nyckeln som `HMAC` nyckel. Tjänsten ställer in den i `X-ASRS-Signature` rubriken när den gör HTTP-förfrågningar till uppladdning:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för Azure SignalR service](howto-use-managed-identity.md)
- [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](signalr-concept-serverless-development-config.md)
