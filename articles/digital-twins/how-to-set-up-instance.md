---
title: Skapa en Azure Digital Twins-instans
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fecacbd2c7c6549a1321367157bb179321779ca9
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206511"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurera en digital Azure-instans

Den här artikeln vägleder dig genom de grundläggande stegen för att skapa en ny Azure Digital-instansen. Detta innefattar att skapa instansen och tilldela [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) behörigheter till instansen själv.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurera en digital Azure-instans

Därefter skapar du en ny Azure-resurs grupp som kan användas i den här instruktionen. Sedan kan du **skapa en ny instans av Azure Digitals dubbla** i den resurs gruppen. 

Du måste också ange ett namn för din instans och välja en region för distributionen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [Azure-produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> Namnet på den nya instansen måste vara unikt inom regionen (vilket innebär att om en annan Azure Digital-instans i regionen redan använder det namn du väljer, måste du välja ett annat namn.

Skapa resurs gruppen och instansen med följande kommandon:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

Resultatet av dessa kommandon ser ut ungefär så här och placerar information om de resurser som du har skapat:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Fönstret Kommando med att skapa en resurs grupp och en Azure Digital-instans":::

Observera att Azure Digitals dubbla instansen *värdnamn*, *namn*och *resourceGroup* från utdata. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen för att konfigurera autentisering och relaterade Azure-resurser.

> [!TIP]
> Du kan se dessa egenskaper, tillsammans med alla egenskaper för din instans, när som helst genom att köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Tilldela Azure Active Directory behörigheter

Azure Digitals flätar använder [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) för rollbaserad åtkomst kontroll (RBAC). Det innebär att innan du kan göra data planet anrop till din Azure Digital-instansen, måste du först tilldela dig en roll med dessa behörigheter.

För att kunna använda Azure Digitals dubbla med ett klient program måste du också se till att din klient app kan autentisera mot Azure Digitals. Det gör du genom att konfigurera en Azure Active Directory-app (AAD)-registrering, som du kan läsa om i [How-to: autentisera ett klient program](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Tilldela dig själv en roll

Skapa en roll tilldelning för dig själv i Azure Digitals-instansen med ditt e-postmeddelande som är kopplat till AAD-klienten i din Azure-prenumeration. 

För att kunna göra detta måste du klassificeras som en ägare i din Azure-prenumeration. Du kan kontrol lera detta genom att köra `az role assignment list --assignee <your-Azure-email>` kommandot och kontrol lera i resultatet att *roleDefinitionName* -värdet är *ägare*. Om du upptäcker att värdet är *deltagare* eller något annat än *ägare*, kontaktar du din prenumerations administratör och ger dig möjlighet att bevilja behörigheter i din prenumeration. De kan antingen höja rollen i hela prenumerationen så att du kan köra följande kommando, eller så kan en ägare köra följande kommando för din räkning för att konfigurera dina Azure Digitals dubbla behörigheter åt dig.

Använd följande kommando för att tilldela behörighet för användaren "ägare" i din Azure Digital-instansen (måste köras av en ägare av Azure-prenumerationen):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Resultatet av det här kommandot är information om den roll tilldelning som har skapats.

> [!TIP]
> Om du får ett *400: BadRequest* -fel i stället kör du följande kommando för att hämta *ObjectID* för din användare:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Upprepa sedan kommandot roll tilldelning med användarens *objekt-ID* i stället för din e-post.

Nu har du en Azure Digital-instansen som är redo att gå och behörighet att hantera den.

## <a name="next-steps"></a>Nästa steg

Se Konfigurera och autentisera en klient app för att arbeta med din instans:
* [Anvisningar: autentisera ett klient program](how-to-authenticate-client.md)
