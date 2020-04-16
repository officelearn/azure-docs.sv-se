---
title: Azure Quickstart - Ange och hämta en nyckel från Key Vault med Azure portal | Microsoft-dokument
description: Snabbstart som visar hur du anger och hämtar en nyckel från Azure Key Vault med Azure-portalen
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 41f3d60d91b7418d6e9733b8351d4830b31dbace
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424197"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Snabbstart: Ange och hämta en nyckel från Azure Key Vault med Azure-portalen

Azure Key Vault är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten skapar du ett nyckelvalv och använder det sedan för att lagra en nyckel. Mer information om nyckelvalv finns i [översikten](../general/overview.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

1. På Azure-portalmenyn eller på **startsidan** väljer du **Skapa en resurs**.
2. Skriv **Key Vault** i sökrutan.
3. Välj **Key Vault** i listan med resultat.
4. Välj **Skapa** i avsnittet Key Vault.
5. Ange följande information i avsnittet **Skapa nyckelvalv** avsnittet Ange följande information:
    - **Namn**: Ett unikt namn krävs. För den här snabbstarten använder vi **Example-Vault**. 
    - **Prenumeration**: Välj en prenumeration.
    - Under **Resursgrupp**väljer du **Skapa nytt** och anger ett resursgruppnamn.
    - Välj en plats i listrutan **Plats**.
    - Lämna standardvärdena för de andra alternativen.
6. När du har angett den här informationen väljer du **Skapa**.

Anteckna de två egenskaperna som visas nedan:

* **Vault Namn:** I exemplet är detta **Exempel-Vault**. Du kommer att använda det här namnet i senare steg.
* **Valvets URI**: I det här exemplet är det https://example-vault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

![Utdata när nyckelvalvet har skapats](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Lägga till en nyckel i Key Vault

Om du vill lägga till en nyckel i valvet behöver du bara vidta några ytterligare steg. I det här fallet lägger vi till en nyckel som kan användas av ett program. Nyckeln kallas **ExampleKey**.

1. På egenskapssidorna För Nyckelvalv väljer du **Nycklar**.
2. Klicka på **Generera/importera**.
3. Välj följande värden på skärmen **Skapa en nyckel:**
    - **Alternativ**: Generera.
    - **Namn**: ExampleKey.
    - Lämna standardvärdena för de andra alternativen. Klicka på **Skapa**.

När du får meddelandet om att nyckeln har skapats kan du klicka på den i listan. Du ser då vissa av egenskaperna. Om du klickar på den aktuella versionen så kan du se värdet du angav i föregående steg.

![Nyckelegenskaper](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Nyckelvalv och sparade en nyckel i det. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se [utvecklarguiden för Azure Key Vault](../general/developers-guide.md)
- Granska [metodtips för Azure Key Vault](../general/best-practices.md)