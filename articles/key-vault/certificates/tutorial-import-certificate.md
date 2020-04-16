---
title: Självstudiekurs - Importera ett certifikat i Key Vault med Azure portal | Microsoft-dokument
description: Självstudiekurs som visar hur du importerar ett certifikat i Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423112"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Självstudiekurs: Importera ett certifikat i Azure Key Vault

Azure Key Vault är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här självstudien skapar du ett nyckelvalv och använder det sedan för att importera ett certifikat. Mer information om nyckelvalv finns i [översikten](../general/overview.md).

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Importera ett certifikat i Nyckelvalv med hjälp av portalen.
> * Importera ett certifikat i Key vault med CLI.


Innan du börjar läser du [grundläggande begrepp för Nyckelvalv](../general/basic-concepts.md). 

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

![Utdata när nyckelvalvet har skapats](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importera ett certifikat till Key Vault

Om du vill importera ett certifikat till valvet måste du ha en PEM- eller PFX-certifikatfil för att finnas på disken. I det här fallet importerar vi ett certifikat med filnamnet **ExampleCertificate**.

> [!IMPORTANT]
> I Azure Key Vault är certifikatformat som stöds PFX och PEM. 
> - .pem-filformatet innehåller en eller flera X509-certifikatfiler.
> - PFX-filformat är ett arkivfilformat för lagring av flera kryptografiska objekt i en enda fil, dvs.  

1. På **egenskapssidorna**För Nyckelvalv väljer du Certifikat .
2. Klicka på **Generera/importera**.
3. Välj följande värden på skärmen **Skapa ett certifikat:**
    - **Metod för att skapa certifikat**: Importera.
    - **Certifikatnamn**: Exempelcertifiera.
    - **Ladda upp certifikatfil:** välj certifikatfilen från disk
    - Lämna standardvärdena för de andra alternativen. Klicka på **Skapa**.

![Egenskaper för certifikat](../media/certificates/tutorial-import-cert/cert-import.png)

När du har fått meddelandet om att certifikatet har importerats kan du klicka på det i listan. Du kan sedan se några av dess egenskaper. 

![Egenskaper för certifikat](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importera ett certifikat med Azure CLI

Importera ett certifikat till ett angivet nyckelvalv. Om du vill importera ett befintligt giltigt certifikat, som innehåller en privat nyckel, till Azure Key Vault kan filen som ska importeras vara i antingen PFX- eller PEM-format. Om certifikatet är i PEM-format måste PEM-filen innehålla såväl nyckel- och x509-certifikat som x509-certifikat. Den här åtgärden kräver certifikat-/importbehörighet.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```
Läs mer om parametrarna [här](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du ett Nyckelvalv och importerade ett certifikat i den. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs mer om [hantering av certifikat i Azure Key Vault](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Se exempel på [importera certifikat med REST-API:er](/rest/api/keyvault/importcertificate/importcertificate)
- Granska [metodtips för Azure Key Vault](../general/best-practices.md)