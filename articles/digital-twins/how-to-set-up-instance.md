---
title: Skapa en Azure Digital Twins-instans
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: bbd7c74fc87b3f7af799637e2288dcd074266843
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725621"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurera en digital Azure-instans

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Den här artikeln vägleder dig genom de grundläggande stegen för att skapa en ny Azure Digital-instansen. Detta innefattar att skapa instansen och tilldela [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) behörigheter till instansen själv.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurera en digital Azure-instans

Kör sedan följande kommandon för att skapa en ny Azure-resurs grupp som kan användas i den här instruktionen och sedan skapa en ny instans av Azure Digital-dubbla i den här resurs gruppen.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

> [!TIP]
> Om du vill skapa en lista med namn på Azure-regioner som kan skickas till kommandon i Azure CLI kör du följande kommando:
> ```azurecli
> az account list-locations -o table
> ```
> Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Resultatet av dessa kommandon ser ut ungefär så här och placerar information om de resurser som du har skapat:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Fönstret Kommando med att skapa en resurs grupp och en Azure Digital-instans":::

Observera att Azure Digitals dubbla instansen *värdnamn*, *namn*och *resourceGroup* från utdata. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen för att konfigurera autentisering och relaterade Azure-resurser.

> [!TIP]
> Du kan se dessa egenskaper, tillsammans med alla egenskaper för din instans, när som helst genom att köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Tilldela Azure Active Directory behörigheter

Azure Digitals flätar använder [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) för rollbaserad åtkomst kontroll (RBAC). Det innebär att innan du kan göra data planet anrop till din Azure Digital-instansen, måste du först tilldela dig en roll med dessa behörigheter.

För att kunna använda Azure Digitals dubbla med ett klient program måste du också se till att din klient app kan autentisera mot Azure Digitals. Det gör du genom att konfigurera en Azure Active Directory-app (AAD)-registrering, som du kan läsa om i [How-to: autentisera ett klient program](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Tilldela dig själv en roll

Skapa en roll tilldelning själv med hjälp av din e-postadress som är kopplad till AAD-klienten i din Azure-prenumeration. Kontrol lera först att du är klassificerad som ägare i din Azure-prenumeration. Sedan kan du använda följande kommando för att tilldela användaren en ägar roll för din Azure Digital-instansen:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Resultatet av det här kommandot är information om den roll tilldelning som du har skapat.

> [!TIP]
> Om du får ett *400: BadRequest* -fel i stället kör du följande kommando för att hämta *ObjectID* för din användare:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Upprepa sedan kommandot roll tilldelning med användarens *objekt-ID* i stället för din e-post.

Nu har du en Azure Digital-instansen som är redo att sätta igång.

## <a name="next-steps"></a>Nästa steg

Se Konfigurera och autentisera en klient app för att arbeta med din instans:
* [Anvisningar: autentisera ett klient program](how-to-authenticate-client.md)
