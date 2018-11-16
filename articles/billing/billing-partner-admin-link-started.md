---
title: Länka Azure-konto och partner ID | Microsoft Docs
description: Spåra arbete med Azure-kunder genom att länka partner-ID till det användarkonto som används för att hantera kundens resurser.
services: billing
author: dhirajgandhi
ms.author: cwatson
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 13748bf23ee70bfa77e15e7112b450637a231742
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710723"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Länka partner-ID till dina Azure-konton

Som partner kan spåra du din inverkan mellan dina kundrelationer genom att länka ditt partner-ID till de konton som används för att hantera kundens resurser.

Den här funktionen är tillgänglig i en offentlig förhandsversion.

## <a name="get-access-from-your-customer"></a>Hämta från kunden

Innan du länka ditt partner-ID, måste kunden får du åtkomst till sina Azure-resurser med hjälp av något av följande alternativ:

- **Gästanvändaren:** kunden kan lägga till dig som en gästanvändare och tilldela alla RBAC-roller. Mer information finns i [lägga till gästanvändare från en annan katalog](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-konto:** kunden kan skapa en ny användare från din organisation i sin katalog och tilldela en RBAC-roll.

- **Tjänstens huvudnamn:** kunden kan lägga till en app eller ditt skript från din organisation i sin katalog och tilldela en RBAC-roll. Identiteten för den app eller ditt skript kallas för tjänstens huvudnamn.

## <a name="link-partner-id"></a>Länka partner-ID

När du har åtkomst till kundens resurser, Använd Azure portal, PowerShell eller CLI för att länka ditt Microsoft Partner Network-ID (MPN-ID) till dina användar-ID eller tjänstens huvudnamn. Du måste länka partner-ID i varje kundklient.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Använd Azure-portalen för att länka ny partner-ID

1. Gå till [länk till ett partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) i Azure-portalen.

2. Logga in på Azure Portal.

3. Ange Microsoft partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID: T för din organisation.

  ![Skärmbild som visar länka partner-ID](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Använd katalogväxlaren för att länka partner-ID för en annan kund. Under växla katalog väljer du din katalog.

  ![Skärmbild som visar länka partner-ID](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Använd PowerShell för att länka ny partner-ID

1. Installera den [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell-modulen.

2. Logga in till kundens klient antingen med det användarkonto eller tjänstens huvudnamn, för mer information, se [logga in med Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Länka ny partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID: T för din organisation.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Hämta länkade partner-ID
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkade partner-ID
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Ta bort länkade partner-ID
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Använd CLI för att länka ny partner-ID
1.  Installera CLI-tillägg.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Logga in på kundens klient med det användarkonto eller tjänstens huvudnamn. Mer information finns i [logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Länka ny partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID: T för din organisation.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Hämta länkade partner-ID
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkade partner-ID
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Ta bort länkade partner-ID
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>Nästa steg

Gå med i diskussionen den [Microsoft Partner Community](https://aka.ms/PALdiscussion) ta emot uppdateringar eller skicka feedback.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vem kan länka partner-ID?**

Alla användare från partnerorganisationen som hanterar kundens Azure-resurser kan länka partner-ID till kontot.

**När ett partner-ID är länkad kan den ändras?**

Ja, länkade partner-ID kan ändras, har lagts till, eller tas bort.

**Vad händer om en användare har ett konto i flera kund klienter?**

Länken mellan partner-ID och konton som görs för varje kundklient.  Du måste länka partner-ID i varje kundklient.

**Kan andra partner eller kund redigera eller ta bort länken till partner-ID?**

Länken är associerade på nivån för kontot. Endast kan du redigera eller ta bort länken till partner-ID. Kunden och andra partner kan inte ändra länken till partner-ID. 
