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
ms.date: 10/05/2018
ms.openlocfilehash: 84529e1097678ba7a039ffaeec57a9293c93dafd
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229647"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Autentisera och få åtkomst till resurser med hanterade identiteter i Azure Logic Apps

För att komma åt resurser i andra Azure Active Directory (Azure AD)-klienter och verifiera din identitet utan att logga in, logikappen kan använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare kallat hanterad tjänstidentitet eller MSI), snarare än autentiseringsuppgifter och hemligheter. Azure hanterar den här identiteten för dig och hjälper dig att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Den här artikeln visar hur du kan skapa och använda en automatiskt genererad hanterad identitet för din logikapp. Läs mer om hanterade identiteter [vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Du kan för närvarande har upp till 10 logikapparbetsflöden med systemtilldelade hanterade identiteter i varje Azure-prenumeration.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration, eller om du inte har en prenumeration <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Logikappen där du vill använda den systemtilldelade hanterad identitet. Om du inte har en logikapp kan se [skapa ditt första logikapparbetsflöde](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Skapa hanterad identitet

Du kan skapa eller aktivera en automatiskt genererad hanterad identitet för din logikapp via Azure-portalen, Azure Resource Manager-mallar eller Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Om du vill aktivera en automatiskt genererad hanterad identitet för din logikapp via Azure portal, aktivera den **registreras med Azure Active Directory** i inställningar för logikappens arbetsflöde.

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. Följ de här stegen: 

   1. På logikappmenyn, under **inställningar**väljer **arbetsflödesinställningarna**. 

   1. Under **hanterad tjänstidentitet** > 
    **registreras med Azure Active Directory**, Välj **på**.

   1. När du är klar väljer **spara** i verktygsfältet.

      ![Aktivera hanterad identitet inställning](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Logikappen har nu en automatiskt genererad hanterad identitet registrerad i Azure Active Directory med dessa egenskaper och värden:

      ![GUID för ägar-ID och klient-ID](./media/create-managed-service-identity/principal-tenant-id.png)

      | Egenskap  | Värde | Beskrivning | 
      |----------|-------|-------------| 
      | **Ägar-ID** | <*huvudkonto-ID*> | En globalt unik identifierare (GUID) som representerar logikappen i en Azure AD-klient | 
      | **Klient-ID** | <*Azure-AD-klient-ID*> | En globalt unik identifierare (GUID) som representerar den Azure AD-klient där logikappen är nu medlem. Tjänstens huvudnamn har samma namn som den logic app-instansen i Azure AD-klient. | 
      ||| 

### <a name="deployment-template"></a>Distributionsmall

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
| **principalId** | <*huvudkonto-ID*> | En globalt unik identifierare (GUID) som representerar logikappen i Azure AD-klient | 
| **TenantId** | <*Azure-AD-klient-ID*> | En globalt unik identifierare (GUID) som representerar den Azure AD-klient där logikappen är nu medlem. Tjänstens huvudnamn har samma namn som den logic app-instansen i Azure AD-klient. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Få åtkomst till resurser med hanterad identitet

När du har skapat en automatiskt genererad hanterad identitet för din logikapp kan du [ge den identitet åtkomsten till andra Azure-resurser](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Du kan sedan använda den identiteten för autentisering, precis som med andra [tjänstens huvudnamn](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Både systemtilldelade hanterad identitet och resursen som du vill tilldela åtkomst måste ha samma Azure-prenumeration.

### <a name="assign-access-to-managed-identity"></a>Tilldela åtkomst till hanterad identitet

Följ dessa steg om du vill ge åtkomst till en annan Azure-resurs för din logikapp systemtilldelade hanterad identitet:

1. Gå till Azure-resursen som du vill tilldela åtkomst för din hanterade identitet i Azure-portalen. 

1. Resursens menyn och välj **åtkomstkontroll (IAM)**, och välj **Lägg till**. 

   ![Lägga till behörigheter](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Under **Lägg till behörigheter**väljer den **rollen** du vill använda för identitet. 

1. I den **tilldela åtkomst till** egenskap, väljer **Azure AD-användare, grupp eller program**, om inte redan är valt.

1. I den **Välj** box, från och med det första tecknet i logikappens namn, ange logikappens namn. När logikappen visas väljer du logikappen.

   ![Välj logikapp med hanterad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. När du är klar väljer du **Spara**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Autentisera med hanterad identitet i logikapp

När du har konfigurerat din logikapp med en automatiskt genererad hanterad identitet och tilldelas åtkomst till resursen som du vill använda för den identiteten, du kan nu använda den identiteten för autentisering. Du kan till exempel använda en HTTP-åtgärd så att logikappen kan skicka en HTTP-begäran eller anrop till den här resursen. 

1. Lägg till i din logikapp den **HTTP** åtgärd. 

1. Ange informationen som krävs för åtgärden, till exempel begäran **metoden** och **URI** platsen för den resursen som du vill anropa.

1. I HTTP-åtgärd väljer **visa avancerade alternativ**. 

1. Från den **autentisering** väljer **hanterad tjänstidentitet**, som sedan visar den **målgrupp** egenskapen för hur du:

   ![Välj ”hanterad tjänstidentitet”](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Fortsätt att skapa logikappen som du vill.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Om du vill inaktivera en automatiskt genererad hanterad identitet i logikappen kan du följa stegen liknar hur du skapade identitet via Azure-portalen, Azure Resource Manager-mallar för distribution eller Azure PowerShell. 

När du tar bort logikappen Azure automatiskt att ta bort din logikapp systemtilldelade identiteter från Azure AD.

### <a name="azure-portal"></a>Azure Portal

1. Öppna logikappen i Logic App Designer.

1. Följ de här stegen: 

   1. På logikappmenyn, under **inställningar**väljer **arbetsflödesinställningarna**. 
   
   1. Under **hanterad tjänstidentitet**, Välj **av** för den **registreras med Azure Active Directory** egenskapen.

   1. När du är klar väljer **spara** i verktygsfältet.

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

