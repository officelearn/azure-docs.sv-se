---
title: Länka Azure-konto att samarbeta ID | Microsoft Docs
description: Spåra Användarsegmentet med Azure-kunder genom att länka partner-ID till det användarkonto som du hanterar kundens resurser.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a48298668e2297cb95f2a2f16eac6387ff509781
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608720"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Länken partner-ID till dina Azure-konton

Som partner kan spåra du din påverkan mellan kunden Användarsegmentet genom att länka ditt partner-ID till de konton som används för att hantera kundens resurser.

Den här funktionen är tillgänglig i en förhandsversion.

## <a name="get-access-from-your-customer"></a>Hämta från kunden

Innan du länka ditt partner-ID måste kunden får du åtkomst till sina Azure-resurser med hjälp av något av följande alternativ:

- **Gästanvändare:** kunden kan lägga till dig som gästanvändare och tilldela några RBAC-roller. Mer information finns i [lägga till gästanvändare från en annan katalog](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-konto:** kunden kan skapa en ny användare från din organisation i sina kataloger och tilldela någon RBAC-roll.

- **Tjänstens huvudnamn:** kunden kan lägga till en app eller skript från din organisation i sina kataloger och tilldela någon RBAC-roll. Identiteten för den app eller skript kallas tjänstens huvudnamn.

## <a name="link-partner-id"></a>Länka partner-ID

När du har åtkomst till resurser i kundens använda Azure-portalen, PowerShell eller CLI för att länka ditt Microsoft Partner Network-ID (MPN ID) till användar-ID eller tjänstens huvudnamn. Du måste länka partner-ID i varje kund-klient.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Använda Azure-portalen för att länka nya partner-ID

1. Gå till [länk till en partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) i Azure-portalen.

2. Logga in på Azure Portal.

3. Ange Microsoft-partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID för din organisation.

  ![Skärmbild som visar länken partner-ID](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Använd directory switcher om du vill länka partner-ID för en annan kund. Välj din katalog under växeln katalog.

  ![Skärmbild som visar länken partner-ID](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Använd PowerShell för att länka nya partner-ID

1. Installera den [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) PowerShell-modulen.

2. Logga in på kundens klient med det användarkonto eller tjänstens huvudnamn, mer information, se [logga in med Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Länka nya partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID för din organisation.

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

### <a name="use-cli-to-link-new-partner-id"></a>Använda CLI för att länka nya partner-ID
1.  Installera tillägget CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Logga in på kundens klient med det användarkonto eller tjänstens huvudnamn. Mer information finns i [logga in med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Länka nya partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID för din organisation.

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

Anslut diskussionen den [Microsoft Partner Community](https://aka.ms/PALdiscussion) ta emot uppdateringar eller skicka feedback.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vem som kan koppla partner-ID?**

Alla användare från partnerorganisationen som hanterar kundens resurs kan länkas partner-ID till kontot.

**När en partner-ID är länkad kan det ändras?**

Ja, länkade partner-ID kan ändras, lägga till, eller tas bort.

**Vad händer om en användare med ett konto i flera kunden klienter?**

Länken mellan partner-ID och kontot görs för varje kund-klient.  Du måste länka partner-ID i varje kund-klient.

**Kan andra partner eller kund redigera eller ta bort länk till partner-ID?**

Länken är associerade på kontonivå. Du kan bara redigera eller ta bort länken till partner-ID. Kunden och andra partnern kan inte ändra länken till partner-ID. 
