---
title: Autentisera med hanterade identiteter – Azure Logic Apps
description: Få åtkomst till resurser i andra Azure Active Directory klienter utan att logga in med autentiseringsuppgifter eller hemligheter genom att använda en hanterad identitet
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: fdc5340c9affa7137815577af842aa8b43a552a8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799629"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps

För att få åtkomst till resurser i andra Azure Active Directory (Azure AD)-klienter och autentisera din identitet utan att logga in, kan din Logic app använda den systemtilldelade [hanterade identiteten](../active-directory/managed-identities-azure-resources/overview.md) (tidigare kallat HANTERAD TJÄNSTIDENTITET eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten för dig och skyddar dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Den här artikeln visar hur du konfigurerar och använder den systemtilldelade hanterade identiteten i din Logic app.

Mer information finns i följande avsnitt:

* [Utlösare och åtgärder som har stöd för hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Azure-tjänster som stöder Azure AD-autentisering med hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Autentiseringstyper som stöds för utgående anrop](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Hanterade identitets gränser för Logic Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration eller om du inte har en prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/). Både den hanterade identiteten och den Azure-resurs där du vill ha åtkomst måste använda samma Azure-prenumeration.

* [Administratörs behörighet för Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) som kan tilldela roller till hanterade identiteter i samma Azure AD-klient som mål resursen. Om du vill ge en hanterad identitets åtkomst till en Azure-resurs måste du lägga till en roll för den identiteten på mål resursen.

* Den mål Azure-resurs som du vill komma åt

* En Logic-app som använder [utlösare och åtgärder som har stöd för hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Aktivera systemtilldelad identitet

Till skillnad från användarens tilldelade identiteter behöver du inte skapa den systemtilldelade identiteten manuellt. Om du vill ställa in din Logic Apps systemtilldelade identitet kan du använda följande alternativ:

* [Azure-portalen](#azure-portal-system-logic-app)
* [Azure Resource Manager mallar](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivera systemtilldelad identitet i Azure Portal

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **identitet** > **system tilldelad**. Under **status**väljer du **på** > **Spara** > **Ja**.

   ![Aktivera den systemtilldelade identiteten](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Din Logic app kan nu använda den systemtilldelade identiteten, som registreras med Azure Active Directory och representeras av ett objekt-ID.

   ![Objekt-ID för systemtilldelad identitet](./media/create-managed-service-identity/object-id.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Objekt-ID** | <*identitet-resurs-ID*> | En globalt unik identifierare (GUID) som representerar den systemtilldelade identiteten för din Logic app i din Azure AD-klient |
   ||||

1. Följ nu [stegen som ger identiteten åtkomst till resursen](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Aktivera systemtilldelad identitet i Azure Resource Manager mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel Logic Apps, kan du använda [Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Om du vill aktivera den systemtilldelade hanterade identiteten för din Logic app i mallen lägger du till `identity`-objektet och den underordnade egenskapen `type` till den logiska appens resurs definition i mallen, till exempel:

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

När Azure skapar din resurs definition för Logic app får `identity`-objektet följande ytterligare egenskaper:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Egenskap (JSON) | Värde | Beskrivning |
|-----------------|-------|-------------|
| `principalId` | <*huvud-ID*> | GUID (globalt unik identifierare) för tjänstens huvud namn för den hanterade identitet som representerar din Logic-app i Azure AD-klienten. Detta GUID visas ibland som "objekt-ID" eller `objectID`. |
| `tenantId` | <*Azure-AD-Tenant-ID*> | GUID (globalt unik identifierare) som representerar Azure AD-klienten där Logic app nu är medlem. I Azure AD-klienten har tjänstens huvud namn samma namn som Logic App-instansen. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Ge identitets åtkomst till resurser

När du har konfigurerat en hanterad identitet för din Logic-app kan du [ge den identitets åtkomst till andra Azure-resurser](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Du kan sedan använda identiteten för autentisering.

1. I [Azure Portal](https://portal.azure.com)går du till den Azure-resurs där du vill att den hanterade identiteten ska ha åtkomst.

1. På resurs menyn väljer du **åtkomst kontroll (IAM)**  > **roll tilldelningar** där du kan granska de aktuella roll tilldelningarna för resursen. Välj **Lägg till** > **Lägg till roll tilldelning**i verktygsfältet.

   ![Välj Lägg till > Lägg till roll tilldelning](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Om alternativet **Lägg till roll tilldelning** är inaktiverat har du förmodligen inte behörighet. Mer information om behörigheter som låter dig hantera roller för resurser finns i [Administratörs roll behörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Under **Lägg till roll tilldelning**väljer du en **roll** som ger din identitet nödvändig åtkomst till mål resursen.

   För det här avsnittets exempel behöver din identitet en [roll som kan komma åt blobben i en Azure Storage-behållare](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Välj rollen "Storage BLOB data Contributor"](./media/create-managed-service-identity/assign-role.png)

1. I rutan **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänstens huvud namn**.

   ![Välj åtkomst för systemtilldelad identitet](./media/create-managed-service-identity/assign-access-system.png)

1. I rutan **Välj** letar du reda på och väljer din Logic app.

   ![Välj Logic app för systemtilldelad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. När du är klar väljer du **Spara**.

   Mål resursens roll tilldelnings lista visar nu den valda hanterade identiteten och rollen.

   ![Hanterade identiteter och roller har lagts till i mål resursen](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Följ nu [stegen för att autentisera åtkomst med identiteten](#authenticate-access-with-identity) i en utlösare eller åtgärd som stöder hanterade identiteter.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autentisera åtkomst med hanterad identitet

När du har [aktiverat den hanterade identiteten för din Logic app](#azure-portal-system-logic-app) och [ger den identitets åtkomst till mål resursen](#access-other-resources), kan du använda den identiteten i [utlösare och åtgärder som stöder hanterade identiteter](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Om du har en Azure-funktion där du vill använda den systemtilldelade identiteten aktiverar du först [autentisering för Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

De här stegen visar hur du använder den hanterade identiteten med en utlösare eller åtgärd via Azure Portal. Information om hur du anger den hanterade identiteten i en utlösare eller åtgärds underliggande JSON-definition finns i [hanterad identitets autentisering](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du inte har gjort det lägger du till utlösaren eller åtgärden [som har stöd för hanterade identiteter](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Anta till exempel att du vill köra [ögonblicks bilds-bloben](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) på en Blob i Azure Storage-kontot där du tidigare har konfigurerat åtkomst till din identitet, men [Azure Blob Storage-anslutningen](/connectors/azureblob/) har för närvarande inte den här åtgärden. I stället kan du använda [http-åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) för att köra åtgärden eller någon annan [BLOB service REST API åtgärder](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs). För autentisering kan HTTP-åtgärden använda den systemtilldelade identitet som du har aktiverat för din Logic app. HTTP-åtgärden använder också dessa egenskaper för att ange den resurs som du vill få åtkomst till:

   * **URI** -egenskapen anger slut punkts-URL: en för att komma åt Azure-resursen. Denna URI-syntax innehåller vanligt vis [resurs-ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) för Azure-resursen eller-tjänsten.

   * Egenskapen **headers** anger eventuella rubrik värden som du behöver eller vill inkludera i begäran, till exempel API-versionen för den åtgärd som du vill köra på mål resursen.

   * Egenskapen **frågor** anger alla frågeparametrar som du behöver ta med i begäran, till exempel parametern för en enskild åtgärd eller en speciell API-version vid behov.

   För att köra [ögonblicks bildens BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)-åtgärd anger http-åtgärden följande egenskaper:

   * **Metod**: anger `PUT` åtgärden.

   * **URI**: anger resurs-ID för en Azure Blob Storage-fil i Azure Global (offentlig) miljö och använder den här syntaxen:

     `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}`

   * **Huvuden**: anger `x-ms-blob-type` som `BlockBlob` och `x-ms-version` som `2019-02-02` för BLOB-åtgärden för ögonblicks bilder. Mer information finns i [begärandehuvuden – ögonblicks bilds-BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) och [versions hantering för Azure Storage Services](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services).

   * **Frågor**: anger `comp` som parameter namn och `snapshot` som parameter värde.

   Här är exempel på HTTP-åtgärd som visar alla dessa egenskaps värden:

   ![Lägg till en HTTP-åtgärd för att få åtkomst till en Azure-resurs](./media/create-managed-service-identity/http-action-example.png)

   Mer information om alla tillgängliga Azure REST API-åtgärder finns i referens för [azure REST API](https://docs.microsoft.com/rest/api/azure/).

1. I listan **autentisering** väljer du **hanterad identitet**. Om egenskapen [ **Authentication** stöds](logic-apps-securing-a-logic-app.md#add-authentication-outbound) men dold, öppnar du listan **Lägg till ny parameter** och väljer **autentisering**.

   > [!NOTE]
   > Inte alla utlösare och åtgärder låter dig välja en autentiseringstyp. Mer information finns i [lägga till autentisering i utgående samtal](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![I egenskapen "autentisering" väljer du "hanterad identitet"](./media/create-managed-service-identity/select-managed-identity.png)

1. När du har valt **hanterad identitet**visas egenskapen **publik** för vissa utlösare och åtgärder. Om **målgrupps** egenskapen stöds men är dold, öppnar du listan **Lägg till ny parameter** och väljer **mål grupp**.

1. Se till att du anger värdet för **mål gruppen** till resurs-ID för mål resursen eller tjänsten. Annars använder **Audience** -egenskapen `https://management.azure.com/` resurs-ID, som är resurs-ID: t för Azure Resource Manager.

   > [!IMPORTANT]
   > Kontrol lera att mål resurs-ID: t *exakt matchar* det värde som Azure Active Directory (AD) förväntar sig, inklusive eventuella avslutande snedstreck. Resurs-ID för alla Azure Blob Storage-konton kräver till exempel ett avslutande snedstreck. Resurs-ID för ett angivet lagrings konto kräver dock inte något avslutande snedstreck. Kontrol lera [resurs-ID: na för de Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   I det här exemplet anges egenskapen **Audience** till `https://storage.azure.com/` så att åtkomsttoken som används för autentisering är giltiga för alla lagrings konton. Du kan dock även ange URL: en för rot tjänsten `https://fabrikamstorageaccount.blob.core.windows.net`för ett angivet lagrings konto.

   ![Ange mål resurs-ID i egenskapen "Audience"](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Mer information om hur du auktoriserar åtkomst med Azure AD för Azure Storage finns i följande avsnitt:

   * [Ge åtkomst till Azure-blobbar och-köer med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Ge åtkomst till Azure Storage med Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Ta bort systemtilldelad identitet

Om du vill sluta använda den systemtilldelade identiteten för din Logic-app har du följande alternativ:

* [Azure-portalen](#azure-portal-disable)
* [Azure Resource Manager mallar](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Om du tar bort din Logic app tar Azure automatiskt bort den hanterade identiteten från Azure AD.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Ta bort systemtilldelad identitet i Azure Portal

I Azure Portal tar du bort den systemtilldelade identiteten [från din Logic app](#disable-identity-logic-app) och identitetens åtkomst [från mål resursen](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Ta bort systemtilldelad identitet från Logic app

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **identitet** > **system tilldelad**. Under **status**väljer du **av** > **Spara** > **Ja**.

   ![Sluta använda systemtilldelad identitet](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Ta bort identitets åtkomst från resurser

1. I [Azure Portal](https://portal.azure.com)går du till den Azure-resurs där du vill ta bort åtkomsten för en hanterad identitet.

1. Från mål resursens meny väljer du **åtkomst kontroll (IAM)** . Under verktygsfältet väljer du **roll tilldelningar**.

1. I listan Roller väljer du de hanterade identiteter som du vill ta bort. Välj **ta bort**i verktygsfältet.

   > [!TIP]
   > Om alternativet **ta bort** är inaktiverat har du förmodligen inte behörighet. Mer information om behörigheter som låter dig hantera roller för resurser finns i [Administratörs roll behörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Den hanterade identiteten tas nu bort och har inte längre åtkomst till mål resursen.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Inaktivera hanterad identitet i Azure Resource Manager mall

Om du har aktiverat den logiska appens systemhanterade identitet med hjälp av en Azure Resource Manager-mall ställer du in `identity` objektets `type` underordnade egenskap `None`. Den här åtgärden tar också bort ägar-ID: t för den systemhanterade identiteten från Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data i Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
