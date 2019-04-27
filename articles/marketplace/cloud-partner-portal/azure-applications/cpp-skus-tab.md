---
title: 'Konfigurera SKU: er för ett erbjudande för Azure-program | Microsoft Docs'
description: 'Så här konfigurerar du SKU: er för en Azure-hanterade program och en mall för Azure-lösning.'
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5d6ec0197699f603c79f414e015cdebcde6b9f60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745118"
---
# <a name="azure-application-skus-tab"></a>Fliken för Azure application SKU: er

Den här artikeln beskriver hur du använder fliken SKU: er för att skapa SKU: er för dina Azure-program. 

> [!IMPORTANT]
> Stegen för att konfigurera en SKU är olika för ett erbjudande för hanterade program och en lösning mall-erbjudandet. Dessa skillnader finns dokumenterade i den här artikeln. 

## <a name="configure-azure-application-skus"></a>Konfigurera SKU: er för Azure-program

### <a name="create-a-new-sku"></a>Skapa en ny SKU

Följ dessa steg för att skapa en ny SKU:

1. Välj den **SKU: er** fliken.
2. Välj under SKU: er, **+ nya SKU: N**.

    ![Nya SKU-fråga](./media/azureapp-plus-sku.png)

3. I det nya SKU: N popup-fönstret, Skriv en **SKU ID**. Detta id är begränsad till 50 tecken och måste bestå av gemener, alfanumeriska tecken, bindestreck och understreck. SKU-ID får inte sluta med ett bindestreck.
4. SKU-ID är synlig för kunder i produkten URL: er, Resource Manager-mallar (om tillämpligt) och fakturering rapporter. Du kan inte ändra detta id när erbjudandet är publicerat.

### <a name="sku-details-for-a-solution-template"></a>SKU-information för en Lösningsmall

Anger du följande inställningar för SKU:

