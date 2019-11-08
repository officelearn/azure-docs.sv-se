---
title: 'Konfigurera SKU: er för ett erbjudande för Azure-program | Azure Marketplace'
description: 'Så här konfigurerar du SKU: er för ett Azure-hanterat program och en Azure-lösnings mall.'
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 29b69499b708726b10947bd3202d3a52893f5c90
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826181"
---
# <a name="azure-application-skus-tab"></a>Fliken för Azure program-SKU: er

Den här artikeln beskriver hur du använder fliken SKU: er för att skapa SKU: er för ditt Azure-program. 

> [!IMPORTANT]
> Stegen för att konfigurera en SKU är olika för ett hanterat program erbjudande och ett erbjudande för lösnings mal len. Dessa skillnader beskrivs i den här artikeln. 

## <a name="configure-azure-application-skus"></a>Konfigurera Azure Application SKU: er

### <a name="create-a-new-sku"></a>Skapa en ny SKU

Använd de här stegen för att skapa en ny SKU:

1. Välj fliken **SKU: er** .
2. Under SKU: er väljer du **+ ny SKU**.

    ![Ny SKU-prompt](./media/azureapp-plus-sku.png)

3. I popup-fönstret ny SKU skriver du ett **SKU-ID**. Detta ID är begränsat till 50 tecken och får bara bestå av gemener, alfanumeriska tecken, bindestreck eller under streck. SKU-ID: t får inte sluta med ett bindestreck.
4. SKU-ID: t är synligt för kunder i produkt-URL: er, Resource Manager-mallar (om tillämpligt) och fakturerings rapporter. Du kan inte ändra det här ID: t när erbjudandet har publicerats.

### <a name="sku-details-for-a-solution-template"></a>SKU-information för en lösnings mall

I nästa skärm bild visas formuläret SKU-information för en lösnings mall.

![Formuläret SKU-information för lösnings mal len](./media/azureapp-sku-details-solutiontemplate.png)

Ange följande SKU-värden.  Fälten som läggs till med en asterisk måste anges.

