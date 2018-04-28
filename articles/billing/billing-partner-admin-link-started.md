---
title: Länka Azure-konto att samarbeta ID | Microsoft Docs
description: Spåra Användarsegmentet med Azure-kunder genom att länka partner-ID till det användarkonto som du hanterar kundens resurser.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d1f6515c63e789643c285218aa121f1182902e0f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Länken partner-ID till dina Azure-konton 
Som partner kan spåra du din påverkan mellan kunden Användarsegmentet genom att länka ditt partner-ID till de konton som används för att hantera kundens resurser.

Den här funktionen är tillgänglig i en förhandsversion. 

## <a name="get-access-from-your-customer"></a>Hämta från kunden 
Innan du länka ditt partner-ID måste kunden får du åtkomst till sina Azure-resurser med hjälp av något av följande alternativ:

- **Gästanvändare:** kunden kan lägga till dig som gästanvändare och tilldela några RBAC-roller. Mer information finns i [lägga till gästanvändare från en annan katalog](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Directory-konto:** kunden kan skapa en ny användare från din organisation i sina kataloger och tilldela någon RBAC-roll. 

- **Tjänstens huvudnamn:** kunden kan lägga till en app eller skript från din organisation i sina kataloger och tilldela någon RBAC-roll. Identiteten för den app eller skript kallas tjänstens huvudnamn.

## <a name="link-partner-id"></a>Länken partner-ID
När du har åtkomst till kundens resurser, Använd PowerShell eller CLI för att länka ditt Microsoft Partner Network-ID (MPN ID) till ditt användar-ID eller tjänstens huvudnamn. Du måste länka partner-ID för varje kund-klient. 

### <a name="use-powershell-to-link-new-partner-id"></a>Använd PowerShell för att länka nya partner-ID

1. Installera den [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) PowerShell-modulen.

2. Logga in på kundens klient med det användarkonto eller tjänstens huvudnamn, mer information, se [logga in med Powershell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
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

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Logga in på kundens klient med det användarkonto eller tjänstens huvudnamn. Mer information finns i [logga in med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Länka nya partner-ID. Partner-ID är den [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID för din organisation.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Hämta länkade partner-ID
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Uppdatera länkade partner-ID
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Ta bort länkade partner-ID
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vem som kan koppla partner-ID?**

Alla användare från partnerorganisationen som hanterar kundens resurs kan länkas partner-ID till kontot.
  

**När en partner-ID är länkad kan det ändras?**

Ja, länkade partner-ID kan ändras, lägga till, eller tas bort.

**Vad händer om en användare med ett konto i flera kunden klienter?**

Länken mellan partner-ID och kontot görs för varje kund-klient.  Du måste länka partner-ID i varje kund-klient.

**Kan andra partner eller kund redigera eller ta bort länk till partner-ID?**

Länken är associerade på kontonivå. Du kan bara redigera eller ta bort länken till partner-ID. Kunden och andra partnern kan inte ändra länken till partner-ID. 


