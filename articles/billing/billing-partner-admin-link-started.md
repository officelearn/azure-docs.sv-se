---
title: Länka ett Azure-konto till ett partner-ID | Microsoft Docs
description: Spåra engagemang med Azure-kunder genom att länka ett partner-ID till det användar konto som du använder för att hantera kundens resurser.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706410"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Länka ett partner-ID till dina Azure-konton

Microsoft-partner tillhandahåller tjänster som hjälper kunder att uppnå affärs-och verksamhets mål med Microsoft-produkter. När du agerar på uppdrag av kunden som hanterar, konfigurerar och stöder Azure-tjänster behöver partner användarna åtkomst till kundens miljö. Med partner admin-länken kan partners associera sitt partner nätverks-ID med de autentiseringsuppgifter som används för tjänst leverans.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Få åtkomst från kunden

Innan du länkar ditt partner-ID måste kunden ge dig åtkomst till sina Azure-resurser genom att använda något av följande alternativ:

- **Gäst användare**: Din kund kan lägga till dig som gäst användare och tilldela roller som baseras på rollbaserad åtkomst kontroll (RBAC). Mer information finns i [lägga till gäst användare från en annan katalog](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Katalog konto**: Kunden kan skapa ett användar konto åt dig i en egen katalog och tilldela en RBAC-roll.

- **Tjänstens huvud namn**: Kunden kan lägga till en app eller ett skript från din organisation i katalogen och tilldela en RBAC-roll. Appens eller skriptets identitet kallas för tjänstens huvud namn.

## <a name="link-to-a-partner-id"></a>Länka till ett partner-ID

När du har åtkomst till kundens resurser använder du Azure Portal, PowerShell eller Azure CLI för att länka ditt Microsoft Partner Network-ID (MPN-ID) till ditt användar-ID eller tjänstens huvud namn. Länka partner-ID: t i varje kund klient organisation.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Använd Azure Portal för att länka till ett nytt partner-ID

1. Gå till [länkar till ett partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) i Azure Portal.

2. Logga in på Azure Portal.

3. Ange Microsoft partner-ID. Partner-ID: t är [Microsoft Partner Network](https://partner.microsoft.com/) -ID för din organisation.

   ![Skärm bild som visar länk till ett partner-ID](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Om du vill länka ett partner-ID för en annan kund växlar du till katalogen. Under **växel katalog**väljer du din katalog.

   ![Skärm bild som visar switch Directory](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Använd PowerShell för att länka till ett nytt partner-ID

1. Installera PowerShell-modulen [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

2. Logga in på kundens klient organisation med antingen användar kontot eller tjänstens huvud namn. Mer information finns i [Logga in med PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Länka till det nya partner-ID: t. Partner-ID: t är [Microsoft Partner Network](https://partner.microsoft.com/) -ID för din organisation.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Hämta det länkade partner-ID: t
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkat partner-ID
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Ta bort det länkade partner-ID: t
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Använd Azure CLI för att länka till ett nytt partner-ID
1. Installera Azure CLI-tillägget.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Logga in på kundens klient organisation med antingen användar kontot eller tjänstens huvud namn. Mer information finns i [Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Länka till det nya partner-ID: t. Partner-ID: t är [Microsoft Partner Network](https://partner.microsoft.com/) -ID för din organisation.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Hämta det länkade partner-ID: t
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkat partner-ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Ta bort det länkade partner-ID: t
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Nästa steg

Delta i diskussionen i [Microsoft partner community](https://aka.ms/PALdiscussion) för att ta emot uppdateringar eller skicka feedback.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vem kan länka partner-ID: t?**

Alla användare från partner organisationen som hanterar en kunds Azure-resurser kan länka partner-ID: t till kontot.

**Kan ett partner-ID ändras efter att det är länkat?**

Ja. Ett länkat partner-ID kan ändras, läggas till eller tas bort.

**Vad händer om en användare har ett konto i fler än en kund klient?**

Länken mellan partner-ID: t och kontot görs för varje kund klient. Länka partner-ID: t i varje kund klient organisation.

**Kan andra partners eller kunder redigera eller ta bort länken till partner-ID: t?**

Länken är kopplad till användar konto nivån. Endast du kan redigera eller ta bort länken till partner-ID: t. Kunden och andra partner kan inte ändra länken till partner-ID: t.


**Vilket MPN-ID ska jag använda om mitt företag har flera?**

Partner plats konton och associerade MPN-ID: n ska användas för att länka partner-ID.  Läs mer om [partner konton](https://docs.microsoft.com/partner-center/account-structure)

**Var kan jag hitta påverkan på intäkts rapportering för länkat partner-ID?**

Prestanda rapportering för moln produkter är tillgängligt för partner i Partner Center på [min insikts instrument panel](https://partner.microsoft.com/membership/reports/myinsights). Du måste välja partner admin-länk som partner associerings typ.

**Varför kan jag inte se min kund i rapporterna?**

Du kan inte se kunden i rapporterna på grund av följande orsaker

1. Det länkade användar kontot har inte någon [rollbaserad åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview) till någon kunds Azure-prenumeration eller-resurs.

2. Azure-prenumerationen där användaren har [rollbaserad åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview) åtkomst har ingen användning.

**Fungerar länk partner ID med Azure Stack?**

Ja, du kan länka ditt partner-ID för Azure Stack.