|    Fält         |       Beskrivning                                                            |
|  ---------       |     ---------------                                                          |
|  **Rubrik\***     | En rubrik för SKU: n. Den här rubriken visas i galleriet för det här objektet.   |
| **Sammanfattning\***    | En kort beskrivning av SKU: n. (Den maximala längden är 100 tecken.)  |
| **Beskrivning\*** | En detaljerad beskrivning av SKU: n. Grundläggande HTML stöds.                 | 
| **SKU-typ\***   | Typ av Azure-programlösning, Välj ***lösnings mal len** för det här scenariot. |
| **Moln tillgänglighets\*** | Platsen för SKU: n. Standardvärdet är **offentlig Azure**.  <b/>**offentliga Azure** -app kan distribueras till kunder i alla offentliga Azure-regioner som har Marketplace-integration.  <b/>**Azure Government Cloud** -app distribueras i Azure Government molnet. Innan du publicerar till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)rekommenderar Microsoft utgivare att testa och verifiera att lösningen fungerar som förväntat i den här miljön. Om du vill mellanlagra och testa begär du ett [utvärderings konto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Är detta en privat SKU?\*** | Välj **Ja** om den här SKU: n endast är tillgänglig för en Välj grupp med kunder. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government är ett moln i en community med kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad och partner berättigade att betjäna dessa entiteter.


### <a name="sku-details-for-managed-application"></a>SKU-information för hanterat program

I nästa skärm bild visas formuläret SKU-information för ett hanterat program.

   ![Formuläret SKU-information för hanterat program](./media/azureapp-sku-details-managedapplication.png)

Konfigurera följande SKU-inställningar. Fälten som läggs till med en asterisk måste anges.

|    Fält         |       Beskrivning                                                            |
|  ---------       |     ---------------                                                          |
|  **Rubrik\***     | En rubrik för SKU: n. Den här rubriken visas i galleriet för det här objektet.   |
| **Sammanfattning\***    | En kort beskrivning av SKU: n. (Den maximala längden är 100 tecken.)  |
| **Beskrivning\*** | En detaljerad beskrivning av SKU: n. Grundläggande HTML stöds.                 | 
| **SKU-typ\***   | Typ av Azure-programlösning väljer du ***hanterat program** för det här scenariot. 
| **Moln tillgänglighets\*** | Platsen för SKU: n. Standardvärdet är **offentlig Azure**.  <b/>**offentliga Azure** -app kan distribueras till kunder i alla offentliga Azure-regioner som har Marketplace-integration.  <b/>**Azure Government Cloud** -app distribueras i Azure Government molnet. Innan du publicerar till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)rekommenderar Microsoft utgivare att testa och verifiera att lösningen fungerar som förväntat i den här miljön. Om du vill mellanlagra och testa begär du ett [utvärderings konto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government är ett moln i en community med kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad och partner berättigade att betjäna dessa entiteter. |
| **Är detta en privat SKU?\*** | Välj **Ja** om den här SKU: n endast är tillgänglig för en Välj grupp med kunder. |
| **Tillgänglighet för land/region\*** | Använd **Välj regioner** för att visa en lista över länder/regioner som är tillgängliga. Markera varje land/region och välj sedan **OK** för att spara dina plockningar.  <b/>![land-och regions tillgänglighets lista](./media/azure-app-select-country-region.png)  |
| **Tidigare prissättnings\*** | Priset för SKU: n i USD per månad. Priserna anges i lokal valuta med aktuella växelkurser vid konfigurationen. Verifiera dessa inställningar eftersom du slutligen äger de här inställningarna. Om du vill ange eller Visa varje lands/regions pris individuellt exporterar du pris kalkyl bladet och importerar det med anpassad prissättning.  Du måste spara pris ändringarna för att tillåta export/import av pris data.  |
| **Pris\* för förenklad valuta** | Priset för SKU: n i USD per månad. Det måste vara samma som för den gamla prissättningen. Mer information finns i [pris för förenklad valuta](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Paket information för lösnings mal len

   ![Paket information för lösnings mal len](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Ange följande **paket information** värden.  Fälten som läggs till med en asterisk måste anges.

- **Version\*** – versionen av paketet som du ska ladda upp. Versions Taggar måste vara av formatet X. Y. Z, där X, Y och Z är heltal.
- **Paket fil (. zip)\*** – det här paketet innehåller följande filer, sparade i en. zip-fil.
  - MainTemplate. JSON – distributions mal Lav bildfilen som används för att distribuera lösningen/programmet och skapa de resurser som definierats för lösningen. Mer information finns i [så här skapar du mallar för distribuering av filer](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition. JSON – den här filen används av Azure Portal för att generera användar gränssnittet för etablering av den här lösningen/programmet. Mer information finns i [skapa Azure Portal användar gränssnitt för ditt hanterade program](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Det här paketet bör innehålla eventuella kapslade mallar eller skript som krävs för att etablera det här programmet. MainTemplate. JSON-filen och createUIDefinition. JSON-filen måste finnas i rotmappen.


### <a name="package-details-for-managed-application"></a>Paket information för hanterat program

   ![Paket information för hanterat program](./media/azureapp-sku-pkgdetails-managedapplication.png)

Ange följande paket information.  Fälten som läggs till med en asterisk måste anges.

- **Version\*** – versionen av paketet som du ska ladda upp. Versions Taggar måste vara av formatet X. Y. Z, där X, Y och Z är heltal.
- **Paket fil (. zip)\*** – det här paketet innehåller följande filer, sparade i en. zip-fil.
  - applianceMainTemplate. JSON – distributions mal Lav bildfilen som används för att distribuera lösningen/programmet och skapa de resurser som definieras. Mer information finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. JSON – den här filen används av Azure Portal för att generera användar gränssnittet för etablering av den här lösningen/programmet. Mer information finns i [skapa Azure Portal användar gränssnitt för ditt hanterade program](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate. JSON – mallfilen som bara innehåller Microsoft. Solution/apparaturs-resursen. Mer information finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Observera följande nyckel egenskaper för resursen:
    - "Natura"-värdet ska vara "Marketplace" när det gäller Marketplace-hanterat program.
    - "ManagedResourceGroupId" – resurs gruppen i kundens prenumeration där alla de resurser som definierats i applianceMainTemplate. JSON kommer att distribueras.
    - "PublisherPackageId" – strängen som unikt identifierar paketet. Det här värdet måste konstrueras på följande sätt: det är en sammanfogning av [publisherId]. [OfferId]-för hands version [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Det här paketet bör innehålla eventuella kapslade mallar eller skript som krävs för att etablera det här programmet. Filerna måste finnas i rotmappen: MainTemplate. JSON, applianceMainTemplate. JSON och applianceCreateUIDefinition. JSON.

- **Klient-id\*** -Azure Active Directory klient organisationens ID.
- **Aktivera JIT-åtkomst?\*** – Välj **Ja** om du vill aktivera hantering av just-in-Time-åtkomst för kund distributioner med det här erbjudandet.

  >[!NOTE] 
  >Om du aktiverar JIT måste du uppdatera filen CreateUiDefinition. JSON för att stödja JIT-åtkomst.

För ett hanterat program måste du konfigurera inställningar för auktorisering och principer.


#### <a name="authorization"></a>Auktorisering

Lägg till Azure Active Directory identifierare för användaren, gruppen eller programmet som du vill ge behörighet till den hanterade resurs gruppen. Behörigheten som beviljas anges av roll Definitions-ID: t. Det kan vara ägare, deltagare eller en anpassad roll.


#### <a name="policy-settings"></a>Princip inställningar

Lägg till de principer som den hanterade appen följer. Läs mer om Azure Resource policies, se [Vad är Azure policy?](../../../governance/policy/overview.md)

   ![Inställningar för auktorisering och principer för ett hanterat program](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Så här skapar du en ny auktorisering:**

1. Under **auktorisering**väljer du **+ ny auktorisering**.
2. Som **huvud-ID**anger du Azure Active Directory identifieraren för den användare, grupp eller det program som du vill ge behörighet till den hanterade resurs gruppen. Behörigheten som beviljas anges av roll definitionen.
3. För **roll definition**väljer du ett av följande alternativ i list rutan: ägare eller deltagare. Mer information finns i [Inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Du kan lägga till flera auktoriseringar. Vi rekommenderar dock att du skapar en Active Directory användar grupp och anger dess ID i "PrincipalId". Detta gör att du kan lägga till fler användare i användar gruppen utan att behöva uppdatera SKU: n.

**Så här skapar du en ny princip:**

1. Under **princip inställningar**väljer du **+ ny princip**.
2. Ange ett namn för principen för **princip namn**. Namnet får bestå av högst 50 tecken.
3. För **principer**väljer du ett av alternativen i list rutan. Välj den princip som data leverantören vill aktivera när programmet använder data. Mer information finns i [Azure policy exempel](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Princip inställningar för ett hanterat program](./media/azureapp-sku-policy-settings.png)

4. För **princip-SKU**väljer du ledig eller standard som princip-SKU-typ. Standard-SKU: n krävs för gransknings principer.


## <a name="next-steps"></a>Nästa steg

Du lär dig mer om ditt erbjudande och levererar marknadsförings till gångar på [fliken Marketplace](./cpp-marketplace-tab.md). 
