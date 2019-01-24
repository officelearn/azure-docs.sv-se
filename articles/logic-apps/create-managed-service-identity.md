---
title: Autentisera med hanterade identiteter – Azure Logic Apps | Microsoft Docs
description: Du kan skapa en hanterad identitet (kallades tidigare hanterad tjänstidentitet eller MSI) för att autentisera utan att logga in, så att logikappen kan komma åt resurser i andra Azure Active Directory (Azure AD) klienter utan autentiseringsuppgifter eller hemligheter
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: a22512a960426cc21f4f012e06b9df4fa86e637e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807277"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Autentisera och få åtkomst till resurser med hanterade identiteter i Azure Logic Apps

För att komma åt resurser i andra Azure Active Directory (Azure AD)-klienter och verifiera din identitet utan att logga in, logikappen kan använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare kallat hanterad tjänstidentitet eller MSI), snarare än autentiseringsuppgifter och hemligheter. Azure hanterar den här identiteten för dig och hjälper dig att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Den här artikeln visar hur du kan konfigurera och använda en automatiskt genererad hanterad identitet för din logikapp. Läs mer om hanterade identiteter [vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Du kan för närvarande har upp till 10 logikapparbetsflöden med systemtilldelade hanterade identiteter i varje Azure-prenumeration.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration, eller om du inte har en prenumeration <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Logikappen där du vill använda den systemtilldelade hanterad identitet. Om du inte har en logikapp kan se [skapa ditt första logikapparbetsflöde](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Aktivera hanterad identitet

För systemtilldelade hanterade identiteter har du inte manuellt skapa den identiteten. Om du vill konfigurera en automatiskt genererad hanterad identitet för din logikapp kan du använda följande sätt: 

* [Azure Portal](#azure-portal) 
* [Azure Resource Manager-mallar](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera en automatiskt genererad hanterad identitet för din logikapp via Azure portal, aktivera den **systemtilldelad** i inställningarna för din logikapp.

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. På logikappmenyn, under **inställningar**väljer **identitet**. 

1. Under **systemtilldelad** > **Status**, Välj **på**. Välj **spara** > **Ja**.

   ![Aktivera hanterad identitet inställning](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Logikappen har nu en automatiskt genererad hanterad identitet i Azure Active Directory:

   ![GUID för objekt-ID](./media/create-managed-service-identity/object-id.png)

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------| 
   | **Objekt-ID** | <*identity-resource-ID*> | En globalt unik identifierare (GUID) som representerar den systemtilldelade hanterad identitet för din logikapp i en Azure AD-klient | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

När du vill automatisera skapa och distribuera Azure-resurser, till exempel logikappar kan du använda [Azure Resource Manager-mallar](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). När du skapar en automatiskt genererad hanterad identitet för din logikapp via en mall till den `"identity"` element och `"type"` egenskap logic app-arbetsflödesdefinitionen i mallen för distribution: 

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

När Azure skapar din logikapp kan innehåller den logikapp arbetsflödesdefinitionen dessa ytterligare egenskaper:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Egenskap  | Värde | Beskrivning | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | En globalt unik identifierare (GUID) som representerar logikappen i Azure AD-klient och ibland visas som ”objekt-ID” eller `objectID` | 
| **tenantId** | <*Azure-AD-tenant-ID*> | En globalt unik identifierare (GUID) som representerar den Azure AD-klient där logikappen är nu medlem. Tjänstens huvudnamn har samma namn som den logic app-instansen i Azure AD-klient. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Få åtkomst till resurser med hanterad identitet

När du har skapat en automatiskt genererad hanterad identitet för din logikapp kan du [ge den identitet åtkomsten till andra Azure-resurser](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Du kan sedan använda den identiteten för autentisering, precis som med andra [tjänstens huvudnamn](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Både systemtilldelade hanterad identitet och resursen som du vill tilldela åtkomst måste ha samma Azure-prenumeration.

### <a name="assign-access-to-managed-identity"></a>Tilldela åtkomst till hanterad identitet

Följ dessa steg om du vill ge åtkomst till en annan Azure-resurs för din logikapp systemtilldelade hanterad identitet:

1. Gå till Azure-resursen som du vill tilldela åtkomst för din hanterade identitet i Azure-portalen. 

1. Resursens menyn och välj **åtkomstkontroll (IAM)**, och välj **Lägg till rolltilldelning**. 

   ![Lägg till rolltilldelning](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Under **Lägg till rolltilldelning**väljer den **rollen** du vill använda för identitet. 

1. I den **tilldela åtkomst till** egenskap, väljer **Azure AD-användare, grupp eller tjänstens huvudnamn**, om inte redan är valt.

1. I den **Välj** box, från och med det första tecknet i logikappens namn, ange logikappens namn. När logikappen visas väljer du logikappen.

   ![Välj logikapp med hanterad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. När du är klar väljer du **Spara**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Autentisera med hanterad identitet i logikapp

När du har konfigurerat din logikapp med en automatiskt genererad hanterad identitet och tilldelas åtkomst till resursen som du vill använda för den identiteten, du kan nu använda den identiteten för autentisering. Du kan till exempel använda en HTTP-åtgärd så att logikappen kan skicka en HTTP-begäran eller anrop till den här resursen. 

1. Lägg till i din logikapp den **HTTP** åtgärd. 

1. Ange informationen som krävs för åtgärden, till exempel begäran **metoden** och **URI** platsen för den resursen som du vill anropa.

   Anta exempelvis att du använder Azure Active Directory (Azure AD)-autentisering med [något av dessa Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication). 
   I den **URI** anger slutpunkts-URL för den Azure-tjänsten. 
   Så om du använder Azure Resource Manager, anger du det här värdet i den **URI** egenskapen:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version-2016-06-01`

1. I HTTP-åtgärd väljer **visa avancerade alternativ**. 

1. Från den **autentisering** väljer **hanterade identiteter**. När du har valt den här autentiseringen i **målgrupp** egenskap visas med standardvärdet för resurs-ID:

   ![Välj ”hanterad identitet”](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > I den **målgrupp** egenskapen, resurs-ID-värdet måste vara en exakt matchning Azure AD förväntar sig, inklusive alla som krävs för avslutande snedstreck. 
   > Du hittar dessa resurs-ID-värden i den här [tabell över Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication). 
   > Till exempel om du använder Azure resurs Manager resurs-ID, kontrollera att URI: N har ett avslutande snedstreck.

1. Fortsätt att skapa logikappen som du vill.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Om du vill inaktivera en automatiskt genererad hanterad identitet i logikappen kan du följa stegen liknar hur du ställer in Identitetshantering via Azure-portalen, Azure Resource Manager-mallar för distribution eller Azure PowerShell. 

När du tar bort logikappen Azure automatiskt att ta bort din logikapp systemtilldelade identiteter från Azure AD.

### <a name="azure-portal"></a>Azure Portal

Om du vill ta bort en automatiskt genererad hanterad identitet för din logikapp via Azure portal måste stänga av den **systemtilldelad** i inställningarna för din logikapp.

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. På logikappmenyn, under **inställningar**väljer **identitet**. 

1. Under **systemtilldelad** > **Status**, Välj **av**. Välj **spara** > **Ja**.

   ![Inaktivera inställningen för hanterad identitet](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Distributionsmall

Om du har skapat den logikapp systemtilldelade hanterad identitet med en Distributionsmall av Azure Resource Manager kan du ange den `"identity"` elementets `"type"` egenskap `"None"`. Den här åtgärden tar också bort ägar-ID från Azure AD. 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).