---
title: Blockchain arbetsstationen adresskonfigurationen som refereras till Azure
description: Azure Konfigurationsöversikt för Blockchain arbetsstationen i programmet.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 178c2c1d4f727241338d6d933cd5eecbbffe65bb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303822"
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
| DisplayName | Eget namn för programmet. | Ja |
| Beskrivning | Beskrivning av programmet. | Nej |
| ApplicationRoles | Samling av [ApplicationRoles](#application-roles). Användarroller som kan fungera eller delta i programmet.  | Ja |
| Arbetsflöden | Samling av [arbetsflöden](#workflows). Varje arbetsflöde som fungerar som en tillståndsdator styr flödet av affärslogiken. | Ja |

Ett exempel finns [exempel på en konfigurationsfil](#configuration-file-example).

## <a name="workflows"></a>Arbetsflöden

Affärslogiken för ett program kan modelleras som en tillståndsdator där vidta åtgärder gör att flödet av affärslogiken flytta från ett tillstånd till en annan. Ett arbetsflöde är en samling av dessa tillstånd och åtgärder. Varje arbetsflöde består av en eller flera smarta kontrakt som representerar affärslogiken i kodfiler. Ett körbart kontrakt är en instans av ett arbetsflöde.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Namn | För unika Arbetsflödesnamn. Motsvarande smart kontrakt måste ha samma **namn** för tillämpliga avtal. | Ja |
| DisplayName | Eget namn för arbetsflödet. | Ja |
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
| Användare     | Adressen för typ användare |
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
| DisplayName | Eget namn för funktionen. | Ja |
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
| DisplayName | Eget namn för tillståndet. | Ja |
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
| AllowedInstanceRoles | Lista med användarroller deltar eller anges i smart kontraktet får initiera övergången. Instansen roller har definierats i **egenskaper** i arbetsflöden. AllowedInstanceRoles representerar en användare som ingår i en instans av ett smart kontrakt. AllowedInstanceRoles ger dig möjlighet att begränsa vidta åtgärder till en användarroll i en instans av kontrakt.  Exempelvis kanske du bara vill att användaren som skapade kontraktet (InstanceOwner) för att kunna avsluta i stället för alla användare i rolltyp (ägare) om du har angett rollen i AllowedRoles. | Nej |
| DisplayName | Eget namn för övergången. | Ja |
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
| DisplayName | Eget namn för egenskapen eller parametern. | Ja |
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

Överföringen tillgången är ett smart kontraktet scenario för handel värdefulla tillgångar, vilket kräver en inspector och appraiser. Säljare kan ange sina tillgångar genom en instans skapades av en tillgång överföring smart kontraktet. Centraliserad kan göra erbjudanden genom att utföra en åtgärd på smart kontraktet och andra parter kan vidta åtgärder för att granska och bedöma tillgången. När tillgången markeras både kontrolleras och ställer, centraliserad och säljaren bekräftar försäljning igen innan kontraktet anges för att slutföra. Alla deltagare ha inblick i tillståndet för kontraktet när den uppdateras vid varje punkt i processen. 

Mer information, inklusive kodfilerna, se [tillgången överföring exempel för Azure Blockchain arbetsstationen](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Följande konfigurationsfil är för tillgångsinformation överföra:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
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
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
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
      },
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
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
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
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
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
    }
  ]
}
```
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Blockchain arbetsstationen REST API-referens](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