- **Rubrik** – en rubrik för SKU: N. Den här rubriken visas i galleriet för det här objektet.
- **Sammanfattning av** – en kort sammanfattande beskrivning av SKU: N. (Maximal längd är 100 tecken.)
- **Beskrivning av** – en detaljerad beskrivning av SKU: N.
- **SKU-typen** – en listruta med följande värden: ”Mall” och ”hanterade program”. Det här scenariot väljer **lösningsmallen**.
- **Tillgänglighet i molnet** -platsen för SKU: N. Standardvärdet är **offentliga Azure**.
Offentliga Azure – den här virtuella datorn kommer att kunna distribueras till kunder i alla offentliga Azure-regioner som har Marketplace-integration.
- **Azure Government-molnet** – den här virtuella datorn ska distribueras i Azure Government-molnet. Innan publicering till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft rekommenderar att du utgivare testa och validera sin lösning fungerar som förväntat i miljön. För att mellanlagra och testa begär en [utvärderingskonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >Microsoft Azure Government är ett myndighetscommunitymoln med kontrollerad åtkomst för kunder från amerikanska federala myndigheter, tillstånd, lokal eller stambaserad och-partner kan hantera dessa enheter.

- **Är detta en privat SKU?** – Välj Ja om denna SKU är endast tillgänglig för en viss grupp med kunder.

    ![SKU: N i formuläret för lösningsmallen](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>SKU-information för hanterat program

Nästa skärmdump visar formuläret SKU-information för ett hanterat program.

   ![Formuläret om SKU för hanterat program](./media/azureapp-sku-details-managedapplication.png)

Konfigurera följande inställningar för SKU:

- **Rubrik** – en rubrik för SKU: N. Den här rubriken visas i galleriet för det här objektet.
- **Sammanfattning av** – en kort sammanfattande beskrivning av SKU: N. (Maximal längd är 100 tecken.)
- **Beskrivning av** – en detaljerad beskrivning av SKU: N.
- **SKU-typen** – en listruta med följande värden: ”Mall” och ”hanterade program”. Det här scenariot väljer **Managed Application**.
- **Tillgänglighet i molnet** -platsen för SKU: N. Standardvärdet är **offentliga Azure**.
- **Offentliga Azure** – den här virtuella datorn kommer att kunna distribueras till kunder i alla offentliga Azure-regioner som har Marketplace-integration.
- **Azure Government-molnet** – den här virtuella datorn ska distribueras i Azure Government-molnet. Innan publicering till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft rekommenderar att du utgivare testa och validera sin lösning fungerar som förväntat i miljön. För att mellanlagra och testa begär en [utvärderingskonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >Microsoft Azure Government är ett myndighetscommunitymoln med kontrollerad åtkomst för kunder från amerikanska federala myndigheter, tillstånd, lokal eller stambaserad och-partner kan hantera dessa enheter.

- **Är detta en privat SKU?** – Välj Ja om denna SKU är endast tillgänglig för en viss grupp med kunder.
- **Land/Region tillgänglighet** – Använd **Välj regioner** att visa en lista över länder/regioner som är tillgängliga. Kontrollera varje land/region och välj sedan **OK** att spara din plockningar. 

   ![Lista över land och region tillgänglighet](./media/azure-app-select-country-region.png)

- **Gamla priser** – ange priset för SKU: N i USD per månad. Priserna som anges i lokal valuta med aktuella växelkurser på konfiguration. Verifiera dessa eftersom du själv hantera de här inställningarna. Om du vill ställa in eller visa priset för varje land/region individuellt, exportera prissättning kalkylbladet och importera med anpassad prissättning.

  >[!NOTE]
  >Spara dina ändringar i prissättning för att aktivera under exporten/importen av prisnivå data.

- **Förenklad valuta priser** -ange priset för SKU: N i USD per månad. Detta måste vara samma som gamla priser. Mer information finns i [förenklad valuta priser](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer).

### <a name="package-details-for-solution-template"></a>Paketinformation för lösningsmallen

Ange följande information i paketet:

- **Version** -versionen av paketet som du överför. Version taggar måste vara i formatet X.Y.Z, där X, Y och Z är heltal.
- **Paketfil (.zip)** – det här paketet innehåller följande filer sparas i en .zip-fil.
  - MainTemplate.json - distribution mallfilen som används för att distribuera lösningen/program och skapa de resurser som definierats för lösningen. Mer information finns i [hur du skapar distributionen mallfiler](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json - den här filen används av Azure-portalen för att generera användargränssnittet för att etablera den här lösningen/application. Mer information finns i [skapa Azure portal användargränssnittet för det hanterade programmet](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Det här paketet innehåller alla kapslade mallar eller skript som behövs för att etablera det här programmet. MainTemplate.json fil- och createUIDefinition.json filen måste vara i rotmappen.

   ![Paketinformation för lösningsmallen](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>Paketinformation för hanterat program

Ange följande information i paketet:

- **Version** -versionen av paketet som du överför. Version taggar måste vara i formatet X.Y.Z, där X, Y och Z är heltal.
- **Paketfil (.zip)** – det här paketet innehåller följande filer sparas i en .zip-fil.
  - applianceMainTemplate.json - distribution mallfilen som används för att distribuera lösningen/program och skapa de resurser som har definierats. Mer information finns i [ Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json - den här filen används av Azure-portalen för att generera användargränssnittet för att etablera den här lösningen/application. Mer information finns i [skapa Azure portal användargränssnittet för det hanterade programmet](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json - mallfilen som innehåller endast Microsoft.Solution/appliances resursen. Mer information finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Observera följande viktiga egenskaper för den här resursen:
    - ”typ” - värdet ska vara ”Marketplace” när det gäller Marketplace-hanterade program.
    - ”ManagedResourceGroupId” - resursgruppen på kundens prenumeration där alla resurser som definierats i applianceMainTemplate.json ska distribueras.
    - ”Formatet för PublisherPackageId” – den sträng som unikt identifierar paketet. Det här värdet måste således: det är en sammansättning av [publisherId]. [OfferId]-förhandsversion [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Det här paketet innehåller alla kapslade mallar eller skript som behövs för att etablera det här programmet. De här filerna måste vara i rotmappen:  MainTemplate.json, applianceMainTemplate.json, and applianceCreateUIDefinition.json.

- **Klient-Id** -Azure Active Directory-klient-id för din organisation.
- **Aktivera JIT-åtkomst?** – Välj **Ja** att aktivera Just-In-Time-åtkomst för distributioner med det här erbjudandet.

  >[!NOTE] 
  >Om du aktiverar JIT måste du uppdatera filen CreateUiDefinition.json för att stödja JIT-åtkomst.

   ![Paketinformation för hanterat program](./media/azureapp-sku-pkgdetails-managedapplication.png)

För ett hanterat program måste du konfigurera auktorisering och inställningar.

#### <a name="authorization"></a>Auktorisering

Lägg till Azure Active Directory identifierare för användare, grupp eller program som du vill bevilja behörighet till den hanterade resursgruppen. Den behörighet som beviljas indikeras av rolldefinitionen Id. Det kan vara ägare, deltagare eller en anpassad roll.

#### <a name="policy-settings"></a>Principinställningar

Lägg till de principer som hanterad App uppfyller. Läs mer om Azure resursprinciper, se [vad är Azure Policy?](../../../governance/policy/overview.md)


   ![Inställningar för auktorisering och principen för ett hanterat program](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Skapa ett nytt tillstånd:**

1. Under **auktorisering**väljer **+ ny auktorisering**.
2. För **huvudkonto-Id**, skriver du det Azure Active Directory Identifier för användaren, gruppen eller programmet som du vill bevilja behörighet till den hanterade resursgruppen. Den behörighet som beviljas anges av rolldefinitionen.
3. För **rolldefinition**, Välj något av följande alternativ i listrutan:  Ägare eller deltagare. Mer information finns i [inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Flera godkännanden kan läggas till. Men rekommenderar vi att du skapar en Active Directory-användargrupp och ange dess ID i ”PrincipalId”. Detta kommer att lägga till fler användare i användargruppen utan att behöva uppdatera SKU: N.

**Skapa en ny princip:**

1. Under **principinställningar**väljer **+ ny princip**.
2. För **principnamn**, ange ett namn för principen. Den maximala längden på namnet är 50 tecken.
3. För **principer**, Välj något av alternativen i listrutan. Välj den princip som dataleverantören vill aktiveras när programmet använder dessa data. Mer information finns i den [Azure Policy Samples](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Principinställningar för ett hanterat program](./media/azureapp-sku-policy-settings.png)

4. För **Programprincipens SKU**, Välj kostnadsfri eller Standard som principen SKU-typen. Standard-SKU måste anges för granskningsprinciper.

## <a name="next-steps"></a>Nästa steg

[fliken Marketplace](./cpp-marketplace-tab.md)
