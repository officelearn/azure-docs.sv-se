---
title: Autentisera med hanterade identiteter – Azure Logic Apps
description: Om du vill autentisera utan att logga in kan du skapa en hanterad identitet (tidigare kallat Hanterad tjänstidentitet eller MSI) så att din Logi Kap par kan komma åt resurser i andra Azure Active Directory (Azure AD) utan autentiseringsuppgifter eller hemligheter
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: bb1443afa14f2a23b807af52ab8fef6ac41ea200
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934039"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Autentisera och få åtkomst till resurser med hanterade identiteter i Azure Logic Apps

För att få åtkomst till resurser i andra Azure Active Directory (Azure AD)-klienter och autentisera din identitet utan att logga in, kan din Logic app använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare HANTERAD TJÄNSTIDENTITET eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten för dig och skyddar dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Den här artikeln visar hur du kan konfigurera och använda en systemtilldelad hanterad identitet för din Logic app. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Din Logi Kap par kan endast använda hanterade identiteter med kopplingar som har stöd för hanterade identiteter. För närvarande stöder bara HTTP-anslutningen hanterade identiteter.
>
> För närvarande kan du ha upp till tio Logic app-arbetsflöden med systemtilldelade hanterade identiteter i varje Azure-prenumeration.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration eller om du inte har en prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic app där du vill använda den systemtilldelade hanterade identiteten. Om du inte har en Logic-app kan du läsa [skapa ditt första Logic app-arbetsflöde](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Aktivera hanterad identitet

För systemtilldelade hanterade identiteter behöver du inte skapa den identiteten manuellt. Om du vill konfigurera en systemtilldelad hanterad identitet för din Logic app kan du använda följande sätt: 

* [Azure Portal](#azure-portal) 
* [Azure Resource Manager mallar](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera en systemtilldelad hanterad identitet för din Logic app via Azure Portal aktiverar du inställningen **systemtilldelad** i din Logic Apps identitets inställningar.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **identitet**.

1. Under **systemtilldelad** > **status**väljer du **på**. Välj sedan **Spara** > **Ja**.

   ![Aktivera hanterad identitets inställning](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Din Logic app har nu en systemtilldelad hanterad identitet registrerad i Azure Active Directory:

   ![GUID för objekt-ID](./media/create-managed-service-identity/object-id.png)

   | Egenskap | Value | Beskrivning |
   |----------|-------|-------------|
   | **Objekt-ID** | <*identity-resource-ID*> | En globalt unik identifierare (GUID) som representerar den systemtilldelade hanterade identiteten för din Logic app i en Azure AD-klient |
   ||||

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

När du vill automatisera skapandet och distributionen av Azure-resurser, till exempel Logic Apps, kan du använda [Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Om du vill skapa en systemtilldelad hanterad identitet för din Logic app via en mall `"identity"` , lägger `"type"` du till element och egenskap i din Logic app Workflow-definition i distributions mal len: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Exempel:

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
         "actions": {}, 
         "parameters": {}, 
         "triggers": {}, 
         "contentVersion": "1.0.0.0", 
         "outputs": {} 
   }, 
   "parameters": {}, 
   "dependsOn": [] 
}
```

När Azure skapar din Logic app innehåller den logiska appens arbets flödes definition följande ytterligare egenskaper:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Egenskap | Value | Beskrivning |
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | En globalt unik identifierare (GUID) som representerar Logic-appen i Azure AD-klienten och som ibland visas som "objekt-ID" eller`objectID` |
| **tenantId** | <*Azure-AD-tenant-ID*> | En globalt unik identifierare (GUID) som representerar Azure AD-klienten där Logic app nu är medlem. I Azure AD-klienten har tjänstens huvud namn samma namn som Logic App-instansen. |
||||

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Åtkomst till resurser med hanterad identitet

När du har skapat en systemtilldelad hanterad identitet för din Logic app kan du [ge den identitets åtkomst till andra Azure-resurser](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Du kan sedan använda identiteten för autentisering, precis som alla andra [tjänstens huvud namn](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Både den systemtilldelade hanterade identiteten och resursen där du vill tilldela åtkomst måste ha samma Azure-prenumeration.

### <a name="assign-access-to-managed-identity"></a>Tilldela åtkomst till hanterad identitet

Följ dessa steg om du vill ge åtkomst till en annan Azure-resurs för din Logic Apps systemtilldelade hanterade identitet:

1. I Azure Portal går du till den Azure-resurs där du vill tilldela åtkomst till din hanterade identitet.

1. På resurs menyn väljer du **åtkomst kontroll (IAM)** . Välj **Lägg** > till**Lägg till roll tilldelning**i verktygsfältet.

   ![Lägg till rolltilldelning](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Under **Lägg till roll tilldelning**väljer du den **roll** som du vill använda för identiteten.

1. I egenskapen **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänstens huvud namn**, om det inte redan har valts.

1. I rutan **Markera** , som börjar med det första specialtecknet i din Logic Apps namn, anger du namnet på din Logic Apps. När din Logi Kap par visas väljer du Logic-appen.

   ![Välj Logic app med hanterad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. När du är klar väljer du **Spara**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Autentisera med hanterad identitet i Logic app

När du har konfigurerat din Logic-app med en systemtilldelad hanterad identitet och tilldelad åtkomst till den resurs du vill använda för den identiteten kan du använda identiteten för autentisering. Du kan till exempel använda en HTTP-åtgärd så att din Logi Kap par kan skicka en HTTP-begäran eller ringa till resursen. 

1. Lägg till **http-** åtgärden i din Logic app.

1. Ange nödvändig information för åtgärden, till exempel **metoden** för begäran och **URI** -platsen för den resurs som du vill anropa.

   Anta till exempel att du använder autentisering med Azure Active Directory (Azure AD) med [någon av dessa Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). I rutan **URI** anger du slut punkts-URL: en för Azure-tjänsten. Om du använder Azure Resource Manager anger du det här värdet i **URI** -egenskapen:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. I HTTP-åtgärden väljer du **Visa avancerade alternativ**.

1. I listan **autentisering** väljer du **hanterad identitet**. När du har valt den här autentiseringen visas **Audience** -egenskapen med standardvärdet för resurs-ID:

   ![Välj "hanterad identitet"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > I egenskapen **Audience** måste resurs-ID-värdet exakt matcha vad Azure AD förväntar sig, inklusive eventuella avslutande snedstreck. 
   > Du kan hitta dessa resurs-ID-värden i den här [tabellen som beskriver Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Om du till exempel använder resurs-ID för Azure resurs Manager ser du till att URI: n har ett avslutande snedstreck.

1. Fortsätt att skapa Logic-appen på det sätt som du vill.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Om du vill inaktivera en systemtilldelad hanterad identitet i din Logic app, kan du följa stegen som liknar hur du konfigurerar identiteten via Azure Portal, Azure Resource Manager mallar för distribution eller Azure PowerShell.

När du tar bort din Logic-app tar Azure automatiskt bort din Logic Apps system-tilldelade identitet från Azure AD.

### <a name="azure-portal"></a>Azure Portal

Om du vill ta bort en systemtilldelad hanterad identitet för din Logic app via Azure Portal inaktiverar du inställningen **systemtilldelad** i din Logic Apps identitets inställningar.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **identitet**.

1. Under **systemtilldelad** > **status**väljer du **av**. Välj sedan **Spara** > **Ja**.

   ![Inaktivera inställningar för hanterad identitet](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Distributionsmall

Om du har skapat den logiska appens systemtilldelade identitet med en mall för Azure Resource Manager distribution anger du `"identity"` `"type"` elementets egenskap till `"None"`. Den här åtgärden tar också bort ägar-ID från Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data i Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)