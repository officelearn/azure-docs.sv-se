---
title: Självstudie för att beställa Azure Data Box | Microsoft Docs
description: I den här självstudien får du lära dig mer om Azure Data Box, en hybrid lösning som gör att du kan importera lokala data till Azure och hur du beställer Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: alkohli
ms.openlocfilehash: 400c5bd4dd31daaf826ed63ada75266d4ab3099c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313219"
---
# <a name="tutorial-order-azure-data-box"></a>Självstudie: Beställa Azure Data Box

Azure Data Box är en hybridmolnlösning som gör att du kan importera lokala data till Azure på ett snabbt, enkelt och tillförlitligt sätt. Du överför data till en lagringsenhet med 80 TB (användbar kapacitet) från Microsoft och skickar sedan tillbaka enheten. Dessa data överförs sedan till Azure.

I den här självstudien beskriver vi hur du kan beställa en Azure Data Box. I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar för att distribuera Data Box
> * Beställa en Data Box
> * Spåra beställningen
> * Avbryta beställningen

## <a name="prerequisites"></a>Krav

# <a name="portal"></a>[Portal](#tab/portal)

Slutför följande konfigurations krav för Data Box-enhet tjänst och enhet innan du distribuerar enheten:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

Du kan logga in på Azure och köra Azure CLI-kommandon på ett av två sätt:

* Du kan installera CLI-och kör CLI-kommandona lokalt.
* Du kan köra CLI-kommandon inifrån Azure Portal i Azure Cloud Shell.

Vi använder Azure CLI via Windows PowerShell för självstudien, men du kan välja något av alternativen.

### <a name="for-azure-cli"></a>För Azure CLI

Innan du börjar ska du kontrollera att:

#### <a name="install-the-cli-locally"></a>Installera CLI lokalt

