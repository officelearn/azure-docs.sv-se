---
title: Konfigurera en instans och autentisering (manuell)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla nätverk, inklusive rätt autentisering. Manuell version.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2d5ce0bc988badc6f25726206a953d87de7eaa2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371471"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Konfigurera en digital Azure-instans och autentisering (manuell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom de här stegen manuellt, en i taget. Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

## <a name="set-up-cloud-shell-session"></a>Konfigurera Cloud Shell-session
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Skapa Azure Digitals-instansen

I det här avsnittet ska du **skapa en ny instans av Azure Digitals dubbla** med kommandot Cloud Shell. Du måste ange:
* En resurs grupp att distribuera den i. Om du inte redan har en befintlig resurs grupp i åtanke kan du skapa en nu med det här kommandot:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* En region för distributionen. Om du vill se vilka regioner som stöder Azure Digitals, kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Ett namn för instansen. Namnet på den nya instansen måste vara unikt inom regionen för din prenumeration (vilket innebär att om din prenumeration har en annan Azure Digital-instans i den region som redan använder det namn du väljer, blir du ombedd att välja ett annat namn).

Använd de här värdena i följande kommando för att skapa instansen:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success"></a>Verifieringen lyckades

Om instansen har skapats ser resultatet i Cloud Shell ut ungefär så här, vilket innebär att information om resursen som du har skapat visas.

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="Fönstret Kommando med att skapa en resurs grupp och en Azure Digital-instans":::

Observera att Azure Digitals dubbla instansen *värdnamn*, *namn*och *resourceGroup* från utdata. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen för att konfigurera autentisering och relaterade Azure-resurser.

> [!TIP]
> Du kan se dessa egenskaper, tillsammans med alla egenskaper för din instans, när som helst genom att köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-your-users-access-permissions"></a>Konfigurera din användares åtkomst behörigheter

Azure Digitals dubbla använder [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) för rollbaserad åtkomst kontroll (RBAC). Det innebär att innan en användare kan göra data planet anrop till din Azure Digital-instansen, måste användaren först tilldelas en roll med behörighet att göra det.

För Azure Digitals dubbla är den här rollen _**Azure Digitals-ägare (för hands version)**_. Du kan läsa mer om roller och säkerhet i [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md).

I det här avsnittet visas hur du skapar en roll tilldelning för en användare i Azure Digitals-instansen, via e-post som är kopplad till Azure AD-klienten i din Azure-prenumeration. Beroende på din roll och dina behörigheter för din Azure-prenumeration kan du antingen ställa in detta själv eller ställa in detta på uppdrag av någon annan som ska hantera Azure Digitals-instansen.

### <a name="assign-the-role"></a>Tilldela rollen

Om du vill ge en användare behörighet att hantera en Azure Digitals-instans måste du tilldela dem rollen _**Azure Digitals-ägare (förhands granskning)**_ i instansen. 

> [!NOTE]
> Observera att den här rollen skiljer sig från rollen Azure AD- *ägare* , som också kan tilldelas i omfånget för Azure Digital-instansen. Detta är två distinkta hanterings roller och Azure AD- *ägaren* beviljar inte åtkomst till data Plans funktioner som beviljas med *Azure Digitals-ägare (för hands version)*.

Använd följande kommando för att tilldela rollen (måste köras av en ägare av Azure-prenumerationen):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Resultatet av det här kommandot är information om den roll tilldelning som har skapats.

> [!TIP]
> Om du får ett *400: BadRequest* -fel i stället kör du följande kommando för att hämta *ObjectID* för användaren:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Upprepa sedan kommandot roll tilldelning med användarens *objekt-ID* i stället för e-postmeddelandet.

### <a name="verify-success"></a>Verifieringen lyckades

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Nu har du en Azure Digital-instansen som är redo att gå och har tilldelats behörighet att hantera den. Sedan konfigurerar du behörigheter för en klient-app för att komma åt den.

## <a name="set-up-access-permissions-for-client-applications"></a>Konfigurera åtkomst behörigheter för klient program

När du har konfigurerat en Azure Digital-instansen är det vanligt att interagera med den instansen via ett klient program som du skapar. För att göra detta måste du kontrol lera att klient programmet kommer att kunna autentisera mot Azures digitala dubbla. Det gör du genom att konfigurera en [Azure Active Directory (Azure AD)-](../active-directory/fundamentals/active-directory-whatis.md) **app-registrering** för din klient app som ska användas.

Den här appens registrering är den plats där du konfigurerar åtkomst behörigheter till [Azure Digitals dubbla API: er](how-to-use-apis-sdks.md). Senare kommer klient programmet att autentiseras mot appens registrering och därför beviljas de konfigurerade åtkomst behörigheterna till API: erna.

>[!TIP]
> Som prenumerations ägare kanske du föredrar att ställa in en ny app-registrering för varje ny Azure Digital-instansen, *eller* för att göra detta bara en gång och upprätta en enda app-registrering som delas mellan alla Azure Digital-instanser i prenumerationen.

### <a name="create-the-registration"></a>Skapa registreringen

Om du vill skapa en app-registrering måste du ange resurs-ID: n för Azure Digitals dubbla API: er och bas linje behörigheterna till API: et. Öppna en ny fil i arbets katalogen och ange följande JSON-kodfragment för att konfigurera den här informationen: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Spara filen som *manifest.jspå*.

> [!NOTE] 
> Det finns vissa platser där en "läsvänlig", läsbar sträng `https://digitaltwins.azure.net` kan användas för resurs-ID: t för Azure Digital tillsammans i stället för GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Till exempel använder många exempel i den här dokumentationen autentisering med MSAL-biblioteket och den egna strängen kan användas för det. Men under det här steget för att skapa appens registrering krävs GUID-formatet för ID: t som visas ovan. 

I Cloud Shell-fönstret klickar du på ikonen Ladda upp/ladda ned filer och väljer överför.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Cloud Shell fönster som visar val av överförings alternativ":::
Navigera till *manifest.jspå* du nyss skapade och tryck på "öppna".

Kör sedan följande kommando för att skapa en app-registrering (ersätter plats hållare vid behov):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Här är ett utdrag av utdata från det här kommandot som visar information om registreringen du har skapat:

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="Cloud Shell utdata från ny Azure AD App-registrering":::

### <a name="verify-success"></a>Verifieringen lyckades

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Kontrol lera först att inställningarna från din uppladdade *manifest.jspå* har angetts korrekt i registreringen. Det gör du genom att välja *manifest* från meny raden för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter fälten från din *manifest.js* under `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Samla in viktiga värden

Välj sedan *Översikt* på Meny raden för att se information om appens registrering:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Portal visning av viktiga värden för appens registrering":::

Anteckna *program* -ID och *katalog (klient)-ID: t* som **visas på sidan** . De här värdena kommer att behövas senare för att [autentisera en klient app mot de Azure Digitals dubbla API: er](how-to-authenticate-client.md). Om du inte är den person som ska skriva kod för sådana program måste du dela dessa värden med den person som ska vara.

### <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Nästa steg

Se hur du ansluter klient programmet till din instans genom att skriva klient appens autentiseringsnyckel:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)
