---
title: Azure Blockchain Workbench konfiguration metadata referens
description: Azure Blockchain Workbench Förhandsgranska metadata översikt för programkonfiguration.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 661e795f0e85f872b1072a8f641b8938115c5d7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252199"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Konfigurationsreferens för Azure Blockchain Workbench

Azure Blockchain Workbench-program är arbetsflöden för flera parter som definieras av konfigurationsmetadata och smart kontraktskod. Konfigurationsmetadata definierar avancerade arbetsflöden och interaktionsmodellen för blockkedjeprogrammet. Smarta kontrakt definierar affärslogiken för blockchain-programmet. Workbench använder konfiguration och smart kontraktskod för att generera användarupplevelser för blockchain-program.

Konfigurationsmetadata anger följande information för varje blockchain-program:

* Namn och beskrivning av blockchain-programmet
* Unika roller för användare som kan agera eller delta i blockchain-programmet
* Ett eller flera arbetsflöden. Varje arbetsflöde fungerar som en tillståndsdator för att styra flödet av affärslogiken. Arbetsflöden kan vara oberoende eller interagera med varandra.

Varje definierat arbetsflöde anger följande:

* Arbetsflödets namn och beskrivning
* lägena för arbetsflödet.  Varje tillstånd är ett steg i affärslogikens kontrollflöde. 
* Åtgärder för övergång till nästa tillstånd
* Användarroller som tillåts initiera varje åtgärd
* Smarta kontrakt som representerar affärslogik i kodfiler

## <a name="application"></a>Program

Ett blockchain-program innehåller konfigurationsmetadata, arbetsflöden och användarroller som kan agera eller delta i programmet.

