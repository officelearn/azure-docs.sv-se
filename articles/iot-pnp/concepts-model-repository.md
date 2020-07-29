---
title: Förstå koncepten i Azure IoT Model-lagringsplatsen | Microsoft Docs
description: Som en lösnings utvecklare eller IT-proffs kan du läsa om de grundläggande begreppen i Azure IoT Model-lagringsplatsen.
author: JimacoMS3
ms.author: v-jambra
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 53ae5bf15c303f26d48550734f46e69ef1fcdd75
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352487"
---
# <a name="azure-iot-model-repository"></a>Azure IoT Model-lagringsplats

Med Azure IoT Model-lagringsplatsen kan enhets byggare hantera och dela IoT-Plug and Play enhets modeller. Enhets modellerna är JSON LD-dokument som definierats med hjälp av [DTDL (Digital Garns Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). De modeller som lagras i modell lagrings tjänsten kan delas med en lösnings utvecklare antingen privat via åtkomst kontroll eller offentligt utan att någon autentisering krävs för att integrera och utveckla IoT Plug and Play Cloud-lösningen.

Du kan komma åt modell databasen med hjälp av:

- [Azure IoT-modellens databas](https://aka.ms/iotmodelrepo) Portal
- [Azure IoT Model-lagringsplats REST API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Databas kommandon i Azure CLI IoT Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest)

## <a name="public-models"></a>Offentliga modeller

De offentliga digitala dubbla modeller som lagras i modell databasen är tillgängliga för alla som vill använda och integrera i sina program utan autentisering. Dessutom gör de offentliga modellerna det möjligt för ett öppet eko system för enhets utvecklare och utvecklare att dela och återanvända sina IoT Plug and Play enhets modeller.

Se avsnittet [publicera en modell](#publish-a-model) under **företags modeller** för instruktioner om hur du publicerar en modell i modell databasen för att göra den offentlig.

Så här visar du en offentlig modell med hjälp av modell databas portalen:

1. Gå till [Azure IoT Model-hanteringsportalen](https://aka.ms/iotmodelrepo).

1. Välj i **Visa offentliga modeller**.

    ![Visa offentliga modeller](./media/concepts-model-repository/public-models.png)

Om du vill visa en offentlig modell program mässigt med hjälp av REST API, se [Hämta modell](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API dokumentation.

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("<url>");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Information om hur du visar en offentlig modell med hjälp av CLI finns i kommandot för att [Hämta modell](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show) i Azure CLI.

## <a name="company-models"></a>Företags modeller

Företags modellens lagrings plats är en klient organisation i Azure IoT-modellen för din organisation för att skapa och hantera digitala dubbla modeller som skapats av användare i företaget eller organisationen. Företags modeller är bara tillgängliga för autentiserade användare av företaget eller organisationen. En klient platss klient organisations administratör kan tilldela behörigheter och styra åtkomsten till andra användare i företaget eller organisationen till modellerna i företagets modell databas.

### <a name="set-up-your-company-model-repository"></a>Konfigurera din företags modell databas

Använd ditt *arbets-eller skol Azure Active Directory (Azure AD)-konto* för att komma åt modell databasen. Om din organisation redan har en Azure AD-klient kan du använda användar konton och tjänstens huvud namn från den här Azure AD-klienten.

Information om hur du konfigurerar en Azure AD-klient och hur du skapar en användare eller tjänstens huvud namn i en Azure AD-klient finns i avsnittet [Ytterligare information](#additional-information) .

- Om du är den första användaren från din organisation för att få åtkomst till modell lagrings platsen eller för att logga in på portalen beviljas du rollen **klient organisations administratör** . Med den här rollen kan du tilldela roller till andra användare i din organisations databas innehavare.

- Du kan tilldela andra roller av en **innehavaradministratör** , till exempel **läsa modeller** eller **skapa modeller**.

### <a name="understand-access-management"></a>Förstå åtkomst hantering

I följande tabell sammanfattas de funktioner som stöds i företags modellens databas och deras associerade behörigheter:

| Funktion  | Behörighet| Beskrivning|
|-------------|-----------|------------|
|Läsa modeller|Läsa modeller|Som standard kan alla användare i företagets klient organisation se sina företags modeller. Dessutom kan användaren även visa den eller de privata modeller som delas av andra företag.|
|Hantera åtkomst|Hantera åtkomst|Hantera tilldelningen av användar roller (Lägg till eller ta bort) för andra användare i organisationen.|
|Skapa modeller|Skapa modeller|Skapa modeller i företags modellens lagrings plats.|
|Publicera modeller|Publicera modeller|Publicera modeller för att göra dem offentliga för alla att visa modellen.|

I följande tabell sammanfattas de roller som stöds och deras funktioner i modell databasen som kan användas för åtkomst hantering.

|Roll|Funktion|
|----|----------|
|TenantAdministrator|Hantera åtkomst, läsa modeller|
|Creator|Skapa modeller, läsa modeller|
|Publisher|Publicera modeller, läsa modeller|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Skicka en säkerhetstoken vid åtkomst till företags modeller med en REST API

När du anropar REST-API: erna för att hantera företags modeller som är privata eller delade, måste du ange en autentiseringstoken för användarens eller tjänstens huvud namn i JWT-format. I avsnittet [Ytterligare information](#additional-information) kan du läsa om hur du hämtar en JWT-token för en användare eller tjänstens huvud namn.

JWT-token måste skickas i HTTP-HTTP-huvudet i API: et vid mål organisations modeller eller delade modeller. JWT-token behövs inte vid mål för offentliga modeller.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Visa företagets eller delade modeller

Du måste vara medlem i rollen för platsens klient organisations *läsare* eller så måste modellen delas med dig för att läsa en modell. Du kan se en lista över opublicerade modeller som har delats med dig och en lista över publicerade modeller som har delats med dig. Som standard kan användare läsa företagets modeller, modeller som har delats med dem av andra företag och alla offentliga modeller.

Så här visar du ett företag eller en delad modell med hjälp av portalen:

1. Logga in på [Azure IoT-modellens databas Portal](https://aka.ms/iotmodelrepo).

1. Expandera **företags modeller** – **avpublicerade** i det vänstra fönstret

    ![Visa företags modeller](./media/concepts-model-repository/view-company-models.png)

1. Expandera **delade modeller – avpublicerade** i det vänstra fönstret

    ![Visa delade modeller](./media/concepts-model-repository/view-shared-models.png)

Om du vill visa ett företag eller en delad modell med hjälp av REST API, se dokumentationen [Hämta modell](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API. Mer information om hur du skickar in ett JWT-auktoriseringsarkiv i HTTP-begäran finns i [skicka en säkerhetstoken vid åtkomst till företags modeller med en REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) .

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Om du vill visa en företags modell eller en delad modell med hjälp av CLI, se kommandot för att [Hämta modell](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show) i Azure CLI.

### <a name="manage-roles"></a>Hantera roller

Klient organisations administratören kan tilldela roller till användare i platsens klient organisation så att de kan skapa modeller som är privata för företaget eller organisationen, publicera modeller eller hantera roller för andra användare.

Så här lägger du till en användare i en modell databas klient roll med hjälp av portalen:

1. Logga in på [Azure IoT-modellens databas Portal](https://aka.ms/iotmodelrepo).

1. Välj **åtkomst hantering** i den vänstra rutan och välj sedan **+ Lägg till**. I fönstret **Lägg till behörighet** anger du arbets adressen för den användare som du vill lägga till i rollen:

    ![Lägg till arbets adress](./media/concepts-model-repository/add-user.png)

1. Välj den roll som du vill lägga till användaren i från List rutan **roll** . Välj sedan **Spara**:

    ![Välj roll](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Ladda upp en modell

Du måste vara medlem i plats innehavarens **skapare** roll för att överföra en modell till företags modellens lagrings plats.

Dessa modeller publiceras inte och är bara tillgängliga för användare i din organisation som standard. Du kan också dela en eller flera opublicerade modeller med externa användare.

Överförda modeller är oföränderliga.

Modell-ID: na för dessa modeller måste vara globalt unika för alla databas klienter för alla uppladdade modeller.

Ladda upp en modell med hjälp av portalen:

1. Logga in på [Azure IoT-modellens databas Portal](https://aka.ms/iotmodelrepo).

1. Expandera **företags modeller** i den vänstra rutan och välj **skapa modell**. Välj sedan **Importera JSON**.

    ![Skapa modell](./media/concepts-model-repository/create-model.png)

1. Välj den fil som du vill ladda upp. Om portalen har validerat modellen väljer du **Spara**.

Om du vill överföra en modell med hjälp av REST API går du till [skapa en modell](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) -API. Mer information om hur du skickar in ett JWT-auktoriseringsarkiv i HTTP-begäran finns i [skicka en säkerhetstoken vid åtkomst till företags modeller med en REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) .

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

Om du vill överföra en modell med hjälp av CLI, se Azure CLI [skapa en modell](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create) kommando.

### <a name="publish-a-model"></a>Publicera en modell

Om du vill publicera en modell måste följande krav uppfyllas:

1. Din organisation måste vara medlem i [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) för att publicera en modell. Information om hur du skapar ett partner Center-konto finns i [skapa ett partner Center-konto](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). När ditt konto har godkänts kan du publicera dina modeller. Mer information finns i [vanliga frågor och svar om Partner Center](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. Användaren måste vara medlem i plats innehavarens *utgivar* roll.

Modeller som skapas och publiceras av användare i din organisation visas som *publicerade modeller*. Dessa modeller är offentliga och kan hittas av alla under **offentliga modeller**.

Publicera en modell med hjälp av portalen:

1. Logga in på [Azure IoT-modellens databas Portal](https://aka.ms/iotmodelrepo).

2. Expandera **företags modeller** i den vänstra rutan och välj den modell som du vill publicera. Välj sedan **Publicera**.

    ![Publicera modell](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Om du får ett meddelande om att du inte har ett Microsoft partner-ID (MPN) följer du registrerings stegen i meddelandet. Mer information finns i kraven i början av det här avsnittet.

Information om hur du publicerar en modell med hjälp av REST API finns i dokumentationen [publicera en modell](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API. Ange frågesträngparametern `update-metadata=true` för att publicera en modell med hjälp av REST API. Mer information om hur du skickar in ett JWT-auktoriseringsarkiv i HTTP-begäran finns i [skicka en säkerhetstoken vid åtkomst till företags modeller med en REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) .

Information om hur du publicerar en modell med hjälp av CLI finns i Azure CLI-kommandot [publicera en modell](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish) .

### <a name="share-a-model"></a>Dela en modell

Du kan dela företags modeller som du har skapat med användare av externa organisationer. På så sätt kan du tillåta medarbetare att visa och utveckla lösningar med privata företags modeller.

En enhets tillverkare kan till exempel vilja underhålla modeller som är privata för företaget eller organisationen. De kan ha kunder som kräver att deras enhets funktioner förblir konfidentiella.

Delning av modeller över företag eller organisationer ger säker åtkomst till modeller som inte är offentliga.

Så här delar du en företags modell med portalen:

- Om du är skaparen av en modell är **delningen** och delningen **med** knappar aktiva när du visar modellen i avsnittet **företags modeller** .

    ![Dela modell](./media/concepts-model-repository/share-model.png)

- Om du vill dela modellen med en extern användare väljer du **dela**. I fönstret **dela modell** anger du e-postadressen för den externa användaren och väljer **Spara**.

- Om du vill se de användare som du har delat modellen med, väljer du **delad med**.

- Om du vill sluta dela modellen med en speciell användare väljer du användaren i listan med användare i fönstret **delat med** . Välj sedan **ta bort** och bekräfta ditt val när du uppmanas till det.

    ![Sluta dela](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Ytterligare information

Följande avsnitt är användbara när du arbetar med Azure AD:

- Information om hur du skapar en ny Azure AD-klient finns i [skapa en ny klient i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). De flesta organisationer har redan Azure AD-klienter.

- Om du vill lägga till användare eller gäst användare i en Azure AD-klient läser du [Lägg till eller ta bort användare med Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Om du vill lägga till ett huvud namn för tjänsten i en Azure AD-klient läser [du så här använder du portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Information om hur du hämtar en JWT-token från Azure AD som ska användas när du anropar REST-API: er finns i [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app).

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att granska [IoT plug and Play-arkitekturen](concepts-architecture.md).