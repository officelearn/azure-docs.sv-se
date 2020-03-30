---
title: Konfigurera en gateway för dirigerarfrågningar
description: Lär dig hur du konfigurerar gatewayen som hanterar inkommande trafik för dina program som körs på Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461963"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurera en gateway-resurs för att dirigera begäranden

En gateway-resurs används för att dirigera inkommande trafik till nätverket som rymmer ditt program. Konfigurera den för att ange regler genom vilka begäranden dirigeras till specifika tjänster eller slutpunkter baserat på begärans struktur. Mer information om nätverk och gateways i Mesh finns [i Introduktion till nätverk i Service Fabric Mesh.](service-fabric-mesh-networks-and-gateways.md) 

Gateway-resurser måste deklareras som en del av distributionsmallen (JSON eller yaml) och är beroende av en nätverksresurs. Det här dokumentet beskriver de olika egenskaper som kan ställas in för gatewayen och täcker en exempelgateway config.

## <a name="options-for-configuring-your-gateway-resource"></a>Alternativ för att konfigurera gatewayresursen

Eftersom gateway-resursen fungerar som en brygga mellan programmets nätverk och den `open` underliggande infrastrukturens nätverk (nätverket). Du bör bara behöva konfigurera en (i förhandsversionen av nätet finns det en gräns för en gateway per app). Deklarationen för resursen består av två huvuddelar: resursmetadata och egenskaperna. 

### <a name="gateway-resource-metadata"></a>Metadata för gateway-resurs

En gateway deklareras med följande metadata:
* `apiVersion`- måste ställas in på "2018-09-01-preview" (eller senare, i framtiden)
* `name`- ett strängnamn för den här gatewayen
* `type`- "Microsoft.ServiceFabricMesh/gateways"
* `location`- bör ställas in på platsen för din app / nätverk; vanligtvis är en referens till platsparametern i distributionen
* `dependsOn`- det nätverk för vilket denna inkörsport kommer att fungera som en ingresspunkt för

Så här ser det ut i en JSON-distributionsmall (Azure Resource Manager): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Gateway-egenskaper

Egenskapsavsnittet används för att definiera de nätverk som gatewayen ligger mellan och reglerna för routningsbegäranden. 

#### <a name="source-and-destination-network"></a>Käll- och målnätverk 

Varje gateway `sourceNetwork` kräver `destinationNetwork`en och . Källnätverket definieras som det nätverk som appen tar emot inkommande begäranden från. Dess namn egenskap bör alltid ställas in på "Öppna". Målnätverket är det nätverk som begärandena riktar in sig på. Namnvärdet för detta ska anges till resursnamnet för appens lokala nätverk (bör innehålla fullständig referens till resursen). Se nedan för ett exempel config av hur detta ser ut för en distribution i ett nätverk som kallas "myNetwork".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regler 

En gateway kan ha flera routningsregler som anger hur inkommande trafik ska hanteras. En routningsregel definierar relationen mellan lyssningsporten och målslutpunkten för ett visst program. För TCP-routningsregler finns det en 1:1-mappning mellan Port:Endpoint. För HTTP-routningsregler kan du ange mer komplexa routningsregler som undersöker sökvägen till begäran och eventuellt rubriker för att bestämma hur begäran ska dirigeras. 

Routningsregler anges per port. Varje ingående port har sin egen matris med regler inom egenskapsdelen av gateway-konfigurationen. 

#### <a name="tcp-routing-rules"></a>TCP-routningsregler 

En TCP-routningsregel består av följande egenskaper: 
* `name`- hänvisning till den regel som kan vara vilken sträng som helst som du väljer 
* `port`- port för att lyssna på för inkommande förfrågningar 
* `destination`- Slutpunktsspecifikation `serviceName`som `endpointName`omfattar, `applicationName`och, för var förfrågningarna måste dirigeras till

Här är ett exempel på TCP-routningsregel:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP-routningsregler 

En HTTP-routningsregel består av följande egenskaper: 
* `name`- hänvisning till den regel som kan vara vilken sträng som helst som du väljer 
* `port`- port för att lyssna på för inkommande förfrågningar 
* `hosts`- En rad principer som gäller för förfrågningar som kommer till de olika "värdar" på porten som anges ovan. Värdar är den uppsättning program och tjänster som kan köras i nätverket och kan betjäna inkommande förfrågningar, dvs en webbapp. Värdprinciper tolkas i ordning, så du bör skapa följande i fallande specificitetsnivåer
    * `name`- DNS-namnet på den värd för vilken följande routningsregler anges. Om du använder "*" här skapas routningsregler för alla värdar.
    * `routes`- En rad principer för denna specifika värd
        * `match`- Specifikation av den inkommande begäranheten för att denna regel ska tillämpas, på grundval av en`path`
            * `path`- innehåller `value` en (inkommande `rewrite` URI), (hur du vill att `type` begäran ska vidarebefordras), och en (kan för närvarande endast vara "Prefix")
            * `header`- är en valfri matris med rubriker värden som matchar i begärans huvud att om begäran matchar sökvägen specifikationen (ovan).
              * varje post `name` innehåller (strängnamnet på rubriken `value` som ska matchas), (strängvärdet `type` för huvudet i begäran) och en (kan för närvarande endast vara "Exakt")
        * `destination`- Om begäran matchar, kommer den att dirigeras till denna `applicationName` `serviceName`destination, som anges med hjälp av en , och`endpointName`

Här är ett exempel på HTTP-routningsregel som skulle gälla för begäranden som kommer på port 80, för alla värdar som betjänas av appar i det här nätverket. Om url:en för begäran har en struktur som `<IPAddress>:80/pickme/<requestContent>`matchar sökvägsspecifikationen, dvs. `myListener`  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Exempel på konfiguration för en gateway-resurs 

Här är vad en fullständig Gateway resurs config ser ut (detta är anpassad från ingressprovet finns i [Mesh prover repo](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Den här gatewayen är konfigurerad för ett Linux-program, "meshAppLinux", som består av minst två tjänster, "helloWorldService" och "counterService", som lyssnar på port 80. Beroende på URL-strukturen för den inkommande begäran dirigeras begäran till en av dessa tjänster. 
* "\<IPAddress>:80/helloWorld /\<\>begäran " skulle resultera i en begäran riktas till "helloWorldListener" i helloWorldService. 
* "\<IPAddress>:80/counter/\<request\>" skulle resultera i att en begäran dirigeras till "counterListener" i counterService. 

## <a name="next-steps"></a>Nästa steg
* Distribuera [exemplet Ingress](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) för att se gateways i aktion
