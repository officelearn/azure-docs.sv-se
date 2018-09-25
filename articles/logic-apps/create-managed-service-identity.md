---
title: Komma åt och autentisera utan att logga in – Azure Logic Apps | Microsoft Docs
description: Skapa en hanterad identitet så att logikappen kan autentisera och komma åt resurser i andra Azure Active Directory (Azure AD)-klienter utan att dina autentiseringsuppgifter
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973974"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Komma åt resurser och autentisera sig som hanterade identiteter i Azure Logic Apps

För att komma åt resurser i andra Azure Active Directory (Azure AD)-klienter och verifiera din identitet utan att logga in, kan du skapa en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) som logikappen använder i stället för dina autentiseringsuppgifter. Azure hanterar den här identiteten för dig och hjälper dig att skydda dina autentiseringsuppgifter eftersom du inte behöver ange eller rotera hemligheter. Den här artikeln visar hur du skapar och använder en hanterad identitet för din logikapp. Mer information finns i [Hantera identiteter för Azure-resurser](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Hanterade identiteter för Azure-resurser är Ersättningsnamn för tjänsten tidigare känd som hanterad tjänstidentitet (MSI).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration, eller om du inte har en prenumeration <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Logikappen där du vill använda den hanterade identitet. Om du inte har en logikapp kan se [skapa ditt första logikapparbetsflöde](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Skapa hanterad identitet

Du kan skapa eller aktivera en hanterad identitet för din logikapp via Azure-portalen, Azure Resource Manager-mallar eller Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Om du vill skapa en hanterad identitet för din logikapp via Azure portal, aktivera den **registreras med Azure Active Directory** i inställningar för logikappens arbetsflöde.

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

1. Följ de här stegen: 

   1. På logikappmenyn, under **inställningar**väljer **arbetsflödesinställningarna**. 

   1. Under **hanterad tjänstidentitet** > 
    **registreras med Azure Active Directory**, Välj **på**.

   1. När du är klar väljer **spara** i verktygsfältet.

      ![Aktivera hanterad identitet inställning](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure visar nu de här egenskaperna och värdena för din logikapp hanterad identitet:

      ![GUID för ägar-ID och klient-ID](./media/create-managed-service-identity/principal-tenant-id.png)

      | Egenskap  | Värde | Beskrivning | 
      |----------|-------|-------------| 
      | **Ägar-ID** | <*huvudkonto-ID-GUID*> | En globalt unik identifierare (GUID) som representerar logikappen i en Azure AD-klient | 
      | **Klient-ID** | <*Azure-AD-klient-ID-GUID*> | En globalt unik identifierare (GUID) som representerar den Azure AD-klient där logikappen är nu medlem. Tjänstens huvudnamn har samma namn som den logic app-instansen i Azure AD-klient. | 
      ||| 

### <a name="deployment-template"></a>Distributionsmall

Du kan ställa in Azure Resource Manager-mallar för att automatisera skapandet och distribuera Azure-resurser, till exempel logikappar. Mer information finns i [skapa och distribuera logikappar med Azure Resource Manager-mallar](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

När du skapar en hanterad identitet för din logikapp via en mall till den **identitet** element och **typ** egenskap logic app-arbetsflödesdefinitionen i mallen för distribution. Dessa inställningar anger skapar och hanterar den här identiteten för din logikapp i Azure:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Din logikapp kan till exempel se ut som den här versionen:

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Egenskap  | Värde | Beskrivning | 
|----------|-------|-------------|
| **principalId** | <*huvudkonto-ID-GUID*> | En globalt unik identifierare (GUID) som representerar logikappen i Azure AD-klient | 
| **TenantId** | <*Azure-AD-klient-ID-GUID*> | En globalt unik identifierare (GUID) som representerar den Azure AD-klient där logikappen är nu medlem. Tjänstens huvudnamn har samma namn som den logic app-instansen i Azure AD-klient. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Få åtkomst till resurser med hanterad identitet

När du har skapat en hanterad identitet för din logikapp kan du [ger den identitet åtkomsten till andra resurser](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Du kan sedan använda den hantera identiteten för autentisering, precis som med andra [tjänstens huvudnamn](../active-directory/develop/app-objects-and-service-principals.md). 

Anta exempelvis att du redan har konfigurerat en logikapp med en hanterad identitet som har åtkomst till en annan resurs. Du kan nu lägga till en HTTP-åtgärd så att logikappen kan skicka en HTTP-begäran eller anrop till den här resursen. 

1. Lägg till i din logikapp den **HTTP** åtgärd. 

1. Ange informationen som krävs för åtgärden, till exempel begäran **metoden** och **URI** platsen för den resursen som du vill anropa.

1. I HTTP-åtgärd väljer **visa avancerade alternativ**. 

1. Från den **autentisering** väljer **hanterad tjänstidentitet**, som sedan visar den **målgrupp** egenskapen för hur du:

   ![Välj ”hanterad tjänstidentitet”](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Fortsätt att skapa logikappen som du vill.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Om du vill inaktivera en hanterad identitet i logikappen kan du följa stegen liknar hur du skapade identitet via Azure-portalen, Azure Resource Manager-mallar för distribution eller Azure PowerShell. 

När du tar bort logikappen Azure automatiskt att ta bort din logikapp systemtilldelade identiteter från Azure AD.

### <a name="azure-portal"></a>Azure Portal

1. Öppna logikappen i Logic App Designer.

1. Följ de här stegen: 

   1. På logikappmenyn, under **inställningar**väljer **arbetsflödesinställningarna**. 
   
   1. Under **hanterad tjänstidentitet**, Välj **av** för den **registreras med Azure Active Directory** egenskapen.

   1. När du är klar väljer **spara** i verktygsfältet.

      ![Inaktivera inställningen för hanterad identitet](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Distributionsmall

Om du har skapat den logikapp hanterad identitet med en Distributionsmall av Azure Resource Manager kan du ange den `"identity"` elementets `"type"` egenskap `"None"`. Den här åtgärden tar också bort ägar-ID från Azure AD. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).
