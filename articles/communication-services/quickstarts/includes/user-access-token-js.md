---
title: inkludera fil
description: inkludera fil
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "90948163"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services administrations klient bibliotek för Java Script.

```console

npm install @azure/communication-administration --save

```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

## <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna en ny textfil i kod redigeraren
1. Lägg till ett `require` anrop för att läsa in `CommunicationIdentityClient`
1. Skapa strukturen för programmet, inklusive grundläggande undantags hantering

Använd följande kod för att börja:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Spara den nya filen som **issue-token.js** i katalogen *User-tokens-snabb start* .

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `CommunicationIdentityClient` med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till följande kod i `main`-metoden:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Skapa en användare

Azure Communication Services upprätthåller en Lightweight Identity-katalog. Använd `createUser` metoden för att skapa en ny post i katalogen med en unik `Id` . Du bör ha en mappning mellan appens användare och kommunikations tjänster genererade identiteter (t. ex. genom att lagra dem i din program servers databas).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Utfärda token för användar åtkomst

Använd `issueToken` metoden för att utfärda en åtkomsttoken för en kommunikations tjänst användare. Om du inte anger den valfria `user` parametern kommer en ny användare att skapas och returneras med token.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Token för användar åtkomst är korta autentiseringsuppgifter som måste återutfärdas för att förhindra att användarna upplever avbrott i tjänsten. `expiresOn`Egenskapen svar anger livs längden för token.

## <a name="revoke-user-access-tokens"></a>Återkalla token för användar åtkomst

I vissa fall kan du behöva återkalla användar åtkomst-token, till exempel när en användare ändrar lösen ordet som de använder för att autentisera till din tjänst. Detta använder `revokeTokens` metoden för att ogiltig förklara alla användares åtkomsttoken.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>Ta bort en användare

Borttagning av en användare återkallar alla aktiva tokens och förhindrar att du utfärdar efterföljande token för identiteterna. Det tar också bort allt beständigt innehåll som är associerat med användaren.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Kör koden

Gå till den katalog som innehåller *issue-token.js* -filen från en konsol tolk och kör sedan följande `node` kommando för att köra appen.

```console
node ./issue-token.js
```
