---
title: Konfigurera en instans och autentisering (CLI)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av Azure Digitals dubbla tjänster med hjälp av CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ba03acabb3325045a71d55f583343a26b4d121ca
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832370"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Konfigurera en digital Azure-instans och autentisering (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom dessa steg manuellt, en i taget, med hjälp av CLI.
* Om du vill gå igenom de här stegen manuellt med Azure Portal, se Portal versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (portal)*](how-to-set-up-instance-portal.md).
* Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-role-cli.md](../../includes/digital-twins-setup-role-cli.md)]

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

### <a name="verify-success-and-collect-important-values"></a>Verifiera lyckade och samla in viktiga värden

Om instansen har skapats ser resultatet i Cloud Shell ut ungefär så här, vilket innebär att information om resursen som du har skapat visas.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Fönstret Kommando med att skapa en resurs grupp och en Azure Digital-instans":::

Observera att Azure Digitals dubbla instansen *värdnamn*, *namn*och *resourceGroup* från utdata. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen för att konfigurera autentisering och relaterade Azure-resurser. Om andra användare kommer att program mera mot instansen bör du dela dessa värden med dem.

> [!TIP]
> Du kan se dessa egenskaper, tillsammans med alla egenskaper för din instans, när som helst genom att köra `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-user-access-permissions"></a>Konfigurera användar åtkomst behörigheter

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Använd följande kommando för att tilldela rollen (måste köras av en ägare av Azure-prenumerationen):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Resultatet av det här kommandot är information om den roll tilldelning som har skapats.

> [!NOTE]
> Om det här kommandot returnerar ett fel som anger att CLI **inte kan hitta användare eller tjänstens huvud namn i diagram databasen**:
>
> Använd användarens *objekt-ID* i stället för e-post. Detta kan inträffa för användare på personliga [Microsoft-konton (MSA: er)](https://account.microsoft.com/account). 
>
> Använd [Azure Portal sidan för Azure Active Directory användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) för att välja användar kontot och öppna dess information. Kopiera användarens *ObjectID*:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Visning av användar sida i Azure Portal som markerar GUID i fältet objekt-ID" lightbox="media/includes/user-id.png":::
>
> Upprepa sedan kommandot roll tilldelnings lista med användarens *objekt-ID* i stället för e-postmeddelandet.

### <a name="verify-success"></a>Verifieringen lyckades

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Nu har du en Azure Digital-instansen som är redo att gå och har tilldelats behörighet att hantera den. Sedan konfigurerar du behörigheter för en klient-app för att komma åt den.

## <a name="set-up-access-permissions-for-client-applications"></a>Konfigurera åtkomst behörigheter för klient program

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Om du vill skapa en app-registrering måste du ange resurs-ID: n för Azure Digitals dubbla API: er och bas linje behörigheterna till API: et.

I arbets katalogen skapar du en ny fil och anger följande JSON-kodfragment för att konfigurera den här informationen: 

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

Spara filen som _**manifest.jspå**_.

> [!NOTE] 
> Det finns vissa platser där en "läsvänlig", läsbar sträng `https://digitaltwins.azure.net` kan användas för resurs-ID: t för Azure Digital tillsammans i stället för GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Till exempel använder många exempel i den här dokumentationen autentisering med MSAL-biblioteket och den egna strängen kan användas för det. Men under det här steget för att skapa appens registrering krävs GUID-formatet för ID: t som visas ovan. 

Sedan laddar du upp den här filen till Cloud Shell. I Cloud Shell-fönstret klickar du på ikonen Ladda upp/ladda ned filer och väljer överför.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell fönster som visar val av överförings alternativ":::
Navigera till *manifest.jspå* du nyss skapade och tryck på "öppna".

Kör sedan följande kommando för att skapa en app-registrering (ersätter plats hållare vid behov):

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Här är ett utdrag av utdata från det här kommandot som visar information om registreringen du har skapat:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="Cloud Shell utdata från ny Azure AD App-registrering":::

### <a name="verify-success"></a>Verifieringen lyckades

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Kontrol lera först att inställningarna från din uppladdade *manifest.jspå* har angetts korrekt i registreringen. Det gör du genom att välja *manifest* från meny raden för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter fälten från din *manifest.js* under `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Samla in viktiga värden

Välj sedan *Översikt* på Meny raden för att se information om appens registrering:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Portal visning av viktiga värden för appens registrering":::

Anteckna *program* -ID och *katalog (klient)-ID: t* som **visas på sidan** . De här värdena kommer att behövas senare för att [autentisera en klient app mot de Azure Digitals dubbla API: er](how-to-authenticate-client.md). Om du inte är den person som ska skriva kod för sådana program måste du dela dessa värden med den person som ska vara.

### <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Nästa steg

Se hur du ansluter klient programmet till din instans genom att skriva klient appens autentiseringsnyckel:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)