| Field | Beskrivning | Krävs |
|-------|-------------|:--------:|
| ApplicationName | Unikt programnamn. Motsvarande smarta kontrakt måste använda samma **ApplicationName** för den tillämpliga kontraktsklassen.  | Ja |
| DisplayName | Programmets eget visningsnamn. | Ja |
| Beskrivning | Beskrivning av ansökan. | Inga |
| ApplicationRoles (ProgramRoles) | Insamling av [ApplicationRoles](#application-roles). Användarroller som kan agera eller delta i programmet.  | Ja |
| Arbetsflöden | Samling [av arbetsflöden](#workflows). Varje arbetsflöde fungerar som en tillståndsdator för att styra flödet av affärslogiken. | Ja |

Ett exempel finns i [exempel på konfigurationsfil](#configuration-file-example).

## <a name="workflows"></a>Arbetsflöden

Ett programs affärslogik kan modelleras som en tillståndsdator där en åtgärd gör att flödet av affärslogiken flyttas från ett tillstånd till ett annat. Ett arbetsflöde är en samling sådana tillstånd och åtgärder. Varje arbetsflöde består av ett eller flera smarta kontrakt som representerar affärslogiken i kodfiler. Ett körbart kontrakt är en instans av ett arbetsflöde.

| Field | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Namn | Unikt arbetsflödesnamn. Motsvarande smarta kontrakt måste använda samma **namn** för den tillämpliga kontraktsklassen. | Ja | 50 |
| DisplayName | Arbetsflödets eget visningsnamn. | Ja | 255 |
| Beskrivning | Beskrivning av arbetsflödet. | Inga | 255 |
| Initierare | Insamling av [ApplicationRoles](#application-roles). Roller som har tilldelats användare som har behörighet att skapa kontrakt i arbetsflödet. | Ja | |
| StartState (StartState) | Namn på arbetsflödets ursprungliga tillstånd. | Ja | |
| Egenskaper | Insamling av [identifierare](#identifiers). Representerar data som kan läsas utanför kedjan eller visualiseras i ett användarupplevelseverktyg. | Ja | |
| Konstruktor | Definierar indataparametrar för att skapa en instans av arbetsflödet. | Ja | |
| Funktioner | En samling [funktioner](#functions) som kan köras i arbetsflödet. | Ja | |
| Tillstånd | En samling [arbetsflödestillstånd](#states). | Ja | |

Ett exempel finns i [exempel på konfigurationsfil](#configuration-file-example).

## <a name="type"></a>Typ

Datatyper som stöds.

| Typ | Beskrivning |
|-------|-------------|
| adress  | Blockchain-adresstyp, till exempel *avtal* eller *användare*. |
| matris    | Matris på en nivå med typ heltal, bool, pengar eller tid. Matriser kan vara statiska eller dynamiska. Använd **ElementType** för att ange datatypen för elementen i matrisen. Se [exempelkonfiguration](#example-configuration-of-type-array). |
| bool     | Boolesk datatyp. |
| Kontrakt | Adress för typkontrakt. |
| Enum     | Uppräknad uppsättning namngivna värden. När du använder uppräkningstypen anger du också en lista över EnumValues. Varje värde är begränsat till 255 tecken. Giltiga värdetecken inkluderar versaler och gemener (A-Ö, a-z) och siffror (0-9). Se [exempelkonfiguration och användning i Soliditet](#example-configuration-of-type-enum). |
| int      | Datatyp för heltal. |
| money    | Datatyp för pengar. |
| state    | Arbetsflödestillstånd. |
| sträng  | Strängdatatyp. Max 4000 tecken. Se [exempelkonfiguration](#example-configuration-of-type-string). |
| användare     | Adress av typen användare. |
| time     | Tidsdatatyp. |
|`[ Application Role Name ]`| Alla namn som anges i programrollen. Begränsar användarna till den rolltypen. |

### <a name="example-configuration-of-type-array"></a>Exempel på konfiguration av textmatris

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

#### <a name="using-a-property-of-type-array"></a>Använda en egenskap för textmatris

Om du definierar en egenskap som typmatris i konfigurationen måste du inkludera en explicit get-funktion för att returnera den offentliga egenskapen för matristypen i Soliditet. Ett exempel:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Exempel på konfiguration av typsträng

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

### <a name="example-configuration-of-type-enum"></a>Exempel på konfiguration av typuppräkning

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

#### <a name="using-enumeration-type-in-solidity"></a>Använda uppräkningstyp i Soliditet

När en uppräkning har definierats i konfigurationen kan du använda uppräkningstyper i Soliditet. Du kan till exempel definiera en uppräkning som heter PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Listan över strängar måste matcha mellan konfigurationen och smart kontrakt för att vara giltiga och konsekventa deklarationer i Blockchain Workbench.

Exempel på tilldelning:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Exempel på funktionsparameter: 

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

| Field | Beskrivning | Krävs |
|-------|-------------|:--------:|
| Parametrar | Insamling av [identifierare](#identifiers) som krävs för att initiera ett smart kontrakt. | Ja |

### <a name="constructor-example"></a>Exempel på konstruktor

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

## <a name="functions"></a>Funktioner

Definierar funktioner som kan köras i arbetsflödet.

| Field | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Namn | Det unika namnet på funktionen. Motsvarande smarta kontrakt måste använda samma **namn** för den aktuella funktionen. | Ja | 50 |
| DisplayName | Funktionens eget visningsnamn. | Ja | 255 |
| Beskrivning | Beskrivning av funktionen | Inga | 255 |
| Parametrar | Insamling av [identifierare](#identifiers) som motsvarar parametrarna för funktionen. | Ja | |

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

## <a name="states"></a>Tillstånd

En samling unika tillstånd i ett arbetsflöde. Varje tillstånd fångar ett steg i affärslogikens kontrollflöde. 

| Field | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Namn | Unikt namn på staten. Motsvarande smarta kontrakt måste använda samma **namn** för det aktuella tillståndet. | Ja | 50 |
| DisplayName | Eget visningsnamn för tillståndet. | Ja | 255 |
| Beskrivning | Beskrivning av tillståndet. | Inga | 255 |
| ProcentKomstförsörjd | Ett heltalsvärde som visas i användargränssnittet för Blockchain Workbench för att visa förloppet inom affärslogikens kontrollflöde. | Ja | |
| Format | Visuell ledtråd som anger om tillståndet representerar ett lyckat tillstånd eller feltillstånd. Det finns två `Success` giltiga `Failure`värden: eller . | Ja | |
| Övergångar | Samling av tillgängliga [övergångar](#transitions) från det aktuella tillståndet till nästa uppsättning lägen. | Inga | |

### <a name="states-example"></a>Exempel på stater

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
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Övergångar

Tillgängliga åtgärder till nästa tillstånd. En eller flera användarroller kan utföra en åtgärd i varje tillstånd, där en åtgärd kan överföra ett tillstånd till ett annat tillstånd i arbetsflödet. 

| Field | Beskrivning | Krävs |
|-------|-------------|:--------:|
| AllowedRoles | Lista över programroller som kan initiera övergången. Alla användare av den angivna rollen kanske kan utföra åtgärden. | Inga |
| AllowedInstanceRoles | Lista över användarroller som deltar eller anges i det smarta kontraktet som tillåts initiera övergången. Förekomstroller definieras i **Egenskaper** i arbetsflöden. AllowedInstanceRoles representerar en användare som deltar i en instans av ett smart kontrakt. AllowedInstanceRoles ger dig möjlighet att begränsa att vidta en åtgärd till en användarroll i en kontraktsinstans.  Du kanske till exempel bara vill tillåta att användaren som skapade kontraktet (InstanceOwner) kan avsluta i stället för alla användare i rolltyp (Ägare) om du har angett rollen i AllowedRoles. | Inga |
| DisplayName | Det egna visningsnamnet på övergången. | Ja |
| Beskrivning | Beskrivning av övergången. | Inga |
| Funktion | Namnet på funktionen för att initiera övergången. | Ja |
| NextStates (NextStates) | En samling potentiella nästa stater efter en lyckad övergång. | Ja |

### <a name="transitions-example"></a>Exempel på övergångar

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

Programroller definierar en uppsättning roller som kan tilldelas användare som vill agera eller delta i programmet. Programroller kan användas för att begränsa åtgärder och deltagande i blockchain-programmet och motsvarande arbetsflöden. 

| Field | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Namn | Det unika namnet på programrollen. Motsvarande smarta kontrakt måste använda samma **namn** för den aktuella rollen. Bastypsnamn är reserverade. Du kan inte namnge en programroll med samma namn som [Typ](#type)| Ja | 50 |
| Beskrivning | Beskrivning av programrollen. | Inga | 255 |

### <a name="application-roles-example"></a>Exempel på programroller

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

Identifierare representerar en samling information som används för att beskriva arbetsflödesegenskaper, konstruktorer och funktionsparametrar. 

| Field | Beskrivning | Krävs | Högsta längd |
|-------|-------------|:--------:|-----------:|
| Namn | Det unika namnet på egenskapen eller parametern. Motsvarande smarta kontrakt måste använda samma **namn** för den aktuella egenskapen eller parametern. | Ja | 50 |
| DisplayName | Eget visningsnamn för egenskapen eller parametern. | Ja | 255 |
| Beskrivning | Beskrivning av egenskapen eller parametern. | Inga | 255 |
| Typ | [Egenskapsdatatyp](#type). | Ja |

### <a name="identifiers-example"></a>Exempel på identifierare

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

## <a name="configuration-file-example"></a>Exempel på konfigurationsfil

Överföring av tillgångar är ett smart kontraktsscenario för att köpa och sälja tillgångar med högt värde, vilket kräver en inspektör och värderingsman. Säljare kan lista sina tillgångar genom att instansiera ett smart kontrakt för överföring av tillgångar. Köpare kan lämna anbud genom att vidta åtgärder på smart kontrakt, och andra parter kan vidta åtgärder för att inspektera eller bedöma tillgången. När tillgången har markerats både inspekteras och utvärderas, kommer köparen och säljaren att bekräfta försäljningen igen innan kontraktet är satt att slutföras. Vid varje punkt i processen har alla deltagare insyn i tillståndet för kontraktet när det uppdateras. 

Mer information, inklusive kodfilerna, finns i [exempel på överföring av tillgångar för Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Följande konfigurationsfil är för exemplet på överföring av tillgångar:

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
          "Description": "Asset transfer has been canceled",
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

