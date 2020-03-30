---
title: Koppla ett Azure-konto till ett partner-ID
description: Spåra interaktioner med Azure-kunder genom att länka ett partner-ID till det användarkonto som du använder för att hantera kundens resurser.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 02/13/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: c3f5dd4d1b67f5b1079a56d5c3428d286fd9d77f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204808"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Länka ett partner-ID till dina Azure-konton

Microsoft-partner tillhandahåller tjänster som hjälper kunder att uppnå affärs- och verksamhetsmål med produkter från Microsoft. När du på uppdrag av kunden hanterar, konfigurerar och ger support för Azure-tjänster behöver partneranvändarna åtkomst till kundens miljö. Med Partneradministratörslänk (PAL) kan partner associera sina partnernätverks-ID:n med autentiseringsuppgifterna som används för tjänstleverans.

PAL gör det möjligt för Microsoft att identifiera och uppmärksamma partner som skapar kundframgång med Azure. Microsoft kan påverka och skapa Azure-relaterade intäkter för din organisation baserat på kontots behörigheter (RBAC-roll) och omfång (prenumeration, resursgrupp och resurs).

## <a name="get-access-from-your-customer"></a>Få åtkomst från kunden

Innan du länkar ditt partner-ID måste kunden ge dig åtkomst till sina Azure-resurser med hjälp av något av följande alternativ:

- **Gästanvändare**: Din kund kan lägga till dig som gästanvändare och tilldela valfria roller för rollbaserad åtkomstkontroll (RBAC). Mer information finns i [Lägga till gästanvändare från en annan katalog](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Katalogkonto**: Kunden kan skapa ett användarkonto åt dig i en egen katalog och tilldela valfri roll för rollbaserad åtkomst.

- **Tjänstens huvudnamn**: Kunden kan lägga till en app eller ett skript från din organisation i katalogen och tilldela en roll för rollbaserad åtkomstkontroll. Appens eller skriptets identitet kallas för tjänstens huvudnamn.

- **Azure Lighthouse**: Kunden kan delegera en prenumeration (eller resursgrupp) så att användarna kan arbeta med den inifrån din klientorganisation. Mer information finns i [Azure-delegerad resurshantering](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).

## <a name="link-to-a-partner-id"></a>Länka till ett partner-ID

När du har åtkomst till kundens resurser använder du Azure-portalen, PowerShell eller Azure CLI för att länka ditt MPN-ID (Microsoft Partner Network) till ditt användar-ID eller tjänstens huvudnamn. Länka partner-ID:t i alla kundens klientorganisationer.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Använd Azure-portalen för att länka till ett nytt partner-ID

1. Gå till [Länka till ett partner-ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) på Azure-portalen.

2. Logga in på Azure Portal.

3. Ange Microsofts partner-ID. Partner-ID:t är ID:t för [Microsoft Partner Network](https://partner.microsoft.com/) för din organisation.

   ![Skärmbild som visar Länka till ett partner-ID](./media/link-partner-id/link-partner-id01.png)

4. Om du vill länka ett partner-ID för en annan kund byter du katalog. Under **Växla katalog** väljer du din katalog.

   ![Skärmbild som visar Växla katalog](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Använd PowerShell för att länka till ett nytt partner-ID

1. Installera PowerShell-modulen [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Logga in i kundens klientorganisation med användarkontot eller tjänstens huvudnamn. Mer information finns i [Logga in med PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Länka till det nya partner-ID:t. Partner-ID:t är ID:t för [Microsoft Partner Network](https://partner.microsoft.com/) för din organisation.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Hämta det länkade partner-ID:t
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera det länkade partner-ID:t
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Ta bort det länkade partner-ID:t
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Använd Azure CLI för att länka till ett nytt partner-ID
1. Installera Azure CLI-tillägget.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Logga in i kundens klientorganisation med användarkontot eller tjänstens huvudnamn. Mer information finns i [Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Länka till det nya partner-ID:t. Partner-ID:t är ID:t för [Microsoft Partner Network](https://partner.microsoft.com/) för din organisation.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Hämta det länkade partner-ID:t
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Uppdatera det länkade partner-ID:t
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Ta bort det länkade partner-ID:t
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Nästa steg

Delta i diskussionen i [Microsofts community för partner](https://aka.ms/PALdiscussion) och få uppdateringar eller skicka feedback.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vem kan länka partner-ID:t?**

Alla användare från partnerorganisationen som hanterar en kunds Azure-resurser kan länka partner-ID:t till kontot.

**Kan ett partner-ID ändras efter att det har länkats?**

Ja. Ett länkat partner-ID kan ändras, läggas till eller tas bort.

**Vad händer om en användare har ett konto i flera klientorganisationer för en kund?**

Länken mellan partner-ID:t och kontot skapas för alla kundens klientorganisationer. Länka partner-ID:t i alla kundens klientorganisationer.

**Kan andra partner eller kunder redigera eller ta bort länken till partner-ID:t?**

Länken är kopplad på användarkontonivå. Du är den enda som kan redigera eller ta bort länken till partner-ID:t. Kunden och andra partner kan inte ändra länken till partner-ID:t.


**Vilket MPN-ID ska jag använda om mitt företag har flera?**

Partnerplatskonton och associerade MPN-ID:n bör användas för att länka partner-ID:n.  Läs mer om [partnerkonton](https://docs.microsoft.com/partner-center/account-structure)

**Var kan jag se hur intäkterna påverkas av länkade partner-ID:n?**

Prestandarapportering för molnprodukter är tillgängligt för partner i Partner Center på [instrumentpanelen My Insights](https://partner.microsoft.com/membership/reports/myinsights) (Mina insikter). Du måste välja Partneradministratörslänk som typ av partnerassociation.

**Varför kan jag inte se min kund i rapporterna?**

Om du inte ser kunden i rapporterna kan det bero på något av följande

1. Det länkade användarkontot har inte [rollbaserad åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview) till någon Azure-prenumeration eller Azure-resurs för en kund.

2. Ingen användning har registrerats i Azure-prenumerationen där användaren har [rollbaserad åtkomst](https://docs.microsoft.com/azure/role-based-access-control/overview).

**Fungerar länkningen av partner-ID:n med Azure Stack?**

Ja, du kan länka ditt partner-ID för Azure Stack.

**Hur gör jag för att länka mitt partner-ID om mitt företag använder [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) för att komma åt kundresurser?**

Om du registrerar kunder i Azure-delegerad resurshantering genom att [publicera ett erbjudande om hanterade tjänster på Azure Marketplace](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers) associeras ditt MPN-ID automatiskt. Om du [registrerar kunder genom att distribuera Azure Resource Manager-mallar](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer) måste du associera ditt MPN-ID (Microsoft Partner Network) med minst ett användarkonto som har åtkomst till var och en av dina registrerade prenumerationer. Observera att du måste göra detta i klientorganisationen för din tjänstleverantör. För enkelhetens skull rekommenderar vi att du skapar ett tjänstobjektskonto i din klientorganisation som är kopplat till ditt MPN-ID och att du ger det läsåtkomst för alla kunder som du registrerar.
