---
title: Skapa ett blockchain-program i Azure Blockchain Workbench
description: Hur du skapar en blockchain-program i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a7ca3f42874bc844bc0036e37a790ffebdc5f8d8
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243488"
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Skapa ett blockchain-program i Azure Blockchain Workbench

Du kan använda Azure Blockchain Workbench för att skapa blockchain-program som representerar flerparti arbetsflöden som definieras av konfiguration och kontraktkod som smarta.

Lär dig att:

> [!div class="checklist"]
> * Konfigurera en blockchain-program
> * Skapa en kodfil smarta kontrakt
> * Lägga till en blockchain-program i Blockchain Workbench
> * Lägga till medlemmar i blockchain-program

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain Workbench-distribution. Mer information finns i [Azure Blockchain Workbench distribution](deploy.md) mer information om distribution.
* Azure Active Directory-användare i klienten som är associerade med Blockchain Workbench. Mer information finns i [lägga till Azure AD-användare i Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* En Blockchain Workbench-administratörskonto. Mer information finns i lägga till [Blockchain Workbench administratörer i Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hello, Blockchain!

Nu ska vi skapa ett program med grundläggande där en begärande skickar en begäran och skicka ett svar på begäran för en Övervakare. Till exempel en ansökan kan ha ”Hello, hur är du”?, och svaret kan vara, ”jag är bra”!. Både begäran och svaret registreras på den underliggande blockchain. 

Följ stegen för att skapa programfilerna eller så kan du [hämta exemplet från GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Konfigurationsfil

Konfigurationsmetadata definierar avancerade arbetsflöden och interaktion modell blockchain-program. Konfigurationsmetadata representerar arbetsflödessteg och interaktion modell blockchain-program.

1. I redigeringsprogram du föredrar, skapar du en fil med namnet `HelloBlockchain.json`.
2. Lägg till följande JSON för att definiera konfigurationen av blockchain-program.

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

I början av konfigurationsfilen innehåller information om programmet namn och beskrivning.

### <a name="application-roles"></a>Programroller

Avsnittet program rollerna definierar de användarroller som kan fungera eller delta i blockchain-program. Du definierar en uppsättning med olika roller som baseras på funktionen. I scenariot begäranden och svar finns ingen skillnad på funktionerna i en begärande som en entitet som producerar begäranden och en Övervakare som en enhet som ger svar.

### <a name="workflows"></a>Arbetsflöden

En eller flera faser och åtgärder som kontraktet definierar arbetsflöden. I begäranden och svar-scenario är det första steget (tillstånd) i arbetsflödet en begärande (roll) vidtar en åtgärd (övergång) för att skicka en begäran (funktion). Nästa steg (tillstånd) är en Övervakare (roll) vidtar en åtgärd (övergång) för att skicka ett svar (funktion). Arbetsflöde för ett program kan omfatta egenskaper, funktioner, och tillstånd krävs Beskriv flödet av ett kontrakt. 

Mer information om innehållet för configuration-filer finns i [adresskonfigurationen som refereras till Azure Blockchain arbetsflöde](configuration.md).

## <a name="smart-contract-code-file"></a>Smarta kontrakt kodfilen

Smarta kontrakt representerar affärslogiken i blockchain-program. Blockchain Workbench stöder för närvarande, Ethereum för blockchain-redovisning. Ethereum använder [Solidity](https://solidity.readthedocs.io) som dess programmeringsspråk som används för att skriva egen framtvinga affärslogik för smarta kontrakt.

Smarta kontrakt i Solidity liknar klasser i objektorienterade språk. Varje avtalet innehåller tillstånd och funktioner för att implementera faser och åtgärder av smarta kontrakt.

I redigeringsprogram du föredrar, skapar du en fil med namnet `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Version pragma

Som bästa praxis, anger du versionen av Solidity som du vill skicka meddelanden. Ange versionen motverkar inkompatibiliteter med framtida Solidity versioner. 

Lägg till följande version pragma överst i `HelloBlockchain.sol` smarta kontrakt kodfilen.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Basklass

**WorkbenchBase** basklass kan Blockchain Workbench att skapa och uppdatera kontraktet. Basklassen krävs för Blockchain Workbench specifika smarta kontraktkod. Ditt avtal måste ärva från den **WorkbenchBase** basklassen.

I `HelloBlockchain.sol` smarta kontrakt kodfilen, lägga till den **WorkbenchBase** klass i början av filen. 

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
Basklassen omfattar två viktiga funktioner:

|Basklass funktion  | Syfte  | När anropet  |
|---------|---------|---------|
| ContractCreated() | Meddelar Blockchain Workbench ett kontrakt har skapats | Innan du avslutar konstruktorn kontrakt |
| ContractUpdated() | Meddelar Blockchain Workbench tillståndet kontrakt har uppdaterats | Innan du avslutar en kontrakt-funktion |

### <a name="configuration-and-smart-contract-code-relationship"></a>Konfiguration och smarta kontrakt kod relation

Blockchain Workbench använder konfigurationsfilen och smarta kontrakt fil med kod för att skapa ett blockchain-program. Det finns en relation mellan enligt definitionen i konfigurationen och koden i smarta kontrakt. Podrobnosti o kontraktu, funktioner, parametrar och typer krävs för att matcha för att skapa programmet. Blockchain Workbench kontrollerar filerna innan du skapa program. 

### <a name="contract"></a>kontrakt

För Blockchain Workbench kontrakt måste ärva från den **WorkbenchBase** basklassen. När du deklarerar kontraktet, måste du skicka namnet på programmet och Arbetsflödesnamnet som argument.

Lägg till den **kontrakt** rubriken till din `HelloBlockchain.sol` smarta kontrakt kodfilen. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

Ditt avtal måste ärva från den **WorkbenchBase** basklassen och skicka in parametrarna **ApplicationName** och arbetsflödet **namn** som definierats i konfigurationen filen. I det här fallet är programnamn och Arbetsflödesnamn samma.

### <a name="state-variables"></a>Statliga variabler

Variabler för systemtillstånd lagra värdena för tillståndet för varje kontrakt-instans. Tillstånd-variabler i ditt avtal måste matcha arbetsflödets egenskaperna som definierats i konfigurationsfilen.

Lägg till variablerna tillstånd i ditt avtal i din `HelloBlockchain.sol` smarta kontrakt kodfilen. 

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

### <a name="constructor"></a>Konstruktor

Konstruktorn definierar indataparametrar för en ny smarta kontrakt-instans av ett arbetsflöde. Konstruktorn deklareras som en funktion med samma namn som kontraktet. Obligatoriska parametrar för konstruktorn definieras som konstruktorn parametrar i konfigurationsfilen. Antal, ordning och typ av parametrar måste matcha i båda filerna.

Skriva all affärslogik i konstruktorfunktionen du vill utföra innan du skapar kontraktet. Till exempel initiera variablerna tillstånd med värden som börjar.

Innan du avslutar konstruktorfunktionen anropa den `ContractCreated()` funktion. Den här funktionen meddelar Blockchain Workbench ett kontrakt har skapats.

Lägga till konstruktorfunktionen i ditt avtal i din `HelloBlockchain.sol` smarta kontrakt kodfilen. 

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

Funktioner är körbara enheter för affärslogik i ett kontrakt. Obligatoriska parametrar för funktionen definieras som parametrarna i konfigurationsfilen. Antal, ordning och typ av parametrar måste matcha i båda filerna. Funktioner är kopplade till övergångar i ett arbetsflöde med Blockchain Workbench i konfigurationsfilen. En övergång är en åtgärd som utförs för att flytta till nästa steg i ett programs arbetsflöde systemets kontraktet.

Skriva all affärslogik som du vill utföra i funktionen. Till exempel ändra tillståndet variabelns värde.

Innan du avslutar funktionen anropa den `ContractUpdated()` funktion. Funktionen meddelar Blockchain Workbench kontrakt tillstånd har uppdaterats. Om du vill återställa tillståndsändringar som gjorts i funktionen kan du anropa revert(). Återställa rensningar tillstånd ändringar som gjorts sedan det senaste anropet till ContractUpdated().

1. Lägg till följande funktioner i ditt avtal i din `HelloBlockchain.sol` smarta kontrakt kodfilen. 

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

2. Spara din `HelloBlockchain.sol` smarta kontrakt kodfilen.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Lägga till blockchain-program i Blockchain Workbench

Om du vill lägga till en blockchain-program i Blockchain Workbench, överför du konfigurationen och smarta kontraktsfiler att definiera programmet.

1. I en webbläsare, navigerar du till webbadressen Blockchain Workbench. Till exempel `https://{workbench URL}.azurewebsites.net/` webbprogrammet skapas när du distribuerar Blockchain Workbench. Information om hur du hittar webbadressen för Blockchain Workbench finns i [Blockchain Workbench-Webbadress](deploy.md#blockchain-workbench-web-url)
2. Logga in som en [Blockchain Workbench administratör](manage-users.md#manage-blockchain-workbench-administrators).
3. Välj **program** > **nya**. Den **nytt program** egenskapsruta visas.
4. Välj **ladda upp konfigurationen av kontrakt** > **Bläddra** att hitta den **HelloBlockchain.json** konfigurationsfil som du skapade. Konfigurationsfilen valideras automatiskt. Välj den **visa** länken visas fel relaterade till verifieringen. Åtgärda verifieringsfel innan du distribuerar programmet.
5. Välj **överför kontrakt koden** > **Bläddra** att hitta den **HelloBlockchain.sol** smarta kontrakt kodfilen. Kodfilen valideras automatiskt. Välj den **visa** länken visas fel relaterade till verifieringen. Åtgärda verifieringsfel innan du distribuerar programmet.
6. Välj **distribuera** skapa blockchain-program baserat på konfiguration och smarta kontraktsfiler.

Distributionen av blockchain-programmet tar några minuter. När distributionen är klar kan det nya programmet visas i **program**. 

> [!NOTE]
> Du kan också skapa blockchain-program med hjälp av den [Azure Blockchain Workbench REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Lägga till medlemmar i blockchain-program

Lägga till programmet medlemmar i ditt program för att initiera och utföra åtgärder på kontrakt. Om du vill lägga till medlemmar i programmet, måste du vara en [Blockchain Workbench administratör](manage-users.md#manage-blockchain-workbench-administrators).

1. Välj **program** > **Hello, Blockchain!**.
2. Antalet medlemmar som är kopplad till programmet visas i det övre högra hörnet på sidan. Antalet medlemmar är för ett nytt program, noll.
3. Välj den **medlemmar** länken i det övre högra hörnet på sidan. En aktuell lista över medlemmar för programmet visas.
4. Välj i medlemskapslistan **lägga till medlemmar**.
5. Välj eller ange medlemmens namn som du vill lägga till. Endast visas Azure AD-användare som finns i Blockchain Workbench-klient. Om användaren inte hittas, måste du [lägga till Azure AD-användare](manage-users.md#add-azure-ad-users).
6. Välj den **rollen** för medlemmen. För den första medlemmen väljer **begärande** som rollen.
7. Välj **Lägg till** att lägga till medlemmen med tillhörande rolltjänster i programmet.
8. Lägg till en annan medlem i programmet med den **svarare** roll.

Läs mer om hur du hanterar användare i Blockchain Workbench [hantera användare i Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett program med grundläggande begäranden och svar. Om du vill lära dig mer om att använda programmet, fortsätta till nästa artikel om anvisningar.

> [!div class="nextstepaction"]
> [Med hjälp av en blockchain-program](use.md)
