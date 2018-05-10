---
title: Blockchain arbetsstationen adresskonfigurationen som refereras till Azure
description: Azure Konfigurationsöversikt för Blockchain arbetsstationen i programmet.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Blockchain arbetsstationen adresskonfigurationen som refereras till Azure

 Azure Blockchain arbetsstationen program är flerparti arbetsflöden som definieras av konfigurationsmetadata och smarta kontraktkod. Konfigurationsmetadata definierar övergripande arbetsflöden och interaktion modell blockchain-programmet. Smart kontrakt definiera affärslogik blockchain programmet. Arbetsstationen använder konfiguration och smarta kontraktkod för att generera blockchain användarupplevelser för programmet.

Konfigurationsmetadata anger följande information för varje blockchain program: 

* Namn och beskrivning av programmet blockchain
* Unik roller om en användare kan arbeta eller delta i programmet blockchain
* Ett eller flera arbetsflöden. Varje arbetsflöde som fungerar som en tillståndsdator styr flödet av affärslogiken. Arbetsflöden kan vara oberoende eller interagera med varandra.

Varje definierat arbetsflöde anger du följande:

* Namn och beskrivning av arbetsflödet
* Tillstånd för arbetsflödet.  Varje tillstånd är en del i den affärslogik Kontrollflöde. 
* Åtgärder för att övergå till nästa steg
* Användarroller som har behörighet att initiera varje åtgärd
* Smart kontrakt som representerar affärslogik i kodfiler

## <a name="application"></a>Program

