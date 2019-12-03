---
title: Konfigurera din egen nyckel för kryptering av Azure Event Hubs-data i vila
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för kryptering av Azure Event Hubs data rest.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 3af951d120282767bd71bc569d8c0bfe39dafffe
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705466"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal-preview"></a>Konfigurera Kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila med hjälp av Azure Portal (för hands version)
Azure Event Hubs tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Event Hubs förlitar sig på Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med hjälp av Microsoft-hanterade nycklar. 

>[!NOTE]
> Den här funktionen är för närvarande en förhandsversion. Vi rekommenderar att du inte använder i en produktions miljö.

## <a name="overview"></a>Översikt
Azure Event Hubs stöder nu möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller Kundhanterade nycklar (Bring Your Own Key – BYOK). Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till de Kundhanterade nycklar som används för att kryptera Azure Event Hubs data i vila.

Att aktivera funktionen BYOK är en tids inställnings process i namn området.

> [!NOTE]
> BYOK-funktionen stöds av [Event Hubs dedikerade kluster med en enda klient](event-hubs-dedicated-overview.md) . Det går inte att aktivera den för standard Event Hubs-namnområden.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckel användning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)

Den här artikeln visar hur du konfigurerar ett nyckel valv med Kundhanterade nycklar med hjälp av Azure Portal. Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure Portal](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Att använda Kundhanterade nycklar med Azure Event Hubs kräver att nyckel valvet har två obligatoriska egenskaper konfigurerade. De är: **mjuk borttagning** och **Rensa inte**. De här egenskaperna är aktiverade som standard när du skapar ett nytt nyckel valv i Azure Portal. Men om du behöver aktivera dessa egenskaper i ett befintligt nyckel valv måste du använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar
Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till Event Hubs Dedicated klustret.
1. Välj det namn område som du vill aktivera BYOK på.
1. På sidan **Inställningar** i Event Hubs namn området väljer du **kryptering (för hands version)** . 
1. Välj den **Kundhanterade nyckel krypteringen i vila** , som visas i följande bild. 

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckel valv med nycklar
När du har aktiverat Kundhanterade nycklar måste du associera kundens hanterade nyckel med ditt Azure Event Hubs-namnområde. Event Hubs stöder endast Azure Key Vault. Om du aktiverar alternativet för **kryptering med kundhanterad nyckel** i föregående avsnitt måste nyckeln importeras till Azure Key Vault. Nycklarna måste också ha **mjuk borttagning** och **Rensa inte** konfigurerad för nyckeln. Dessa inställningar kan konfigureras med hjälp av [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) eller [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Om du vill skapa ett nytt nyckel valv följer du [snabb](../key-vault/key-vault-overview.md)starten för Azure Key Vault. Mer information om hur du importerar befintliga nycklar finns i [om nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).
1. Om du vill aktivera både mjuk borttagning och tömning av skydd när du skapar ett valv använder du kommandot AZ-kommandot för att [skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) ett valv.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensnings skydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du kommandot AZ-kommando för att [Uppdatera](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel väljer du **generera/importera** från menyn **nycklar** under **Inställningar**.
        
        ![Välj knappen generera/importera](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 
    1. Nu kan du välja den här nyckeln för att associera med Event Hubs namn området för kryptering i list rutan. 

        ![Välj nyckel från Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Fyll i informationen om nyckeln och klicka på **Välj**. Detta möjliggör kryptering av data i vila i namn området med en kundhanterad nyckel. 

        > [!NOTE]
        > För för hands versionen kan du bara välja en enskild nyckel. 

## <a name="rotate-your-encryption-keys"></a>Rotera dina krypterings nycklar
Du kan rotera din nyckel i nyckel valvet med hjälp av rotations funktionen för Azure Key Vault. Mer information finns i [Konfigurera nyckel rotation och granskning](../key-vault/key-vault-key-rotation-log-monitoring.md). Aktiverings-och utgångs datum kan också ställas in för att automatisera nyckel rotationen. Tjänsten Event Hubs identifierar nya nyckel versioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar
Om du återkallar åtkomst till krypterings nycklarna rensas inte data från Event Hubs. Men det går inte att komma åt data från namn området Event Hubs. Du kan återkalla krypterings nyckeln via åtkomst principen eller genom att ta bort nyckeln. Läs mer om åtkomst principer och skydda nyckel valvet från [säker åtkomst till ett nyckel valv](../key-vault/key-vault-secure-your-key-vault.md).

När krypterings nyckeln har återkallats går Event Hubss tjänsten på det krypterade namn området inte att fungera. Om åtkomsten till nyckeln är aktive rad eller om borttagnings nyckeln återställs, kommer Event Hubs-tjänsten att välja nyckeln så att du kan komma åt data från namn området krypterad Event Hubs.

> [!NOTE]
> Om du tar bort en befintlig krypterings nyckel från nyckel valvet och ersätter den med en ny nyckel i Event Hubs namn området, eftersom borttagnings nyckeln fortfarande är giltig (eftersom den cachelagras) i upp till en timme, kan dina gamla data (som krypterades med den gamla nyckeln) fortfarande vara tillgängliga tillsammans  med nya data, som nu är åtkomliga med den nya nyckeln. Det här beteendet är avsiktligt i för hands versionen av funktionen. 

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar 
Genom att ställa in diagnostikloggar för BYOK-aktiverade namn rymder får du nödvändig information om åtgärder när ett namn område krypteras med Kundhanterade nycklar. Dessa loggar kan aktive ras och senare strömmas till en Event Hub eller analyseras via Log Analytics eller strömmas till lagring för att utföra anpassad analys. Mer information om diagnostiska loggar finns i [Översikt över Azure Diagnostic-loggar](../azure-monitor/platform/resource-logs-overview.md).

## <a name="enable-user-logs"></a>Aktivera användar loggar
Följ dessa steg om du vill aktivera loggar för Kundhanterade nycklar.

1. I Azure Portal navigerar du till det namn område där BYOK är aktiverat.
1. Välj **diagnostikinställningar** under **övervakning**.

    ![Välj diagnostikinställningar](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Välj **+ Lägg till diagnostisk inställning**. 

    ![Välj Lägg till diagnostisk inställning](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Ange ett **namn** och välj var du vill strömma loggarna till.
1. Välj **CustomerManagedKeyUserLogs** och **Spara**. Den här åtgärden aktiverar loggarna för BYOK i namn området.

    ![Välj kund hanterat alternativ för nyckel användar loggar](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Logg schema 
Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller sträng fält som använder det format som beskrivs i följande tabell. 

| Namn | Beskrivning |
| ---- | ----------- | 
| /TN | Beskrivning av uppgiften som misslyckades. |
| ActivityId | Internt ID som används för spårning. |
| category | Definierar klassificeringen för uppgiften. Om nyckeln från ditt nyckel valv till exempel är inaktive rad, är det en informations kategori eller om en nyckel inte kan vara unwrap, kan den falla under fel. |
| resourceId | Resurs-ID för Azure Resource Manager |
| keyVault | Fullständigt namn på nyckel valvet. |
| key | Nyckel namnet som används för att kryptera Event Hubs-namnområdet. |
| version | Den nyckel version som används. |
| reparation | Åtgärden som utförs på nyckeln i ditt nyckel valv. Du kan till exempel inaktivera/aktivera nyckeln, radbrytas eller packa upp |
| rikt | Den kod som är kopplad till åtgärden. Exempel: felkod, 404 innebär att nyckeln inte hittades. |
| meddelande | Ett fel meddelande som är kopplat till åtgärden |

Här är ett exempel på loggen för en kundhanterad nyckel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Felsökning
Vi rekommenderar att du alltid aktiverar loggar som visas i föregående avsnitt. Det hjälper till att spåra aktiviteter när BYOK-kryptering är aktiverat. Det hjälper också att lösa problemen.

Nedan visas vanliga felkoder som du kan titta efter när BYOK-kryptering är aktiverat.

| Åtgärd | Felkod | Resulterande data tillstånd |
| ------ | ---------- | ----------------------- | 
| Ta bort behörigheten wrap/unwrap från ett nyckel valv | 403 |    Otillgänglig |
| Ta bort AAD-rollens medlemskap från ett AAD-huvud som beviljats behörigheten wrap/unwrap | 403 |  Otillgänglig |
| Ta bort en krypterings nyckel från nyckel valvet | 404 | Otillgänglig |
| Ta bort nyckel valvet | 404 | Otillgänglig (Detta förutsätter att mjuk borttagning är aktiverat, vilket är en obligatorisk inställning.) |
| Ändra förfallo perioden på krypterings nyckeln så att den redan har gått ut | 403 |   Otillgänglig  |
| Ändra NBF (inte före) så att nyckel krypterings nyckeln inte är aktiv | 403 | Otillgänglig  |
| Att välja alternativet **Tillåt MSFT-tjänster** för Key Vault-brandväggen eller på annat sätt blockera nätverks åtkomst till nyckel valvet som har krypterings nyckeln | 403 | Otillgänglig |
| Flytta nyckel valvet till en annan klient | 404 | Otillgänglig |  
| Tillfälligt nätverks problem eller DNS/AAD/MSI-avbrott |  | Tillgänglig med krypterings nyckel för cachelagrad data |

> [!IMPORTANT]
> Om du vill aktivera geo-DR på ett namn område som använder BYOK-krypteringen måste det sekundära namn området för länkning vara i ett dedikerat kluster och måste ha en systemtilldelad hanterad identitet aktive rad. Mer information finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Om tjänst slut punkter för virtuella nätverk (VNet) konfigureras på Azure Key Vault för din Event Hubs-namnrymd, stöds inte BYOK. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över Event Hubs](event-hubs-about.md)
- [Översikt över Key Vault](../key-vault/key-vault-overview.md)




