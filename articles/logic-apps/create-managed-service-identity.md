---
title: Autentisera med hanterade identiteter
description: Komma åt resurser i andra Azure Active Directory-klienter utan att logga in med autentiseringsuppgifter eller hemligheter med hjälp av en hanterad identitet
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117291"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps

Om du vill komma åt resurser i andra Azure Active Directory-klienter (Azure AD) och autentisera din identitet utan att logga in kan logikappen använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare hanterad tjänstidentitet eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter.

Azure Logic Apps stöder både [*systemtilldelade*](../active-directory/managed-identities-azure-resources/overview.md) och [*användartilldelade*](../active-directory/managed-identities-azure-resources/overview.md) hanterade identiteter. Logikappen kan använda antingen den systemtilldelade identiteten eller en *enda* användartilldelad identitet, som du kan dela över en grupp logikappar, men inte båda. För närvarande stöder endast [specifika inbyggda utlösare och åtgärder](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) hanterade identiteter, inte hanterade kopplingar eller anslutningar, till exempel:

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

Den här artikeln visar hur du ställer in båda typerna av hanterade identiteter för logikappen. Mer information finns i de här ämnena:

* [Utlösare och åtgärder som stöder hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Autentiseringstyper som stöds vid utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Begränsningar för hanterade identiteter för logikappar](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-tjänster som stöder Azure AD-autentisering med hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Både den hanterade identiteten och mål Azure-resursen där du behöver åtkomst måste använda samma Azure-prenumeration.

* Om du vill ge en hanterad identitetsåtkomst till en Azure-resurs måste du lägga till en roll i målresursen för den identiteten. Om du vill lägga till roller behöver du [Azure AD-administratörsbehörigheter](../active-directory/users-groups-roles/directory-assign-admin-roles.md) som kan tilldela roller till identiteter i motsvarande Azure AD-klientorganisation.

* Målet Azure-resurs som du vill komma åt. På den här resursen lägger du till en roll för den hanterade identiteten, vilket hjälper logikappen att autentisera åtkomst till målresursen.

* Logikappen där du vill använda [utlösaren eller åtgärderna som stöder hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Aktivera hanterad identitet

Om du vill konfigurera den hanterade identitet som du vill använda följer du länken för den identiteten:

* [Systemtilldelad identitet](#system-assigned)
* [Användartilldelad identitet](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Aktivera systemtilldelad identitet

Till skillnad från användartilldelade identiteter behöver du inte skapa den systemtilldelade identiteten manuellt. Om du vill konfigurera den systemtilldelade identiteten för logikappen här är de alternativ som du kan använda:

* [Azure-portal](#azure-portal-system-logic-app)
* [Azure Resource Manager-mallar](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivera systemtilldelad identitet i Azure-portalen

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Välj **Identitet**under **Inställningar**på logikapp-menyn. Välj **System som tilldelats** > **vid** > **spara**. När Azure uppmanar dig att bekräfta väljer du **Ja**.

   ![Aktivera den systemtilldelade identiteten](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Om du får ett felmeddelande om att du bara kan ha en enda hanterad identitet är logikappen redan associerad med den användartilldelade identiteten. Innan du kan lägga till den systemtilldelade identiteten måste du först *ta bort* den användartilldelade identiteten från logikappen.

   Din logikapp kan nu använda den systemtilldelade identiteten, som är registrerad med Azure Active Directory och representeras av ett objekt-ID.

   ![Objekt-ID för systemtilldelad identitet](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Objekt-ID** | <*identitet-resurs-ID*> | En globalt unik identifierare (GUID) som representerar den systemtilldelade identiteten för logikappen i en Azure AD-klient |
   ||||

1. Följ nu stegen [som ger den identitetsåtkomsten till resursen](#access-other-resources) senare i det här avsnittet.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Aktivera systemtilldelad identitet i Azure Resource Manager-mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel logikappar, kan du använda [Azure Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Om du vill aktivera den systemtilldelade hanterade identiteten `identity` för logikappen i mallen lägger du till objektet och den underordnade egenskapen `type` i logikappens resursdefinition i mallen, till exempel:

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

När Azure skapar din logikappresursdefinition får `identity` objektet dessa ytterligare egenskaper:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Egenskap (JSON) | Värde | Beskrivning |
|-----------------|-------|-------------|
| `principalId` | <*huvud-ID*> | GUID (Globally Unique Identifier) för tjänsthuvudobjektet för den hanterade identiteten som representerar logikappen i Azure AD-klienten. Det här GUID-gränssnittet visas ibland `objectID`som ett "objekt-ID" eller . |
| `tenantId` | <*Azure-AD-tenant-ID*> | Guid (Globally Unique Identifier) som representerar Azure AD-klienten där logikappen nu är medlem. I Azure AD-klienten har tjänstens huvudnamn samma namn som logikappinstansen. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Aktivera användartilldelade identitet

Om du vill konfigurera en användartilldelad hanterad identitet för logikappen måste du först skapa den identiteten som en separat fristående Azure-resurs. Här är de alternativ som du kan använda:

* [Azure-portal](#azure-portal-user-identity)
* [Azure Resource Manager-mallar](#template-user-identity)
* Azure PowerShell
  * [Skapa användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Lägg till rolltilldelning](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Skapa användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Lägg till rolltilldelning](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* REST-API för Azure
  * [Skapa användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Lägg till rolltilldelning](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Skapa användartilldelade identitet i Azure-portalen

1. I [Azure-portalen](https://portal.azure.com)anger du `managed identities`i sökrutan på valfri sida och väljer Hanterade **identiteter**.

   ![Sök efter och välj "Hanterade identiteter"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Under **Hanterade identiteter**väljer du **Lägg till**.

   ![Lägg till ny hanterad identitet](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Ange information om din hanterade identitet och välj sedan **Skapa**, till exempel:

   ![Skapa användartilldelade hanterade identitet](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Resursnamn** | Ja | <*användarens tilldelade identitetsnamn*> | Namnet för att ge din användartilldelade identitet. I det här exemplet används "Fabrikam-user-assigned-identity". |
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Namnet på azure-prenumerationen som ska användas |
   | **Resursgrupp** | Ja | <*Azure-resource-group-namn*> | Namnet på den resursgrupp som ska användas. Skapa en ny grupp eller välj en befintlig grupp. I det här exemplet skapas en ny grupp med namnet "fabrikam-managed-identities-RG". |
   | **Location** | Ja | <*Azure-region*> | Azure-regionen där information om din resurs ska lagras. I det här exemplet används "Västra USA". |
   |||||

   Nu kan du lägga till den användartilldelade identiteten i logikappen. Du kan inte lägga till mer än en användartilldelad identitet i logikappen.

1. I Azure-portalen kan du hitta och öppna logikappen i Logic App Designer.

1. Välj **Identitet**under **Inställningar**på logikapp-menyn och välj sedan **Användare tilldelad** > **Lägg till**.

   ![Lägga till användartilldelade hanterade identitet](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. I fönstret **Lägg till användare tilldelad hanterad identitet** väljer du din Azure-prenumeration i listan **Prenumeration** om du inte redan har valt. Leta reda på och välj den användartilldelade identitet som du vill använda i listan som visar *alla* hanterade identiteter i prenumerationen. Om du vill filtrera listan anger du namnet på identitets- eller resursgruppen i sökrutan **Användare tilldelade hanterade identiteter.** När du är klar väljer du **Lägg till**.

   ![Välj den användartilldelade identitet som ska användas](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Om du får ett felmeddelande om att du bara kan ha en enda hanterad identitet är logikappen redan associerad med den systemtilldelade identiteten. Innan du kan lägga till den användartilldelade identiteten måste du först inaktivera den systemtilldelade identiteten i logikappen.

   Logikappen är nu associerad med den användartilldelade hanterade identiteten.

   ![Association med användartilldelade identitet](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Följ nu stegen [som ger den identitetsåtkomsten till resursen](#access-other-resources) senare i det här avsnittet.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Skapa användartilldelade identitet i en Azure Resource Manager-mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel logikappar, kan du använda [Azure Resource Manager-mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), som stöder [användartilldelade identiteter för autentisering](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). I mallens `resources` avsnitt kräver logikappens resursdefinition följande:

* Ett `identity` objekt `type` med egenskapen inställt på`UserAssigned`

* Ett `userAssignedIdentities` underordnat objekt som anger identitetens resurs-ID, `principalId` `clientId` som är ett annat underordnat objekt som har egenskaperna och

I det här exemplet visas en logikappresursdefinition för en `identity` HTTP PUT-begäran och innehåller ett objekt som inte är parameteriserat. Svaret på PUT-begäran och efterföljande `identity` GET-åtgärden har också det här objektet:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Egenskap (JSON) | Värde | Beskrivning |
|-----------------|-------|-------------|
| `principalId` | <*huvud-ID*> | GUID (Globally Unique Identifier) för den användartilldelade hanterade identiteten i Azure AD-klienten |
| `clientId` | <*klient-ID*> | Ett globalt unikt identifierare (GUID) för logikappens nya identitet som används för samtal under körning |
||||

Om mallen även innehåller den hanterade identitetens resursdefinition kan du parametriera `identity` objektet. I det här `userAssignedIdentities` exemplet visas `userAssignedIdentity` hur det underordnade objektet `variables` refererar till en variabel som du definierar i mallens avsnitt. Den här variabeln refererar till resurs-ID för din användartilldelade identitet.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Egenskap (JSON) | Värde | Beskrivning |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-tenant-ID*> | Guid (Globally Unique Identifier) som representerar Azure AD-klienten där den användartilldelade identiteten nu är medlem. I Azure AD-klienten har tjänsthuvudhuvudet samma namn som det användartilldelade identitetsnamnet. |
| `principalId` | <*huvud-ID*> | GUID (Globally Unique Identifier) för den användartilldelade hanterade identiteten i Azure AD-klienten |
| `clientId` | <*klient-ID*> | Ett globalt unikt identifierare (GUID) för logikappens nya identitet som används för samtal under körning |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Ge identitetsåtkomst till resurser

Innan du kan använda logikappens hanterade identitet för autentisering ställer du in åtkomst för den identiteten på Azure-resursen där du planerar att använda identiteten. För att slutföra den här uppgiften, tilldela lämplig roll till den identiteten på målet Azure-resursen. Här är de alternativ som du kan använda:

* [Azure-portal](#azure-portal-assign-access)
* [Azure Resource Manager-mall](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - Mer information finns i [Lägga till rolltilldelning med hjälp av Azure RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - Mer information finns i Lägga till [rolltilldelning med hjälp av Azure RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [REST-API för Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Tilldela åtkomst i Azure-portalen

1. I [Azure-portalen](https://portal.azure.com)går du till Azure-resursen där du vill att din hanterade identitet ska ha åtkomst.

1. På resursens meny väljer du > **IAM-rolltilldelningar** **(Access control)** där du kan granska de aktuella rolltilldelningarna för den resursen. Välj **Lägg till** > **rolltilldelning**i verktygsfältet .

   ![Välj "Lägg till" > "Lägg till rolltilldelning"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Om alternativet **Lägg till rolltilldelning** är inaktiverat har du troligen inte behörighet. Mer information om de behörigheter som gör att du kan hantera roller för resurser finns [i Administratörsrollbehörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Under **Lägg till rolltilldelning**väljer du en **roll** som ger din identitet nödvändig åtkomst till målresursen.

   I det här avsnittets exempel behöver din identitet en [roll som kan komma åt blobben i en Azure Storage-behållare](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![Välj rollen "Storage Blob Data Contributor"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Följ dessa steg för din hanterade identitet:

   * **Systemtilldelad identitet**

     1. Välj **Logic App**i rutan **Tilldela åtkomst** . När egenskapen **Prenumeration** visas väljer du den Azure-prenumeration som är kopplad till din identitet.

        ![Välj åtkomst för systemtilldelad identitet](./media/create-managed-service-identity/assign-access-system.png)

     1. Välj logikappen i listan under rutan **Välj.** Om listan är för lång använder du rutan **Markera** för att filtrera listan.

        ![Välj logikapp för systemtilldelad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Användartilldelad identitet**

     1. Välj **Användartilldelad hanterad identitet**i rutan **Tilldela åtkomst** till . När egenskapen **Prenumeration** visas väljer du den Azure-prenumeration som är kopplad till din identitet.

        ![Välj åtkomst för användartilldelade identitet](./media/create-managed-service-identity/assign-access-user.png)

     1. Välj din identitet i listan under rutan **Välj.** Om listan är för lång använder du rutan **Markera** för att filtrera listan.

        ![Välj din användartilldelade identitet](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. När du är klar väljer du **Spara**.

   Målresursens rolltilldelningslista visar nu den valda hanterade identiteten och rollen. Det här exemplet visar hur du kan använda den systemtilldelade identiteten för en logikapp och en användartilldelad identitet för en grupp andra logikappar.

   ![Lade till hanterade identiteter och roller för att rikta resursen](./media/create-managed-service-identity/added-roles-for-identities.png)

   Om du vill ha mer information [kan du tilldela en hanterad identitetsåtkomst till en resurs med hjälp av Azure-portalen](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Följ nu [stegen för att autentisera åtkomst med identiteten](#authenticate-access-with-identity) i en utlösare eller åtgärd som stöder hanterade identiteter.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autentisera åtkomst med hanterad identitet

När du [har aktiverat den hanterade identiteten för logikappen](#azure-portal-system-logic-app) och gett [identitetsåtkomst till målresursen eller entiteten](#access-other-resources)kan du använda den identiteten i [utlösare och åtgärder som stöder hanterade identiteter](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Om du har en Azure-funktion där du vill använda den systemtilldelade identiteten aktiverar du först [autentisering för Azure-funktioner](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

De här stegen visar hur du använder den hanterade identiteten med en utlösare eller åtgärd via Azure-portalen. Information om hur du anger den hanterade identiteten i en utlösare eller åtgärds underliggande JSON-definition finns i [Autentisering av hanterad identitet](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Om du inte har gjort det ännu lägger du till [utlösaren eller åtgärden som stöder hanterade identiteter](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   HTTP-utlösaren eller åtgärden kan till exempel använda den systemtilldelade identitet som du har aktiverat för logikappen. I allmänhet använder HTTP-utlösaren eller åtgärden dessa egenskaper för att ange den resurs eller entitet som du vill komma åt:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Metod** | Ja | DEN HTTP-metod som används av åtgärden som du vill köra |
   | **Uri** | Ja | Slutpunkts-URL:en för åtkomst till mål-Azure-resursen eller entiteten. URI-syntaxen innehåller vanligtvis [resurs-ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) för Azure-resursen eller tjänsten. |
   | **Rubriker** | Inga | Alla rubrikvärden som du behöver eller vill inkludera i den utgående begäran, till exempel innehållstypen |
   | **Frågor** | Inga | Alla frågeparametrar som du behöver eller vill inkludera i begäran, till exempel parametern för en viss åtgärd eller API-versionen för den åtgärd som du vill köra |
   | **Autentisering** | Ja | Den autentiseringstyp som ska användas för att autentisera åtkomst till målresursen eller entiteten |
   ||||

   Anta som ett specifikt exempel att du vill köra [åtgärden Ögonblicksbildblob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) på en blob i Azure Storage-kontot där du tidigare har konfigurerat åtkomst för din identitet. [Azure Blob Storage-anslutningsappen](https://docs.microsoft.com/connectors/azureblob/) erbjuder dock inte den här åtgärden. I stället kan du köra den här åtgärden med hjälp av [HTTP-åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) eller en annan [Blob Service REST API-åtgärd](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Om du vill komma åt Azure-lagringskonton bakom brandväggar med hjälp av HTTP-begäranden och hanterade identiteter kontrollerar du att du också konfigurerar ditt lagringskonto med [undantaget som tillåter åtkomst av betrodda Microsoft-tjänster](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Om du vill köra [åtgärden Ögonblicksbildblob anger](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)HTTP-åtgärden dessa egenskaper:

   | Egenskap | Krävs | Exempelvärde | Beskrivning |
   |----------|----------|---------------|-------------|
   | **Metod** | Ja | `PUT`| HTTP-metoden som åtgärden Ögonblicksbildblob använder |
   | **Uri** | Ja | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Resurs-ID för en Azure Blob Storage-fil i Azure Global (offentlig) miljö, som använder den här syntaxen |
   | **Rubriker** | Ja, för Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Huvudvärdena `x-ms-blob-type` och `x-ms-version` huvudvärdena som krävs för Azure Storage-åtgärder. <p><p>**Viktigt:** I utgående HTTP-utlösare och åtgärdsbegäranden för Azure Storage kräver huvudet egenskapen `x-ms-version` och API-versionen för den åtgärd som du vill köra. <p>Mer information finns i de här ämnena: <p><p>- [Begär rubriker - Ögonblicksbildblob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Versionshantering för Azure Storage-tjänster](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Frågor** | Ja, för den här åtgärden | `comp` = `snapshot` | Frågeparameterns namn och värde för åtgärden Ögonblicksbildblob. |
   |||||

   Här är exemplet HTTP-åtgärd som visar alla dessa egenskapsvärden:

   ![Lägga till en HTTP-åtgärd för att komma åt en Azure-resurs](./media/create-managed-service-identity/http-action-example.png)

1. Lägg nu till egenskapen **Autentisering** i HTTP-åtgärden. Välj **Autentisering**i listan **Lägg till ny parameter** .

   ![Lägg till egenskapen Autentisering i HTTP-åtgärd](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Alla utlösare och åtgärder stöder inte att du kan lägga till en autentiseringstyp. Mer information finns i [Lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Välj **Hanterad identitet**i listan **Autentiseringstyp** .

   ![För "Autentisering" väljer du "Hanterad identitet"](./media/create-managed-service-identity/select-managed-identity.png)

1. Välj bland de tillgängliga alternativen i listan över hanterade identiteter baserat på ditt scenario.

   * Om du ställer in den systemtilldelade identiteten väljer du **Systemtilldelad hanterad identitet** om den inte redan är markerad.

     ![Välj "Systemtilldelad hanterad identitet"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Om du har konfigurerat en användartilldelad identitet väljer du den identiteten om den inte redan är markerad.

     ![Välj den användartilldelade identiteten](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Det här exemplet fortsätter med den **systemtilldelade hanterade identiteten**.

1. På vissa utlösare och åtgärder visas egenskapen **Målgrupp** även för att du ska ange målresurs-ID. Ange egenskapen **Målgrupp** till [resurs-ID för målresursen eller måltjänsten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Annars använder egenskapen **Målgrupp** som `https://management.azure.com/` standard resurs-ID: et, som är resurs-ID för Azure Resource Manager.

   > [!IMPORTANT]
   > Kontrollera att målresurs-ID:t *exakt matchar* det värde som Azure Active Directory (AD) förväntar sig, inklusive eventuella nödvändiga efterföljande snedstreck. Resurs-ID för alla Azure Blob Storage-konton kräver till exempel ett efterföljande snedstreck. Resurs-ID:t för ett visst lagringskonto kräver dock inte ett efterföljande snedstreck. Kontrollera [resurs-ID:na för Azure-tjänsterna som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   I det **Audience** här exemplet `https://storage.azure.com/` anges egenskapen Målgrupp så att åtkomsttoken som används för autentisering är giltiga för alla lagringskonton. Du kan dock också ange url:en för rottjänsten `https://fabrikamstorageaccount.blob.core.windows.net`för ett visst lagringskonto.

   ![Ange egenskapen Målgrupp för att rikta in sig på resurs-ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Mer information om hur du auktoriserar åtkomst med Azure AD för Azure Storage finns i följande avsnitt:

   * [Auktorisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Auktorisera åtkomst till Azure Storage med Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fortsätt att skapa logikappen som du vill.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Inaktivera hanterad identitet

Om du vill sluta använda en hanterad identitet för logikappen har du följande alternativ:

* [Azure-portal](#azure-portal-disable)
* [Azure Resource Manager-mallar](#template-disable)
* Azure PowerShell
  * [Ta bort rolltilldelning](../role-based-access-control/role-assignments-powershell.md)
  * [Ta bort användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Ta bort rolltilldelning](../role-based-access-control/role-assignments-cli.md)
  * [Ta bort användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* REST-API för Azure
  * [Ta bort rolltilldelning](../role-based-access-control/role-assignments-rest.md)
  * [Ta bort användartilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Om du tar bort logikappen tar Azure automatiskt bort den hanterade identiteten från Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Inaktivera hanterad identitet i Azure-portalen

I Azure-portalen tar du först bort identitetens åtkomst till [din målresurs](#disable-identity-target-resource). Stäng sedan av den systemtilldelade identiteten eller ta bort den användartilldelade identiteten från [logikappen](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Ta bort identitetsåtkomst från resurser

1. I [Azure-portalen](https://portal.azure.com)går du till azure-målresursen där du vill ta bort åtkomst för den hanterade identiteten.

1. Välj **Åtkomstkontroll (IAM) på**målresursens meny . Välj **Rolltilldelningar under verktygsfältet**.

1. I rolllistan väljer du de hanterade identiteter som du vill ta bort. Välj **Ta bort**i verktygsfältet .

   > [!TIP]
   > Om alternativet **Ta bort** är inaktiverat har du troligen inte behörighet. Mer information om de behörigheter som gör att du kan hantera roller för resurser finns [i Administratörsrollbehörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Den hanterade identiteten tas nu bort och har inte längre åtkomst till målresursen.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Inaktivera hanterad identitet i logikappen

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Välj **Identitet**under **Inställningar**på logikapp-menyn och följ sedan stegen för din identitet:

   * Välj **System som tilldelats** > **vid** > **spara**. När Azure uppmanar dig att bekräfta väljer du **Ja**.

     ![Inaktivera den systemtilldelade identiteten](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Välj **Användare tilldelad** och den hanterade identiteten och välj sedan **Ta bort**. När Azure uppmanar dig att bekräfta väljer du **Ja**.

     ![Ta bort den användartilldelade identiteten](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Den hanterade identiteten är nu inaktiverad i logikappen.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Inaktivera hanterad identitet i Azure Resource Manager-mall

Om du har skapat logikappens hanterade identitet med hjälp `identity` av `type` en Azure `None`Resource Manager-mall anger du objektets underordnade egenskap till . För den systemhanterade identiteten tar den här åtgärden också bort huvud-ID:et från Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data i Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)