Ett blockchain program innehåller configuration metadata, arbetsflöden och användaren roller som kan fungera eller delta i programmet.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| ApplicationName | Unikt programnamn. Motsvarande smart kontrakt måste ha samma **ApplicationName** för tillämpliga avtal.  | Ja |
| Visningsnamn | Eget namn för programmet. | Ja |
| Beskrivning | Beskrivning av programmet. | Nej |
| ApplicationRoles | Samling av [ApplicationRoles](#application-roles). Användarroller som kan fungera eller delta i programmet.  | Ja |
| Arbetsflöden | Samling av [arbetsflöden](#workflows). Varje arbetsflöde som fungerar som en tillståndsdator styr flödet av affärslogiken. | Ja |

Ett exempel finns [exempel på en konfigurationsfil](#configuration-file-example).

## <a name="workflows"></a>Arbetsflöden

Affärslogiken för ett program kan modelleras som en tillståndsdator där vidta åtgärder gör att flödet av affärslogiken flytta från ett tillstånd till en annan. Ett arbetsflöde är en samling av dessa tillstånd och åtgärder. Varje arbetsflöde består av en eller flera smarta kontrakt som representerar affärslogiken i kodfiler. Ett körbart kontrakt är en instans av ett arbetsflöde.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Namn | För unika Arbetsflödesnamn. Motsvarande smart kontrakt måste ha samma **namn** för tillämpliga avtal. | Ja |
| Visningsnamn | Eget namn för arbetsflödet. | Ja |
| Beskrivning | Beskrivning av arbetsflödet. | Nej |
| Initierare | Samling av [ApplicationRoles](#application-roles). Roller som är kopplade till användare som har behörighet att skapa kontrakt i arbetsflödet. | Ja |
| StartState | Namnet på arbetsflödet initialt tillstånd. | Ja |
| Egenskaper | Samling av [identifierare](#identifiers). Representerar data som kan läsas av kedjan eller visualiserade i en användare få verktyget. | Ja |
| Konstruktorn | Definierar indataparametrar för att skapa en instans av arbetsflödet. | Ja |
| Functions | En samling [funktioner](#functions) som kan köras i arbetsflödet. | Ja |
| tillstånd | En samling av arbetsflöde [tillstånd](#states). | Ja |

Ett exempel finns [exempel på en konfigurationsfil](#configuration-file-example).

## <a name="type"></a>Typ

Vilka datatyper som stöds.

| Typ | Beskrivning |
|-------|-------------|
| Adress  | Blockchain-adresstyp, t.ex *kontrakt* eller *användare* |
| bool     | Boolesk datatyp |
| Kontrakt | Adress av typen kontrakt |
| int      | Datatypen Integer |
| Money    | Money-datatyp |
| state    | Arbetsflödets tillstånd |
| sträng   | Datatypen String |
| användare     | Adressen för typ användare |
| time     | Time-datatyp |
|`[ Application Role Name ]`| Alla namn som anges i programrollen. Begränsar användarna av den rolltypen. |

## <a name="constructor"></a>Konstruktorn

Definierar indataparametrar för en instans av ett arbetsflöde.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Parametrar | Samling av [identifierare](#identifiers) krävs för att initiera ett smart kontrakt. | Ja |

### <a name="constructor-example"></a>Konstruktorn exempel

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

Definierar funktioner som kan utföras på arbetsflödet.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Namn | Det unika namnet på funktionen. Motsvarande smart kontrakt måste ha samma **namn** för tillämplig funktion. | Ja |
| Visningsnamn | Eget namn för funktionen. | Ja |
| Beskrivning | Beskrivning av funktionen | Nej |
| Parametrar | Samling av [identifierare](#identifiers) motsvarande parametrar för funktionen. | Ja |

### <a name="functions-example"></a>Exempel på funktioner

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>tillstånd

En samling av unikt tillstånd i ett arbetsflöde. Varje tillstånd fångar upp ett steg i den affärslogik Kontrollflöde. 

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Namn | Unikt namn för tillståndet. Motsvarande smart kontrakt måste ha samma **namn** för tillämpligt tillstånd. | Ja |
| Visningsnamn | Eget namn för tillståndet. | Ja |
| Beskrivning | Beskrivning av tillståndet. | Nej |
| Värdet | Ett heltal som visas i användargränssnittet Blockchain arbetsstationen för att visa förlopp i business logic kontrollflödet. | Ja |
| Stil | Visuella tips som anger om tillståndet representerar en lyckad eller misslyckad status. Det finns två giltiga värden: `Success` eller `Failure`. | Ja |
| Övergångar | Samling av tillgängliga [övergångar](#transitions) från det aktuella tillståndet för en uppsättning tillstånd. | Nej |

### <a name="states-example"></a>Exempel på tillstånd

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Övergångar

Tillgängliga åtgärder till nästa steg. En eller flera roller kan utföra en åtgärd på varje tillstånd där åtgärden kan överföra ett tillstånd till ett annat tillstånd i arbetsflödet. 

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| AllowedRoles | Lista över program roller kan initiera övergången. Alla användare av den angivna rollen kanske att utföra åtgärden. | Nej |
| AllowedInstanceRoles | Lista med användarroller deltar eller anges i smart kontraktet får initiera övergången. Instansen roller har definierats i **egenskaper** i arbetsflöden. Dessa användare representerar en användare som deltar eller angetts i smart kontraktet och alla användare av en rolltyp. | Nej |
| Visningsnamn | Eget namn för övergången. | Ja |
| Beskrivning | Beskrivning av övergången. | Nej |
| Funktion | Namnet på funktionen för att initiera övergången. | Ja |
| NextStates | En samling av potentiella nästa tillstånd efter en lyckad övergång. | Ja |

### <a name="transitions-example"></a>Övergångar exempel

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Programroller

Programroller definiera roller som kan tilldelas till användare som vill vidta åtgärder eller delta i programmet. Programroller kan användas för att begränsa åtgärder och delta i blockchain program och motsvarande arbetsflöden. 

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Namn | Det unika namnet för programrollen. Motsvarande smart kontrakt måste ha samma **namn** för rollen tillämpliga. Bastypen namn är reserverade. Du kan inte namnge en roll för program med samma namn som [typ](#type)| Ja |
| Beskrivning | Beskrivning av programrollen. | Nej |

### <a name="application-roles-example"></a>Exempel på roller

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identifierare

Identifierare som representerar en samling information som används för att beskriva arbetsflödesegenskaper och konstruktorn funktionsparametrar. 

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Namn | Det unika namnet på egenskapen eller parametern. Motsvarande smart kontrakt måste ha samma **namn** för tillämpliga egenskapen eller parametern. | Ja |
| Visningsnamn | Eget namn för egenskapen eller parametern. | Ja |
| Beskrivning | Beskrivning av egenskapen eller parametern. | Nej |

### <a name="identifiers-example"></a>Identifierare exempel

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Exempel på en konfigurationsfil

I följande exempel definieras en grundläggande begäran och svar program där en begärande skickar en begäran och en Övervakare skickar ett svar på begäran.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Nästa steg

[Distribuera Azure Blockchain arbetsstationen](blockchain-workbench-deploy.md)

