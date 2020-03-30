---
title: Konfigurera SKU:er för ett Azure-programerbjudande | Azure Marketplace
description: Konfigurera SKU:erna för ett Azure-hanterat program och en Azure-lösningsmall.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289081"
---
# <a name="azure-application-skus-tab"></a>Fliken Azure-programSKU:er

I den här artikeln beskrivs hur du använder fliken SKU:er för att skapa SKU:er för ditt Azure-program. 

> [!IMPORTANT]
> Stegen för att konfigurera en SKU är olika för ett hanterat programerbjudande och ett lösningsmallerbjudande. Dessa skillnader dokumenteras i den här artikeln. 

## <a name="configure-azure-application-skus"></a>Konfigurera SKU:er för Azure-program

### <a name="create-a-new-sku"></a>Skapa en ny SKU

Så här skapar du en ny SKU:

1. Välj fliken **SKU:er.**
2. Under SKU väljer du **+ Ny SKU**.

    ![Ny SKU-prompt](./media/azureapp-plus-sku.png)

3. Skriv ett **SKU-ID i**popup-fönstret Nytt SKU . Detta id är begränsat till 50 tecken och får endast bestå av gemener, alfanumeriska tecken, streck eller understreck. SKU-ID:t kan inte sluta med ett streck.
4. SKU-ID:t är synligt för kunder i produktadresser, Resource Manager-mallar (om tillämpligt) och faktureringsrapporter. Du kan inte ändra id-programmet när erbjudandet har publicerats.

### <a name="sku-details-for-a-solution-template"></a>SKU-information för en lösningsmall

Nästa skärminspelning visar formuläret SKU Details för en lösningsmall.

![SKU-informationsformulär för lösningsmall](./media/azureapp-sku-details-solutiontemplate.png)

Ange följande SKU-värden.  De fält som läggs till med en asterisk krävs.

