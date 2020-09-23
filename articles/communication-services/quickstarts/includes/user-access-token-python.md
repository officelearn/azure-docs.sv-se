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
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948157"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 eller senare.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

1. Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Använd en text redigerare för att skapa en fil med namnet **Issue-tokens.py** i projektets rot Katalog och lägga till strukturen för programmet, inklusive grundläggande undantags hantering. Du kommer att lägga till alla käll koder för den här snabb starten till den här filen i följande avsnitt.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Communication Services administrations klient bibliotek för python-paket med hjälp av `pip install` kommandot.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `CommunicationIdentityClient` med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till den här koden inuti `try` blocket:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Skapa en användare

Azure Communication Services upprätthåller en Lightweight Identity-katalog. Använd `create_user` metoden för att skapa en ny post i katalogen med en unik `Id` . Du bör ha en mappning mellan appens användare och kommunikations tjänster genererade identiteter (t. ex. genom att lagra dem i din program servers databas).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Utfärda token för användar åtkomst

Använd `issue_token` metoden för att utfärda en åtkomsttoken för en kommunikations tjänst användare. Om du inte anger den valfria `user` parametern kommer en ny användare att skapas och returneras med token.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Token för användar åtkomst är korta autentiseringsuppgifter som måste återutfärdas för att förhindra att användarna upplever avbrott i tjänsten. `expires_on`Egenskapen svar anger livs längden för token.

## <a name="revoke-user-access-tokens"></a>Återkalla token för användar åtkomst

I vissa fall kan du behöva återkalla användar åtkomst-token, till exempel när en användare ändrar lösen ordet som de använder för att autentisera till din tjänst. Den här funktionen är tillgänglig via Azure Communication Services administrations klient bibliotek.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Ta bort en användare

Om du tar bort en identitet återkallar du alla aktiva tokens och förhindrar att du utfärdar efterföljande token för identiteterna. Det tar också bort allt beständigt innehåll som är associerat med användaren.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Kör koden

Från en konsol tolk navigerar du till katalogen som innehåller filen *Issue-token.py* och kör sedan följande `python` kommando för att köra appen.

```console
python ./issue-token.py
```
