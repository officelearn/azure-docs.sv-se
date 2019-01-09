---
title: Referens för Azure Blockchain Workbench-konfiguration
description: Azure Blockchain Workbench programmet configuration-Översikt.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: f93bfcb076bfae5c50c751ac664a145e1b375f23
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107777"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referens för Azure Blockchain Workbench-konfiguration

 Azure Blockchain Workbench-program är flerparti arbetsflöden som definieras av konfigurationsmetadata och smarta kontraktkod. Konfigurationsmetadata definierar avancerade arbetsflöden och interaktion modell blockchain-program. Smarta kontrakt definiera affärslogiken i blockchain-program. Workbench använder konfiguration och smarta kontraktkod för att generera användarupplevelser för blockchain-program.

Konfigurationsmetadata anger följande information för varje blockchain-program:

* Namn och en beskrivning av blockchain-program
* Unikt roller för användare som kan fungera eller delta i blockchain-program
* Ett eller flera arbetsflöden. Varje arbetsflöde fungerar som en tillståndsdator att styra flödet av affärslogik. Arbetsflöden kan vara oberoende eller interagera med varandra.

Varje definierad arbetsflöde anger du följande:

* Namn och en beskrivning av arbetsflödet
* Tillstånd för arbetsflödet.  Varje tillstånd är en del i den affärslogik Kontrollflöde. 
* Åtgärder för att gå över till nästa steg
* Användarroller som tillåts att initiera varje åtgärd
* Smarta kontrakt som representerar affärslogik i kodfiler

## <a name="application"></a>Program