* Installera [Azure CLI](/cli/azure/install-azure-cli) version 2.0.67 eller senare. Alternativt kan du [Installera med MSI](https://aka.ms/installazurecliwindows).

**Logga in på Azure**

Öppna ett Windows PowerShell-kommando fönster och logga in på Azure med kommandot [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
PS C:\Windows> az login
```

Här är resultatet från en lyckad inloggning:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Installera Azure Data Box CLI-tillägget**

Innan du kan använda kommandona för Azure Data Box CLI måste du installera tillägget. Med Azure CLI-tillägg får du tillgång till experimentella kommandon som inte finns med i standardversionen av CLI:t ännu. Mer information om tillägg finns i [använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

Kör följande kommando för att installera tillägget för Azure Data Box: `az extension add --name databox` :

```azurecli

    PS C:\Windows> az extension add --name databox
```

Om tillägget har installerats visas följande utdata:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Du kan använda [Azure Cloud Shell](https://shell.azure.com/), en Azure-värdbaserad, interaktiv gränssnitts miljö i webbläsaren för att köra CLI-kommandon. Azure Cloud Shell stöder bash eller Windows PowerShell med Azure-tjänster. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Välj knappen Cloud Shell på menyn i det övre högra avsnittet av Azure Portal:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen som beskrivs i den här instruktions artikeln.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>För Azure PowerShell

Innan du börjar ska du kontrol lera att du:

* Installera Windows PowerShell 6.2.4 eller högre.
* Installera Azure PowerShell-modulen (AZ).
* Installera Azure Data Box-modulen (AZ. data data).
* Logga in i Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Installera Azure PowerShell och moduler lokalt

**Installera eller uppgradera Windows PowerShell**

Du måste ha Windows PowerShell-version 6.2.4 eller senare installerat. Om du vill ta reda på vilken version av PowerShell som du har installerat kör du: `$PSVersionTable` .

Du ser följande utdata:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Om din version är lägre än 6.2.4 måste du uppgradera din version av Windows PowerShell. Information om hur du installerar den senaste versionen av Windows PowerShell finns i [installera Azure PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7&preserve-view=true).

**Installera Azure PowerShell-och Data Box-enhet-moduler**

Du måste installera Azure PowerShell-modulerna för att kunna använda Azure PowerShell för att beställa en Azure Data Box. Så här installerar du Azure PowerShell-moduler:

1. Installera [Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).
2. Installera sedan AZ. data med hjälp av kommandot `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna ett Windows PowerShell-kommando fönster och logga in på Azure med kommandot [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) :

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Här är resultatet från en lyckad inloggning:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Detaljerad information om hur du loggar in på Azure med hjälp av Windows PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Beställa Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Utför följande steg i Azure Portal för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Välj **+ Skapa en resurs** och sök efter *Azure Data Box*. Välj **Azure Data Box**.

   ![Skärm bild av det nya avsnittet med Azure Data Box i Sök fältet.](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Välj **Skapa**.

   ![Skärm bild av Azure Data Box-avsnittet med alternativet Skapa som kallas för.](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller välj följande information och välj **Tillämpa**.

    |Inställning  |Värde  |
    |---------|---------|
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Resursgrupp | Välj en befintlig resurs grupp. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. |
    |Källans land/region    |    Välj landet/regionen där dina data finns.         |
    |Azure-målregion     |     Välj den Azure-region dit du vill överföra data. <br> Mer information finns i [regional tillgänglighet](data-box-overview.md#region-availability).            |

    [![Azure Data Box import ordning](media/data-box-deploy-ordered/select-data-box-import-04b.png)](media/data-box-deploy-ordered/select-data-box-import-04b.png#lightbox)

5. Välj **Data Box**. Den högsta användbara kapaciteten för en enskild order är 80 TB. Du kan skapa flera beställningar för större datamängder.

    ![Skärm bild av de olika data storlekar som är tillgängliga för markering: Data Box Disk 40 terabyte, Data Box-enhet 100 terabyte, Data Box Heavy, 1000 terabyte och skicka dina egna diskar 1 terabyte.](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Gå till fliken **grundläggande** i **ordning**. Ange eller Välj följande information och välj **Nästa: data mål>**.

    |Inställningen  |Värde  |
    |---------|---------|
    |Prenumeration      | Prenumerationen fylls i automatiskt baserat på din tidigare val.|
    |Resursgrupp    | Den resurs grupp som du valde tidigare. |
    |Importera beställnings namn | Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.    |

    ![Skärm bild av guiden order som visar grundläggande steg i guiden med rätt information ifylld.](media/data-box-deploy-ordered/select-data-box-import-06.png)

    Som standard krypteras lösen ordet för enhets upplåsningen med hjälp av en Microsoft-hanterad nyckel. När du har slutfört beställningen kan du lägga till en kundhanterad nyckel. Med en kundhanterad nyckel kan du använda din egen nyckel från en Azure Key Vault-nyckel för att skydda enhetens lösen ord för att låsa upp enheten. Mer information finns i [använda Kundhanterade nycklar i Azure Key Vault för Azure Data Box](data-box-customer-managed-encryption-key-portal.md).

7. På fliken **data mål** väljer du **data mål**.

    Om du använder **lagrings kontona** som lagrings mål visas följande skärm bild:

    ![Azure Data Box data destination](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. Data Box kan länkas med upp till 10 lagringskonton. Du kan också skapa ett nytt konto för **Generell användning v1** , **Generell användning v2** eller **bloblagring**.

    Lagringskonton med virtuella nätverk stöds. För att Data Box-tjänsten ska fungera med skyddade lagringskonton aktiverar du de betrodda tjänsterna i inställningarna för nätverksbrandväggen för lagringskontot. Mer information finns i så här [lägger du till Azure Data box som en betrodd tjänst](../storage/common/storage-network-security.md#exceptions).

    Om du använder Data Box-enhet för att skapa **hanterade diskar** från lokala virtuella hård diskar (VHD: er), måste du också ange följande information:

    |Inställningen  |Värde  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resursgrupper om du planerar att skapa hanterade diskar från lokala virtuella hårddiskar. Du kan bara använda en befintlig resurs grupp om resurs gruppen skapades tidigare när du skapade en Data Box-enhets ordning för den hanterade disken av Data Box-enhet-tjänsten. <br> Ange flera resursgrupper avgränsade med semikolon. Högst 10 resursgrupper stöds.|

    ![Skärm bild av guiden order som visar steget data mål i guiden med rätt information ifylld.](media/data-box-deploy-ordered/select-data-box-import-07b.png)

    Det angivna lagringskontot för hanterade diskar används som ett mellanlagringskonto. Data Box-tjänsten laddar upp de virtuella hårddiskarna som sidblobar till mellanlagringskontot innan de konverteras till hanterade diskar och flyttas till resursgrupperna. Mer information finns i [Verifiera dataöverföring till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).
   > [!NOTE]
   > Om en sid-BLOB inte har konverterats till en hanterad disk finns den kvar i lagrings kontot och du debiteras för lagring.

    Välj **Nästa: säkerhet** för att fortsätta.

    På sidan **säkerhet** kan du använda din egen enhet och dela lösen ord och välja att använda Double Encryption. 

    Alla inställningar på **säkerhets** skärmen är valfria. Om du inte ändrar några inställningar används standardinställningarna.

    ![Säkerhets skärm för en Data Box-enhet import ordning](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

1. Om du inte vill använda de systemgenererade lösen orden som Azure Data Box använder som standard, expanderar **du ta med ditt eget lösen ord**.

   De systemgenererade lösen orden är säkra och rekommenderas om inte organisationen kräver något annat.

   ![Utöka alternativen för att ta med egna lösen ord för en Data Box-enhet import ordning](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Om du vill använda ditt eget lösen ord för den nya enheten väljer du **Använd ditt eget lösen ord** i **Ange inställningar för enhetens lösen** ord och anger ett lösen ord som uppfyller säkerhets kraven.
   
     ![Säkerhets skärm för Data Box-enhet import, alternativ för att använda ditt eget enhets lösen ord](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

   - Så här använder du egna lösen ord för resurser:

     1. Genom att **Ange inställningar för dela lösen ord** väljer **du Använd dina egna lösen ord** och **väljer sedan lösen ord för resurserna**.
     
        ![Säkerhets skärmen för Data Box-enhet import, alternativ för att använda dina egna resurs lösen ord](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

     1. Ange ett lösen ord för varje lagrings konto i ordern. Lösen ordet kommer att användas på alla resurser för lagrings kontot.
     
        Om du vill använda samma lösen ord för alla lagrings konton väljer **du kopiera till alla**. När du är klar väljer du **Spara**.
     
        ![Skärm för att ange resurs lösen ord för en Data Box-enhet import ordning](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

       På **säkerhets** skärmen kan du använda **Visa eller ändra lösen ord** för att ändra lösen ord.

1. I **säkerhet** , om du vill aktivera programvarubaserad dubbel kryptering, expanderar du **Double-Encryption (för hög säkra miljöer)** och väljer **Aktivera dubbel kryptering för beställningen**.

   ![Säkerhets skärm för Data Box-enhet import, aktivera programvarubaserad kryptering för en Data Box-enhet beställning](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

   Den programvarubaserade krypteringen utförs förutom AES-256-bitars kryptering av data på Data Box-enhet.

   > [!NOTE]
   > Att aktivera det här alternativet kan göra order bearbetning och data kopieringen ta längre tid. Du kan inte ändra det här alternativet när du har skapat din beställning.

   Välj **Nästa: kontakt uppgifter** för att fortsätta.

8. I **kontakt information** väljer du **+ Lägg till leverans adress**.

    ![Skärm bild av guiden order som visar steget kontakt information i guiden med alternativet Lägg till leverans adress som kallas för.](media/data-box-deploy-ordered/select-data-box-import-08a.png)

9. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Välj **Verifiera adress**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det.

   ![Skärm bild av dialog rutan Lägg till leverans adress med alternativet leverera med alternativ och alternativet Lägg till leverans adress som kallas.](media/data-box-deploy-ordered/select-data-box-import-10.png)

   Om du har valt självhanterad leverans får du ett e-postmeddelande när beställningen har placerats. Mer information om självhanterad leverans finns i [Använd självhanterad leverans](data-box-portal-customer-managed-shipping.md).

10. Välj **Lägg till leverans adress** när leverans informationen har verifierats. Du kommer tillbaka till fliken **kontakt information** .

11. När du har återlämnat **kontakt uppgifterna** lägger du till en eller flera e-postadresser. Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

    ![Skärm bild av e-postavsnittet i steget kontakt information i guiden Beställ med text rutan e-post och nästa alternativ: granska och ordna som anropas.](media/data-box-deploy-ordered/select-data-box-import-08c.png)

12. Granska informationen i **Granska + order** som är relaterad till order, kontakt, avisering och sekretess villkor. Markera rutan för avtalet till sekretesspolicyn.

13. Välj **Beställ**. Det tar några minuter att skapa beställningen.

    ![Skärm bild av guiden order som visar steget granska och sortera och beställnings alternativet.](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utför följande steg med Azure CLI för att beställa en enhet:

1. Skriv ned inställningarna för din Data Box-enhets ordning. Inställningarna omfattar din personliga/affärs information, prenumerations namn, enhets information och information om leverans. Du måste använda de här inställningarna som parametrar när du kör CLI-kommandot för att skapa Data Box-enhets ordningen. I följande tabell visas de parameter inställningar som används för `az databox job create` :

   | Inställning (parameter) | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resource-group| Använd ett befintligt eller skapa ett nytt. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |name| Namnet på den ordning som du skapar. | "mydataboxorder"|
   |kontakt namn| Namnet som är associerat med leverans adressen. | "Gus Polen"|
   |phone| Telefonnumret till den person eller det företag som ska ta emot ordern.| "14255551234"
   |location| Den närmaste Azure-region som ska leverera din enhet.| "Västra USA"|
   |sku| Den speciella Data Box-enhet enhet som du beställer. Giltiga värden är: "data," DataBoxDisk "och" DataBoxHeavy "| DataBox |
   |e-postlista| E-postadresserna som är kopplade till ordern.| "gusp@contoso.com" |
   |gata – Address1| Gatuadressen dit ordern ska skickas. | "15700 NE 39th St" |
   |gata – Address2| Den sekundära adress informationen, till exempel lägenhets nummer eller build-nummer. | "Bld 123" |
   |city| Den stad som enheten ska skickas till. | Redmond |
   |delstat-eller-provins| Det tillstånd som enheten ska skickas till.| WA |
   |land| Det land som enheten kommer att skickas till. | "USA" |
   |post nummer| Post numret eller post numret som är associerat med leverans adressen.| "98052"|
   |företags namn| Namnet på ditt företag som du arbetar för.| "Contoso, LTD" |
   |storage account| Det Azure Storage konto som du vill importera data från.| mystorageaccount|
   |felsökning| Inkludera felsöknings information för utförlig loggning  | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga <string>|
   |utförlig| Inkludera utförlig loggning. | --utförlig |

2. I kommando tolken för Choice eller Terminal kör du [AZ data Box Create](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create&preserve-view=true) för att skapa din Azure Data boxs order.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Här är ett exempel på kommando användning:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Alla Azure CLI-kommandon kommer att använda JSON som utdataformat som standard om du inte ändrar det. Du kan ändra utdataformatet med hjälp av den globala parametern `--output <output-format>` . Om du ändrar formatet till "Tabell" förbättras läsbarheten.

   Här är samma kommando som vi precis körde med en liten genom att ändra formateringen:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Här är utdata från att köra kommandot:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Utför följande steg med Azure PowerShell för att beställa en enhet:

1. Innan du skapar import ordningen måste du skaffa ditt lagrings konto och spara lagrings konto objekt i en variabel.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Skriv ned inställningarna för din Data Box-enhets ordning. Inställningarna omfattar din personliga/affärs information, prenumerations namn, enhets information och information om leverans. Du måste använda de här inställningarna som parametrar när du kör PowerShell-kommandot för att skapa Data Box-enhets ordningen. I följande tabell visas de parameter inställningar som används för [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Inställning (parameter) | Beskrivning |  Exempelvärde |
    |---|---|---|
    |ResourceGroupName [krävs]| Använd en befintlig resurs grupp. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
    |Namn [obligatoriskt]| Namnet på den ordning som du skapar. | "mydataboxorder"|
    |Kontakt person [nödvändig]| Namnet som är associerat med leverans adressen. | "Gus Polen"|
    |Telefonnummer [obligatoriskt]| Telefonnumret till den person eller det företag som ska ta emot ordern.| "14255551234"
    |Plats [krävs]| Den närmaste Azure-region som ska leverera din enhet.| WestUS|
    |DataBoxType [krävs]| Den speciella Data Box-enhet enhet som du beställer. Giltiga värden är: "data," DataBoxDisk "och" DataBoxHeavy "| DataBox |
    |EmailId [krävs]| E-postadresserna som är kopplade till ordern.| "gusp@contoso.com" |
    |StreetAddress1 [krävs]| Gatuadressen dit ordern ska skickas. | "15700 NE 39th St" |
    |StreetAddress2| Den sekundära adress informationen, till exempel lägenhets nummer eller build-nummer. | "Bld 123" |
    |StreetAddress3| Den tertiära adress informationen. | |
    |Stad [obligatoriskt]| Den stad som enheten ska skickas till. | Redmond |
    |StateOrProvinceCode [krävs]| Det tillstånd som enheten ska skickas till.| WA |
    |CountryCode [krävs]| Det land som enheten kommer att skickas till. | "USA" |
    |Post nummer [obligatoriskt]| Post numret eller post numret som är associerat med leverans adressen.| "98052"|
    |CompanyName| Namnet på ditt företag som du arbetar för.| "Contoso, LTD" |
    |StorageAccountResourceId [krävs]| Azure Storage konto-ID: t som du vill importera data från.| <AzStorageAccount>. ID |

3. I kommando tolken för Choice eller Terminal använder du kommandot [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) för att skapa din Azure Data boxs ordning.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Här är utdata från att köra kommandot:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Spåra beställningen

# <a name="portal"></a>[Portal](#tab/portal)

När du har skapat beställningen kan du spåra statusen för ordern via Azure-portalen. Öppna Data Box-beställningen och navigera till **Overview** (Översikt) för att visa status. Portalen visar beställningen i tillståndet **Ordered** (beställd).

Om enheten inte är tillgänglig får du ett meddelande. Om enheten är tillgänglig identifierar Microsoft enheten för leverans och förbereder försändelsen. Vid enhetsförberedelsen utförs följande åtgärder:

* SMB-resurser skapas för varje lagringskonto som är associerat med enheten.
* För varje resurs genereras autentiseringsuppgifter för åtkomst såsom användarnamn och lösenord.
* Det enhetslösenord som hjälper till att låsa upp enheten skapas också.
* Data Box låses för att förhindra obehörig åtkomst till enheten.

När enhetsförberedelserna är klara visar portalen beställningen i tillståndet **Processed** (Behandlad).

![Data Box-beställningen behandlas](media/data-box-overview/data-box-order-status-processed.png)

Microsoft förbereder sedan enheten och skickar den via en regional transportör. Du får ett spårningsnummer när enheten har skickats. Portalen visar ordningen för statusen **Dispatched** (Skickad).

![Data Box-beställningen skickas](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>Spåra en enskild order

Om du vill ha spårnings information om en enskild, befintlig Azure Data Box ordning kör du [AZ Data Center-jobbet show](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show&preserve-view=true). Kommandot visar information om ordningen, till exempel, men inte begränsat till: namn, resurs grupp, spårnings information, prenumerations-ID, kontakt information, leverans typ och enhets-SKU.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   I följande tabell visas parameter informationen för `az databox job show` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resurs-grupp [obligatoriskt]| Namnet på resurs gruppen som är kopplad till ordern. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |namn [obligatoriskt]| Namnet på den ordning som ska visas. | "mydataboxorder"|
   |felsökning| Inkludera felsöknings information för utförlig loggning | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga <string>|
   |utförlig| Inkludera utförlig loggning. | --utförlig |

   Här är ett exempel på kommandot med utdataformatet inställt på "Tabell":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Här är utdata från att köra kommandot:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> List ordningen kan stödjas på prenumerations nivå och som gör resurs gruppen till en valfri parameter (i stället för en obligatorisk parameter).

### <a name="list-all-orders"></a>Lista alla beställningar

Om du har ordnat flera enheter kan du köra [AZ](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list&preserve-view=true) för att visa alla dina Azure Data boxs beställningar. Kommandot visar alla beställningar som tillhör en speciell resurs grupp. Visas också i utdata: order namn, leverans status, Azure-region, leverans typ, order status. Annullerade order ingår också i listan.
Kommandot visar även tidsstämplar för varje order.

```azurecli
az databox job list --resource-group <resource-group>
```

I följande tabell visas parameter informationen för `az databox job list` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resurs-grupp [obligatoriskt]| Namnet på resurs gruppen som innehåller orderna. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |felsökning| Inkludera felsöknings information för utförlig loggning | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga <string>|
   |utförlig| Inkludera utförlig loggning. | --utförlig |

   Här är ett exempel på kommandot med utdataformatet inställt på "Tabell":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Spåra en enskild order

Kör [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob)för att få spårnings information om en enskild, befintlig Azure Data boxs ordning. Kommandot visar information om ordningen, till exempel, men inte begränsat till: namn, resurs grupp, spårnings information, prenumerations-ID, kontakt information, leverans typ och enhets-SKU.

> [!NOTE]
> `Get-AzDataBoxJob` används för att visa både en och flera order. Skillnaden är att du anger ordnings namnet för enskilda beställningar.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   I följande tabell visas parameter informationen för `Get-AzDataBoxJob` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |ResourceGroup [krävs]| Namnet på resurs gruppen som är kopplad till ordern. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |Namn [obligatoriskt]| Namnet på den ordning som information ska hämtas för. | "mydataboxorder"|
   |ResourceId| ID för den resurs som är kopplad till ordern. |  |

   Här är ett exempel på kommandot med utdata:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Här är utdata från att köra kommandot:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Lista alla beställningar

Om du har ordnat flera enheter kan du köra [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob) för att visa alla dina Azure Data Box beställningar. Kommandot visar alla beställningar som tillhör en speciell resurs grupp. Visas också i utdata: order namn, leverans status, Azure-region, leverans typ, order status. Annullerade order ingår också i listan.
Kommandot visar även tidsstämplar för varje order.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Här är ett exempel på kommandot:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Här är utdata från att köra kommandot:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Avbryta beställningen

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill avbryta den här ordningen går du till **Översikt** i Azure Portal och väljer **Avbryt** från kommando fältet.

När du har gjort en beställning kan du avbryta den när som helst innan orderstatusen markeras som Processed (Behandlad).

Om du vill ta bort en annullerad order går du till **Översikt** och väljer **ta bort** från kommando fältet.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>Annullera en beställning

Om du vill avbryta en Azure Data Boxs ordning kan du köra [AZ data-Job Cancel](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel&preserve-view=true). Du måste ange orsaken till annulleringen av ordern.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   I följande tabell visas parameter informationen för `az databox job cancel` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resurs-grupp [obligatoriskt]| Namnet på resurs gruppen som är kopplad till den ordning som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |namn [obligatoriskt]| Namnet på den ordning som ska tas bort. | "mydataboxorder"|
   |Orsak [required]| Orsaken till att ordern annulleras. | "Jag har angett felaktig information och behövde avbryta beställningen." |
   |yes| Begär inte bekräftelse. | --Ja (-y)| --Ja-y |
   |felsökning| Inkludera felsöknings information för utförlig loggning | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga <string>|
   |utförlig| Inkludera utförlig loggning. | --utförlig |

   Här är ett exempel på kommandot med utdata:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Ta bort en order

Om du har avbrutit en Azure Data Box order kan du köra [AZ](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete&preserve-view=true) för att ta bort ordern.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   I följande tabell visas parameter informationen för `az databox job delete` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resurs-grupp [obligatoriskt]| Namnet på resurs gruppen som är kopplad till den ordning som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |namn [obligatoriskt]| Namnet på den ordning som ska tas bort. | "mydataboxorder"|
   |prenumeration| Namnet eller ID (GUID) för din Azure-prenumeration. | "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" |
   |yes| Begär inte bekräftelse. | --Ja (-y)| --Ja-y |
   |felsökning| Inkludera felsöknings information för utförlig loggning | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga <string>|
   |utförlig| Inkludera utförlig loggning. | --utförlig |

Här är ett exempel på kommandot med utdata:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Här är utdata från att köra kommandot:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Annullera en beställning

Om du vill avbryta en Azure Data Box ordning kör du [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). Du måste ange orsaken till annulleringen av ordern.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

I följande tabell visas parameter informationen för `Stop-AzDataBoxJob` :

| Parameter | Beskrivning |  Exempelvärde |
|---|---|---|
|ResourceGroup [krävs]| Namnet på resurs gruppen som är kopplad till den order som ska avbrytas. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
|Namn [obligatoriskt]| Namnet på den ordning som ska tas bort. | "mydataboxorder"|
|Orsak [required]| Orsaken till att ordern annulleras. | "Jag har angett felaktig information och behövde avbryta beställningen." |
|Force | Tvingar cmdleten att köras utan bekräftelse från användaren. | -Force |

Här är ett exempel på kommandot med utdata:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Här är utdata från att köra kommandot:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Ta bort en order

Om du har avbrutit en Azure Data Box order kan du köra [Remove-AzDataBoxJob](/powershell/module/az.databox/remove-azdataboxjob) för att ta bort ordern.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

I följande tabell visas parameter informationen för `Remove-AzDataBoxJob` :

| Parameter | Beskrivning |  Exempelvärde |
|---|---|---|
|ResourceGroup [krävs]| Namnet på resurs gruppen som är kopplad till den ordning som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
|Namn [obligatoriskt]| Namnet på den ordning som ska tas bort. | "mydataboxorder"|
|Force | Tvingar cmdleten att köras utan bekräftelse från användaren. | -Force |

Här är ett exempel på kommandot med utdata:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Här är utdata från att köra kommandot:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Azure Data Box-artiklar som:

> [!div class="checklist"]
>
> * Förutsättningar för att distribuera Data Box
> * Beställa Data Box
> * Spåra beställningen
> * Avbryta beställningen

Gå vidare till nästa självstudie och lär dig hur du ställer in din Data Box.

> [!div class="nextstepaction"]
> [Konfigurera Azure Data Box](./data-box-deploy-set-up.md)