|    Field         |       Beskrivning                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | En titel för SKU. Den här titeln visas i galleriet för det här objektet.   |
| **Sammanfattning\***    | En kort sammanfattning beskrivning av SKU. (Maximal längd är 100 tecken.)  |
| **Beskrivning\*** | En detaljerad beskrivning av SKU. Grundläggande HTML stöds.                 | 
| **SKU-typ\***   | Typ av Azure-programlösning, välj ***Lösningsmall** för det här scenariot. |
| **Molntillgänglighet\*** | Platsen för SKU. Standard är **Public Azure**.  <b/>   **Offentlig Azure** - Appen kan distribueras till kunder i alla offentliga Azure-regioner som har marketplace-integrering.  <b/>   **Azure Government Cloud** - Appen distribueras i Azure Government Cloud. Innan publicering till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)rekommenderar Microsoft utgivare att testa och validera deras lösning fungerar som förväntat i den här miljön. Om du vill iscensätta och testa begär du ett [utvärderingskonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Är det här en privat SKU?\*** | Välj **Ja** om den här SKU:n bara är tillgänglig för en utvald kundgrupp. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government är ett moln för myndigheter med kontrollerad åtkomst för kunder från usa:s federala, statliga, lokala eller stam- och stam- och-partner som är kvalificerade att betjäna dessa enheter.


### <a name="sku-details-for-managed-application"></a>SKU-information för hanterat program

Nästa skärminspelning visar formuläret SKU Details för ett hanterat program.

   ![SKU-informationsformulär för hanterat program](./media/azureapp-sku-details-managedapplication.png)

Konfigurera följande SKU-inställningar. De fält som läggs till med en asterisk krävs.

|    Field         |       Beskrivning                                                            |
|  ---------       |     ---------------                                                          |
|  **Titel\***     | En titel för SKU. Den här titeln visas i galleriet för det här objektet.   |
| **Sammanfattning\***    | En kort sammanfattning beskrivning av SKU. (Maximal längd är 100 tecken.)  |
| **Beskrivning\*** | En detaljerad beskrivning av SKU. Grundläggande HTML stöds.                 | 
| **SKU-typ\***   | Typ av Azure-programlösning, välj ***Hanterat program** för det här scenariot. 
| **Molntillgänglighet\*** | Platsen för SKU. Standard är **Public Azure**.  <b/>   **Offentlig Azure** - Appen kan distribueras till kunder i alla offentliga Azure-regioner som har marketplace-integrering.  <b/>   **Azure Government Cloud** - Appen distribueras i Azure Government Cloud. Innan publicering till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)rekommenderar Microsoft utgivare att testa och validera deras lösning fungerar som förväntat i den här miljön. Om du vill iscensätta och testa begär du ett [utvärderingskonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government är ett moln för myndigheter med kontrollerad åtkomst för kunder från usa:s federala, statliga, lokala eller stam- och stam- och-partner som är kvalificerade att betjäna dessa enheter. |
| **Är det här en privat SKU?\*** | Välj **Ja** om den här SKU:n bara är tillgänglig för en utvald kundgrupp. |
| **Tillgänglighet för land/region\*** | Använd **Välj regioner** om du vill visa en lista över tillgängliga länder/regioner. Kontrollera varje land/region och välj sedan **OK** för att spara dina val.  <b/>   ![Tillgänglighetslista för land och region](./media/azure-app-select-country-region.png)  |
| **Gamla priser\*** | Priset för SKU, i USD per månad. Priserna anges i lokal valuta med hjälp av aktuella valutakurser vid konfiguration. Validera dessa eftersom du i slutändan äger dessa inställningar. Om du vill ange eller visa varje lands/regions pris individuellt exporterar du priskalkylbladet och importerar med anpassad prissättning.  Du måste spara dina prisändringar för att aktivera export/import av prisdata.  |
| **Förenklad valutaprissättning\*** | Priset för SKU, i USD per månad. Detta måste vara samma som gammal prissättning. Mer information finns i [Förenklad valutaprissättning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Paketinformation för lösningsmall

![Paketinformation för lösningsmall](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Ange följande **paketinformationsvärden.**  De fält som läggs till med en asterisk krävs.

- **Version\* ** - Den version av paketet som du kommer att ladda upp. Versionstaggar måste vara av formuläret X.Y.Z, där X, Y och Z är heltal.
- **Paketfil (.zip)\* ** - Det här paketet innehåller följande filer, sparade i en ZIP-fil.
  - **mainTemplate.json\* ** - Distributionsmallfilen som används för att distribuera lösningen/programmet och skapa de resurser som definierats för lösningen. Mer information finns i [Så här skapar du distributionsmallfiler](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition.json\* ** - Den här filen används av Azure-portalen för att generera användargränssnittet för etablering av den här lösningen/programmet. Mer information finns i [Skapa Azure Portal-användargränssnitt för ditt hanterade program](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Skript (om det behövs) - Eventuella ytterligare skript som kan `Microsoft.Compute/virtualMachines/extensions`krävas när mallen körs, till exempel .
  - Kapslade mallar (om det behövs) - Eventuella ytterligare kapslade mallar.

  > [!IMPORTANT] 
  > Det här paketet ska innehålla alla kapslade mallar eller skript som behövs för att etablera det här programmet. Filen mainTemplate.json och filen createUIDefinition.json måste finnas i rotmappen. Mer information om distributionsartefakter finns i [Azure Resource Manager-mallar – metodtipsguide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Paketinformation för hanterade program

   ![Paketinformation för hanterade program](./media/azureapp-sku-pkgdetails-managedapplication.png)

Ange följande paketinformation.  De fält som läggs till med en asterisk krävs.

- **Version\* ** - Den version av paketet som du kommer att ladda upp. Versionstaggar måste vara av formuläret X.Y.Z, där X, Y och Z är heltal.
- **Paketfil (.zip)\* ** - Det här paketet innehåller följande filer, sparade i en ZIP-fil.
  - applianceMainTemplate.json - Distributionsmallfilen som används för att distribuera lösningen/programmet och skapa de resurser som har definierats. Mer information finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json - Den här filen används av Azure-portalen för att generera användargränssnittet för etablering av den här lösningen/programmet. Mer information finns i [Skapa Azure Portal-användargränssnitt för ditt hanterade program](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json - Mallfilen som bara innehåller Microsoft.Solution/appliances-resursen. Mer information finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Observera följande nyckelegenskaper för den här resursen:
    - "typ" - Värdet ska vara "Marketplace" när det gäller Marketplace-managed-program.
    - "ManagedResourceGroupId" - Resursgruppen i kundens prenumeration där alla resurser som definierats i installationenMainTemplate.json kommer att distribueras.
    - "PublisherPackageId"- Strängen som unikt identifierar paketet. Det här värdet måste konstrueras på följande sätt: det är en sammanfogning av [publisherId]. [OfferId]-preview[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Det här paketet ska innehålla alla kapslade mallar eller skript som behövs för att etablera det här programmet. Dessa filer måste finnas i rotmappen: MainTemplate.json, applianceMainTemplate.json och applianceCreateUIDefinition.json.

- **Klient-ID\* ** - Azure Active Directory-klient-ID för din organisation.
- **Vill du aktivera JIT Access? \* ** - Välj **Ja** för att aktivera just-in-time-hanteringsåtkomst för kunddistributioner med det här erbjudandet.

  >[!NOTE] 
  >Om du aktiverar JIT måste du uppdatera filen CreateUiDefinition.json för att stödja JIT-åtkomst.

För ett hanterat program måste du konfigurera Auktoriserings- och principinställningar.


#### <a name="authorization"></a>Auktorisering

Lägg till Azure Active Directory-identifieraren för användare, grupp eller program som du vill bevilja behörighet till den hanterade resursgruppen. Behörigheten som beviljas anges av rolldefinitions-ID. Det kan vara en ägare, deltagare eller en anpassad roll.


#### <a name="policy-settings"></a>Princip Hantering av mobilprogram &gt; Inställningar

Lägg till de principer som den hanterade appen följer. Läs mer om Azure Resource-principer, se [Vad är Azure-princip?](../../../governance/policy/overview.md)

   ![Auktoriserings- och principinställningar för ett hanterat program](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Så här skapar du en ny auktorisering:**

1. Under **Auktorisering**väljer du **+ Ny auktorisering**.
2. För **huvud-ID**skriver du Azure Active Directory Identifier för användare, grupp eller program som du vill bevilja behörighet till den hanterade resursgruppen. Behörigheten som beviljas anges av rolldefinitionen.
3. För **rolldefinition**väljer du ett av följande alternativ i listrutan: Ägare eller Deltagare. Mer information finns i [Inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Flera auktoriseringar kan läggas till. Vi rekommenderar dock att du skapar en Active Directory-användargrupp och anger dess ID i "PrincipalId". Detta gör det möjligt att lägga till fler användare i användargruppen utan att behöva uppdatera SKU.

**Så här skapar du en ny princip:**

1. Välj **+ Ny princip**under **Principinställningar**.
2. För **principnamn**anger du ett namn på principen. Namnets maximala längd är 50 tecken.
3. För **principer**väljer du ett av alternativen i listrutan. Välj den princip som dataprovidern vill aktivera när programmet använder data. Mer information finns i [Azure Policy Samples](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Principinställningar för ett hanterat program](./media/azureapp-sku-policy-settings.png)

4. För **Princip SKU**väljer du Ledig eller Standard som princip-SKU-typ. Standard-SKU krävs för granskningsprinciper.


## <a name="next-steps"></a>Nästa steg

Du kommer att beskriva dina erbjudande- och leveransmarknadstillgångar ytterligare på [fliken Marketplace](./cpp-marketplace-tab.md). 