En blockchain-program innehåller configuration metadata, arbetsflöden och användaren roller som kan fungera eller delta i programmet.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| ApplicationName | Unikt programnamn. Motsvarande smarta kontrakt måste använda samma **ApplicationName** för tillämpliga avtal.  | Ja |
| DisplayName | Eget namn för programmet. | Ja |
| Beskrivning | Beskrivning av programmet. | Nej |
| ApplicationRoles | Insamling av [ApplicationRoles](#application-roles). Användarroller som kan fungera eller delta i programmet.  | Ja |
| Arbetsflöden | Insamling av [arbetsflöden](#workflows). Varje arbetsflöde fungerar som en tillståndsdator att styra flödet av affärslogik. | Ja |

Ett exempel finns i [exempel på en konfigurationsfil](#configuration-file-example).

## <a name="workflows"></a>Arbetsflöden

Ett programs affärslogik kan modelleras som en tillståndsdator där vidta åtgärder gör att flödet av affärslogik flytta från ett tillstånd till en annan. Ett arbetsflöde är en samling av sådana tillstånd och åtgärder. Varje arbetsflöde består av en eller flera smarta kontrakt som representerar affärslogiken i kodfiler. Ett körbart kontrakt är en instans av ett arbetsflöde.

| Fält | Beskrivning | Krävs | Maxlängd |
|-------|-------------|:--------:|-----------:|
| Namn | För unika Arbetsflödesnamn. Motsvarande smarta kontrakt måste använda samma **namn** för tillämpliga avtal. | Ja | 50 |
| DisplayName | Eget namn för arbetsflödet. | Ja | 255 |
| Beskrivning | Beskrivning av arbetsflödet. | Nej | 255 |
| Initierare | Insamling av [ApplicationRoles](#application-roles). Roller som är tilldelade till användare som har behörighet att skapa kontrakt i arbetsflödet. | Ja | |
| StartState | Namnet på det ursprungliga tillståndet för arbetsflödet. | Ja | |
| Egenskaper | Insamling av [identifierare](#identifiers). Representerar data som kan läsas av kedjan eller visualiserade i en användare få verktyget. | Ja | |
| Konstruktor | Definierar indataparametrar för att skapa en instans av arbetsflödet. | Ja | |
| Functions | En samling [functions](#functions) som kan köras i arbetsflödet. | Ja | |
| Tillstånd | En samling av arbetsflödet [tillstånd](#states). | Ja | |

Ett exempel finns i [exempel på en konfigurationsfil](#configuration-file-example).

## <a name="type"></a>Typ

Datatyper som stöds.

| Typ | Beskrivning |
|-------|-------------|
| Adress  | Blockchain-adresstypen, till exempel *kontrakt* eller *användare*. |
| matris    | Enskild nivå matris av typen heltal, bool, pengar och tid. Matriser kan vara statisk eller dynamisk. Använd **ElementType** Ange datatyp för element i matrisen. Se [exempelkonfiguration](#example-configuration-of-type-array). |
| Bool     | Boolesk datatyp. |
| kontrakt | Adress av typen kontrakt. |
| Enum     | Numrerade uppsättning namngivna värden. När du använder uppräkningstypen kan ange du också en lista över EnumValues. Varje värde är begränsad till 255 tecken. Giltigt värde tecken omfattar övre och gemena bokstäver (A-Z, a – z) och siffror (0-9). Se [exempel på konfiguration och användning i Solidity](#example-configuration-of-type-enum). |
| int      | Datatypen Integer. |
| pengar    | Datatypen Money. |
| state    | Arbetsflödets tillstånd. |
| sträng  | Strängdatatyp. 4000 tecken maximalt. Se [exempelkonfiguration](#example-configuration-of-type-string). |
| Användare     | Adressen för typ av användare. |
| time     | Tid datatyp. |
|`[ Application Role Name ]`| Vilket namn som anges i programrollen. Begränsar användarna av den rolltypen. |

### <a name="example-configuration-of-type-array"></a>Exempel på konfiguration av typen matris

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Med hjälp av en egenskap av typen matris

Om du definierar en egenskap som typen matris i konfigurationen måste du inkludera en explicit get-funktion för att returnera den offentliga egenskapen av matristypen i Solidity. Exempel:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Exempel på konfiguration av typen sträng

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Exempel på konfiguration av typen enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Med uppräkningstypen i Solidity

När en uppräkning har definierats i konfigurationen, kan du använda Uppräkningstyper i Solidity. Du kan till exempel definiera en uppräkning som kallas PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Lista med strängar måste matcha mellan konfigurations- och smarta kontrakt är giltig och konsekvent deklarationer i Blockchain Workbench.

Exempel på tilldelning:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Exempel för funktionen parameter: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Definierar indataparametrar för en instans av ett arbetsflöde.

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Parametrar | Insamling av [identifierare](#identifiers) initiera ett smarta kontrakt. | Ja |

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

Definierar funktioner som kan köras i arbetsflödet.

| Fält | Beskrivning | Krävs | Maxlängd |
|-------|-------------|:--------:|-----------:|
| Namn | Det unika namnet på funktionen. Motsvarande smarta kontrakt måste använda samma **namn** för funktionen tillämpliga. | Ja | 50 |
| DisplayName | Eget namn för funktionen. | Ja | 255 |
| Beskrivning | Beskrivning av funktionen | Nej | 255 |
| Parametrar | Insamling av [identifierare](#identifiers) motsvarar parametrarna för funktionen. | Ja | |

### <a name="functions-example"></a>Functions-exempel

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

## <a name="states"></a>Tillstånd

En samling av unika tillstånd i ett arbetsflöde. Varje tillstånd fångar ett steg i den affärslogik Kontrollflöde. 

| Fält | Beskrivning | Krävs | Maxlängd |
|-------|-------------|:--------:|-----------:|
| Namn | Unikt namn för tillståndet. Motsvarande smarta kontrakt måste använda samma **namn** för tillämpligt tillstånd. | Ja | 50 |
| DisplayName | Eget namn för tillståndet. | Ja | 255 |
| Beskrivning | Beskrivning av tillstånd. | Nej | 255 |
| Värdet för procent färdigt | Ett heltalsvärde som visas i användargränssnittet för Blockchain Workbench för att visa förloppet i kontrollflödet för business logic. | Ja | |
| Stil | Visuella tips som anger om tillståndet representerar tillståndet har lyckats eller misslyckats. Det finns två giltiga värden: `Success` eller `Failure`. | Ja | |
| Övergångar | Samling av tillgängliga [övergångar](#transitions) från det aktuella tillståndet för nästa uppsättning tillstånd. | Nej | |

### <a name="states-example"></a>Tillstånd-exempel

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

Tillgängliga åtgärder till nästa steg. En eller flera roller kan utföra en åtgärd på varje tillstånd, där en åtgärd kan överföra ett tillstånd till ett annat tillstånd i arbetsflödet. 

| Fält | Beskrivning | Krävs |
|-------|-------------|:--------:|
| AllowedRoles | Lista över roller som program tillåts för att initiera övergången. Alla användare av den angivna rollen kanske att utföra åtgärden. | Nej |
| AllowedInstanceRoles | Lista med användarroller deltar eller anges i det smarta kontrakt som tillåts att initiera övergången. Instansroller definieras i **egenskaper** i arbetsflöden. AllowedInstanceRoles representerar en användare som deltar i en instans av ett smarta kontrakt. AllowedInstanceRoles ger dig möjlighet att begränsa att utföra en åtgärd till en användarroll i en kontrakt-instans.  Till exempel kanske du bara vill tillåta användaren som skapade kontraktet (InstanceOwner) för att kunna avsluta i stället för alla användare i rolltyp (ägare) om du har angett rollen i AllowedRoles. | Nej |
| DisplayName | Övergången eget visningsnamn. | Ja |
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

Programroller definierar en uppsättning roller som kan tilldelas till användare som vill fungera eller delta i programmet. Programroller kan användas för att begränsa åtgärder och delta i blockchain-program och motsvarande arbetsflöden. 

| Fält | Beskrivning | Krävs | Maxlängd |
|-------|-------------|:--------:|-----------:|
| Namn | Det unika namnet för programrollen. Motsvarande smarta kontrakt måste använda samma **namn** för tillämpliga rollen. Bastypen namn är reserverade. Du kan kalla en programroll med samma namn som [typ](#type)| Ja | 50 |
| Beskrivning | Beskrivning av programrollen. | Nej | 255 |

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

Identifierare som representerar en mängd information som används för att beskriva egenskaperna för arbetsflödet, konstruktor och funktionsparametrar. 

| Fält | Beskrivning | Krävs | Maxlängd |
|-------|-------------|:--------:|-----------:|
| Namn | Det unika namnet på egenskapen eller parametern. Motsvarande smarta kontrakt måste använda samma **namn** för egenskap eller parametern. | Ja | 50 |
| DisplayName | Eget visningsnamn för egenskap eller parametern. | Ja | 255 |
| Beskrivning | Beskrivning av den egenskapen eller parametern. | Nej | 255 |

### <a name="identifiers-example"></a>Exempel för identifierare

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

Överföringen tillgången är ett smarta kontrakt-scenario för att köpa och sälja resurser med högt värde, som kräver en inspector och appraiser. Säljare kan visa sina tillgångar genom att instansiera en tillgång överföring smarta kontrakt. Köpare kan göra erbjudanden genom att utföra en åtgärd på det smarta kontraktet och andra parter kan vidta åtgärder för att granska eller bedöma tillgången. När tillgången har märkts både granskas och ställer, köparen och säljaren bekräftar försäljningen igen innan kontraktet är inställt på Slutför. Alla deltagare ha insyn i tillståndet för kontraktet när den uppdateras vid varje punkt i processen. 

Mer information, inklusive kodfiler finns i [tillgången överföring exempel för Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Följande konfigurationsfil är för tillgången överföring exemplet:

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
> [REST API-referens för Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

