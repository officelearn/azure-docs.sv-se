---
title: Skapa en blockchain program i Azure Blockchain arbetsstationen
description: Hur du skapar ett blockchain program i Azure Blockchain arbetsstationen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e4e2866e18c508c0df7a9a9e85a22c98e4372661
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Skapa en blockchain program i Azure Blockchain arbetsstationen

Du kan använda Azure Blockchain arbetsstationen för att skapa blockchain program som representerar flerparti arbetsflöden som definieras av konfiguration och kontraktkod som smart.

Lär dig att:

> [!div class="checklist"]
> * Konfigurera ett blockchain-program
> * Skapa en fil för smart kontrakt-kod
> * Lägga till en blockchain program till Blockchain arbetsstationen
> * Lägga till medlemmar i blockchain-programmet

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain arbetsstationen-distribution. Mer information finns i [Azure Blockchain arbetsstationen distribution](blockchain-workbench-deploy.md) mer information om distribution.
* Azure Active Directory-användare i innehavaren som associeras med Blockchain arbetsstationen. Mer information finns i [lägger du till Azure AD-användare i Azure Blockchain arbetsstationen](blockchain-workbench-manage-users.md#add-azure-ad-users).
* Ett administratörskonto Blockchain arbetsstationen. Mer information finns i lägga till [Blockchain arbetsstationen administratörer i Azure Blockchain arbetsstationen](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hej Blockchain

Vi ska skapa ett grundläggande program där en begärande skickar en begäran och skicka ett svar på begäran för en Övervakare. En begäran kan till exempel vara, ”Hello, hur är du”?, och svaret kan vara, ”jag är bra”!. Både begäran och svar registreras på den underliggande blockchain. 

Följ stegen för att skapa programfilerna eller så kan du [hämta exemplet från GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Konfigurationsfilen

Konfigurationsmetadata definierar övergripande arbetsflöden och interaktion modell blockchain-programmet. Konfigurationsmetadata representerar etapper för arbetsflöde och interaktion modell för blockchain-programmet.

1. Skapa en fil med namnet i redigeringsprogrammet favorit `HelloBlockchain.json`.
2. Lägg till följande JSON för att definiera konfigurationen av blockchain-programmet.

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
          "Name": "HelloBlockchain",
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

3. Spara filen `HelloBlockchain.json`.

Konfigurationsfilen har flera avsnitt. Information om varje avsnitt är följande:

### <a name="application-metadata"></a>Programmetadata

I början av konfigurationsfilen innehåller information om programmet, inklusive namn och beskrivning.

### <a name="application-roles"></a>Programroller

Avsnittet för programmets roller definierar de användarroller som kan fungera eller delta i programmet blockchain. Du kan definiera en uppsättning distinkta roller som baseras på funktionen. I begäran och svar-scenariot finns en åtskillnad mellan funktionerna i en begärande som en entitet som ger begäranden och en Övervakare som en entitet som ger svar.

### <a name="workflows"></a>Arbetsflöden

En eller flera faser och åtgärder för kontraktet definierar arbetsflöden. I begäran och svar-scenario är det första steget (tillstånd) i arbetsflödet en begärande (roll) utför en åtgärd (övergång) att skicka en begäran (funktion). Nästa steg (status) är en Övervakare (roll) utför en åtgärd (övergång) att skicka ett svar (funktion). Arbetsflöde för ett program kan omfatta egenskaper, funktioner, och tillstånd krävs beskriver flödet av ett kontrakt. 

Mer information om innehållet konfigurationsfiler finns [adresskonfigurationen som refereras till Azure Blockchain arbetsflöde](blockchain-workbench-configuration-overview.md).

## <a name="smart-contract-code-file"></a>Kodfilen för smart kontrakt

Smart kontrakt representerar affärslogik blockchain programmet. För närvarande stöder Blockchain arbetsstationen Ethereum för blockchain redovisning. Ethereum använder [täckningsgrad](https://solidity.readthedocs.io) som dess programmeringsspråk som används för att skriva egen att framtvinga affärslogik för smart kontrakt.

Smart kontrakt i täckningsgrad liknar klasser i objektorienterad språk. Varje kontrakt innehåller tillstånd och funktioner för att implementera steg och åtgärder för smart kontraktet.

Skapa en fil med namnet i redigeringsprogrammet favorit `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Version pragma

Som bästa praxis, anger du versionen av täckningsgrad som du utvecklar. Anger versionen hjälper till att undvika kompatibilitetsproblem med framtida täckningsgrad versioner. 

Lägg till följande version pragma överst i `HelloBlockchain.sol` smart kontraktet kodfilen.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Basklass

**WorkbenchBase** basklass aktiverar Blockchain arbetsstationen för att skapa en uppdatering kontraktet. Basklassen som krävs för Blockchain arbetsstationen specifika smart kontraktkod. Ditt kontrakt måste ärva från den **WorkbenchBase** basklassen.

I `HelloBlockchain.sol` kontraktet kodfilen för smartkort, lägga till den **WorkbenchBase** klass i början av filen. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
Basklassen innehåller två viktiga funktioner:

|Basklass funktion  | Syfte  | När anropet  |
|---------|---------|---------|
| ContractCreated() | Meddelar Blockchain arbetsstationen ett kontrakt har skapats | Innan du avslutar kontrakt-konstruktorn |
| ContractUpdated() | Meddelar Blockchain arbetsstationen tillståndet kontraktet har uppdaterats | Innan du avslutar en kontrakt-funktion |

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfiguration och smarta kontraktet kod relation

Blockchain arbetsstationen använder konfigurationsfilen och smarta kontraktet kodfilen för att skapa ett blockchain program. Det finns en relation mellan vad som har definierats i konfigurationen och koden i smart kontraktet. Kontraktsdetaljer, funktioner, parametrar och typer krävs för att skapa programmet. Blockchain arbetsstationen kontrollerar filerna innan programmet skapas. 

### <a name="contract"></a>Kontrakt

För Blockchain arbetsstationen kontrakt måste ärva från den **WorkbenchBase** basklassen. När deklarerar kontrakt, måste du skicka programmets namn och namnet på arbetsflödet som argument.

Lägg till den **kontraktet** sidhuvud till din `HelloBlockchain.sol` smart kontraktet kodfilen. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Ditt kontrakt måste ärva från den **WorkbenchBase** basklassen och ange parametrarna **ApplicationName** och arbetsflödet **namn** som definierats i konfigurationen filen. I det här fallet är programnamn och Arbetsflödesnamn samma.

### <a name="state-variables"></a>Tillstånd variabler

Variabler för systemtillstånd lagra värdena för tillståndet för varje förekomst av kontrakt. Tillstånd variabler i ditt kontrakt måste matcha arbetsflödesegenskaper som definierats i konfigurationsfilen.

Lägg till variablerna tillstånd i ditt kontrakt i din `HelloBlockchain.sol` smart kontraktet kodfilen. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Konstruktorn

Konstruktorn definierar indataparametrar för en ny smart kontrakt-instans av ett arbetsflöde. Konstruktorn har deklarerats som en funktion med samma namn som kontraktet. Obligatoriska parametrar för konstruktorn definieras som konstruktorparametrarna i konfigurationsfilen. Antal, ordning och typ av parametrar måste matcha i båda filerna.

Skriva all affärslogik i konstruktorfunktionen du vill utföra innan du skapar kontraktet. Till exempel initiera variablerna tillstånd med start av värden.

Innan du avslutar konstruktorfunktionen anropa den `ContractCreated()` funktion. Den här funktionen meddelar Blockchain arbetsstationen ett kontrakt har skapats.

Lägga till konstruktorfunktionen i ditt kontrakt i din `HelloBlockchain.sol` smart kontraktet kodfilen. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Functions

Funktioner är körbara enheter för affärslogik i ett kontrakt. Obligatoriska parametrar för funktionen definieras som funktionsparametrar i konfigurationsfilen. Antal, ordning och typ av parametrar måste matcha i båda filerna. Funktioner är kopplad till övergångar i ett arbetsflöde Blockchain arbetsstationen i konfigurationsfilen. En övergång är en åtgärd som utförs för att flytta till nästa steg i ett program arbetsflödet enligt kontraktet.

Skriva all affärslogik som du vill utföra i funktionen. Till exempel ändrar tillstånd variabelns värde.

Innan du avslutar funktionen anropa den `ContractUpdated()` funktion. Funktionen meddelar Blockchain arbetsstationen kontraktet tillstånd har uppdaterats. Om du vill ångra ändringarna i funktionen tillstånd anropa revert(). Återställa rensningar tillstånd ändringar som gjorts sedan det senaste anropet till ContractUpdated().

1. Lägg till följande funktioner i ditt kontrakt i din `HelloBlockchain.sol` smart kontraktet kodfilen. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Spara dina `HelloBlockchain.sol` smart kontraktet kodfilen.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Lägga till blockchain program till Blockchain arbetsstationen

Om du vill lägga till ett blockchain program till Blockchain arbetsstationen överför konfiguration och smarta kontraktsfiler att definiera programmet.

1. I en webbläsare, navigerar du till webbadressen Blockchain arbetsstationen. Till exempel `https://{workbench URL}.azurewebsites.net/` webbprogrammet skapas när du distribuerar Blockchain arbetsstationen. Information om hur du hittar din Blockchain arbetsstationen webbadress finns [Blockchain arbetsstationen Webbadress](blockchain-workbench-deploy.md#blockchain-workbench-web-url)
2. Logga in som administratör Blockchain arbetsstationen. Läs mer om hur du hanterar användare, [hantera användare i Azure Blockchain arbetsstationen](blockchain-workbench-manage-users.md).
3. Välj **program** > **nya**. Den **nytt program** visas.
4. Välj **överför kontraktet konfigurationen** > **Bläddra** att hitta den **HelloBlockchain.json** konfigurationsfil som du skapade. Konfigurationsfilen verifieras automatiskt. Välj den **visa** länken ska visa valideringsfel. Åtgärda verifieringsfel innan du distribuerar programmet.
5. Välj **överför kontraktet koden** > **Bläddra** att hitta den **HelloBlockchain.sol** smart kontraktet kodfilen. Kodfilen verifieras automatiskt. Välj den **visa** länken ska visa valideringsfel. Åtgärda verifieringsfel innan du distribuerar programmet.
6. Välj **distribuera** skapa blockchain programmet baserat på konfigurationen och smarta kontraktsfiler.

Distributionen av programmet blockchain tar några minuter. När distributionen är klar visas det nya programmet visas i **program**. 

> [!NOTE]
> Du kan också skapa blockchain program med hjälp av den [Azure Blockchain arbetsstationen REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Lägga till medlemmar i blockchain program

Lägg till program medlemmar i programmet för att initiera och vidta åtgärder i kontrakt. Om du vill lägga till medlemmar i programmet, du måste vara en [Blockchain arbetsstationen administratören](blockchain-workbench-manage-users.md#manage-blockchain-workbench-administrators).

1. Välj **program** > **Hello, Blockchain!**.
2. Antalet medlemmar som hör till programmet visas i det övre högra hörnet på sidan. Antalet medlemmar kommer att noll för ett nytt program.
3. Välj den **medlemmar** länk i det övre högra hörnet på sidan. En lista över medlemmar för programmet visas.
4. Välj i medlemskapslistan **lägga till medlemmar**.
5. Välj eller ange medlemsnamn du vill lägga till. Endast visas Azure AD-användare som finns i Blockchain arbetsstationen klienten. Om användaren inte hittas, behöver du [lägga till Azure AD-användare](blockchain-workbench-manage-users.md#add-azure-ad-users).
6. Välj den **rollen** för medlemmen. Den första medlemmen Välj **begärande** som rollen.
7. Välj **Lägg till** att lägga till medlem med rollen kopplade till programmet.
8. Lägga till en annan medlem för programmet med den **svarare** roll.

Mer information om hur du hanterar användare i Blockchain arbetsstationen finns [hantera användare i Azure Blockchain arbetsstationen](blockchain-workbench-manage-users.md)

## <a name="next-steps"></a>Nästa steg

Du har skapat en grundläggande förfrågan och svar-program i den här artikeln. Om du vill lära dig mer om att använda programmet, fortsätta till nästa anvisningar artikeln.

> [!div class="nextstepaction"]
> [Med hjälp av en blockchain](blockchain-workbench-use.md)
