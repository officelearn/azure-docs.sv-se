---
title: Konfigurera en Gateway för att dirigera begäranden | Microsoft Docs
description: Lär dig mer om att konfigurera gatewayen som hanterar inkommande trafik för dina program som körs på Service Fabric-nät.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2e2502e35b3720ddbfe5950b89e2388de378f2ba
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528126"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurera en resurs för gatewayen för begäranden

En resurs för gatewayen används för att dirigera inkommande trafik till det nätverket där ditt program. Konfigurera den att ange regler som begäranden dirigeras till specifika tjänster eller slutpunkter baserat på strukturen för begäran. Se [introduktion till nätverk i Service Fabric-nät](service-fabric-mesh-networks-and-gateways.md) mer information om nätverk och gateways i nät. 

Gateway-resurser måste deklareras som en del av din Distributionsmall (JSON eller yaml) och är beroende av en nätverksresurs. Det här dokumentet beskriver de olika egenskaper som kan ställas in för din gateway och täcker en exempel-gateway-konfiguration.

## <a name="options-for-configuring-your-gateway-resource"></a>Alternativ för att konfigurera din Gateway-resurs

Eftersom gatewayresursen fungerar som en brygga mellan ditt program och den underliggande infrastrukturen (den `open` nätverk). Du behöver bara konfigurera en (förhandsverison nät, det finns en gräns på en gateway per app). Deklarationen för resursen består av två delar: resursmetadata och egenskaper. 

### <a name="gateway-resource-metadata"></a>Metadata för gateway-resurs

En gateway har deklarerats med följande metadata:
* `apiVersion` -måste anges till ”2018-09-01-preview” (eller senare, i framtiden)
* `name` -ett namn för anslutningssträngen för den här gatewayen
* `type` -”Microsoft.ServiceFabricMesh/gateways”
* `location` -måste vara inställt på platsen för din app / nätverk; Vanligtvis är en referens till parametern plats i din distribution
* `dependsOn` -nätverket som den här gatewayen kommer att fungera som en ingående tidpunkt för

Här är hur den ser ut i en Distributionsmall av Azure Resource Manager (JSON): 

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

Egenskapsavsnittet används för att definiera de nätverk som ligger gatewayen och regler för dirigering av begäranden. 

#### <a name="source-and-destination-network"></a>Käll- och nätverk 

Varje gateway kräver en `sourceNetwork` och `destinationNetwork`. Källnätverket definieras som det nätverk som din app får inkommande begäranden. Dess namnegenskapen ska alltid vara inställd på ”Öppna”. Målnätverket är nätverket som riktar in sig begäranden. Namn-värde för det här bör vara inställd resursnamnet för din app lokalt nätverk (bör inkludera fullständig referens till resursen). Nedan finns en exempel-konfiguration av hur detta ser ut för en distribution i ett nätverk med namnet ”myNetwork”.

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

En gateway kan ha flera routning regler som anger hur inkommande trafik ska hanteras. En routningsregel definierar relationen mellan lyssningsporten och målslutpunkt för ett visst program. Det finns en 1:1-mappning mellan Port: slutpunkten för TCP routningsregler. Du kan ange mer komplexa regler för routning som Kontrollera sökvägen för begäran, och du kan också rubriker, att bestämma hur begäran kommer att dirigeras för regler för HTTP-routning. 

Routningsregler anges på basis av per port. Varje inkommande port har sin egen uppsättning regler i egenskapsavsnittet i gateway-konfiguration. 

#### <a name="tcp-routing-rules"></a>Routningsregler för TCP 

En regel för vidarebefordran av TCP består av följande egenskaper: 
* `name` -Referens för den regel som kan vara valfri sträng med valfritt 
* `port` -porten som ska lyssna på efter inkommande begäranden 
* `destination` -endpoint-specifikationen som innehåller `applicationName`, `serviceName`, och `endpointName`, för där begäranden måste dirigeras till

Här är en regel för vidarebefordran av exempel TCP:

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


#### <a name="http-routing-rules"></a>Regler för routning av HTTP 

En regel för vidarebefordran av HTTP-består av följande egenskaper: 
* `name` -Referens för den regel som kan vara valfri sträng med valfritt 
* `port` -porten som ska lyssna på efter inkommande begäranden 
* `hosts` -en matris med principer som gäller för begäranden som kommer till olika ”värdar” på den port som anges ovan. Värdar är en uppsättning program och tjänster som kan köras i nätverket och kan hantera inkommande begäranden, dvs. en webbapp. Principer för värden tolkas i ordning, så bör du skapa följande i fallande nivåer av dessutom
    * `name` -DNS-namnet på den värd som följande regler för vidarebefordran har angetts. Med hjälp av ”*” här skulle skapa routningsregler för alla värdar.
    * `routes` -en matris med principer för den här specifika värden
        * `match` -specifikation av inkommande begäran strukturen för den här regeln tillämpas, baserat på en `path`
            * `path` -innehåller en `value` (inkommande URI) `rewrite` (hur du vill att begäran om att vidarebefordras), och en `type` (för närvarande kan bara vara ”prefixet”)
            * `header` -är en valfri matris av huvuden värdena för att matcha i begärans-huvudet att om begäran matchar sökvägsuttrycket (ovan).
              * varje post innehåller `name` (sträng namnet på rubriken så att den matchar), `value` (string värdet för huvudet i begäran), och en `type` (för närvarande kan bara vara ”Exact”)
        * `destination` -Om begäran matchar, kommer den att dirigeras till den här destinationen som anges med hjälp av en `applicationName`, `serviceName`, och `endpointName`

Här är ett exempel HTTP-routningsregel som gäller för begäranden som kommer på port 80, för alla värdar som hanteras av appar i det här nätverket. Om URL: en för begäran om en struktur som matchar sökvägsuttrycket, d.v.s. `<IPAddress>:80/pickme/<requestContent>`, och sedan dirigeras till den `myListener` slutpunkt.  

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

## <a name="sample-config-for-a-gateway-resource"></a>Exempel-konfigurationen för en resurs för gatewayen 

Här är en fullständig Gateway resurskonfiguration ser det ut (detta har anpassats från det ingående exemplet som är tillgängligt i den [nät exempel lagringsplatsen](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

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

Den här gatewayen har konfigurerats för ett program för Linux, ”meshAppLinux”, som består av minst två tjänster, ”helloWorldService” och ”counterService” som lyssnar på port 80. Beroende på URL-struktur för inkommande begäran dirigerar den begäran till någon av dessa tjänster. 
* ”\<IP-adress >: 80/helloWorld/\<begäran\>” skulle resultera i en begäran dirigeras till ”helloWorldListener” i helloWorldService. 
* ”\<IP-adress >: 80/räknare/\<begäran\>” skulle resultera i en begäran dirigeras till ”counterListener” i counterService. 

## <a name="next-steps"></a>Nästa steg
* Distribuera den [ingående exempel](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) att se gateways fungerar i praktiken
