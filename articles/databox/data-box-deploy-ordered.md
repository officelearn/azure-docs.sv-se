---
title: Självstudie för att beställa Azure Data Box | Microsoft Docs
description: Lär dig om förutsättningarna för distribution och hur du beställer en Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392495"
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

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

Du kan logga in på Azure och köra Azure CLI-kommandon på ett av två sätt:

* Du kan installera CLI-och kör CLI-kommandona lokalt.
* Du kan köra CLI-kommandon inifrån Azure Portal i Azure Cloud Shell.

Vi använder Azure CLI via Windows PowerShell för självstudien, men du kan välja något av alternativen.

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

* Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) version 2.0.67 eller senare. Alternativt kan du [Installera med MSI](https://aka.ms/installazurecliwindows).

#### <a name="sign-in-to-azure"></a>Logga in på Azure

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

#### <a name="install-the-azure-data-box-cli-extension"></a>Installera Azure Data Box CLI-tillägget

Innan du kan använda kommandona för Azure Data Box CLI måste du installera tillägget. Azure CLI-tillägg ger dig till gång till experiment-och för hands versions kommandon som ännu inte har levererats som en del av kärn-CLI. Mer information om tillägg finns i [använda tillägg med Azure CLI](/cli/azure/azure-cli-extensions-overview).

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

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Du kan använda [Azure Cloud Shell](https://shell.azure.com/), en Azure-värdbaserad, interaktiv gränssnitts miljö i webbläsaren för att köra CLI-kommandon. Azure Cloud Shell stöder bash eller Windows PowerShell med Azure-tjänster. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Klicka på knappen Cloud Shell på menyn i det övre högra avsnittet av Azure Portal:

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen som beskrivs i den här instruktions artikeln.

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>Beställa Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Utför följande steg i Azure Portal för att beställa en enhet.

1. Använd dina Microsoft Azure-autentiseringsuppgifter för att logga in på denna URL: [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **+ Skapa en resurs** och sök efter *Azure Data Box*. Klicka på **Azure Data Box**.

   [![Söka efter Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Klicka på **Skapa**.

4. Kontrollera om Data Box-tjänsten är tillgänglig i din region. Ange eller välj följande information och välj **Tillämpa**.

    |Inställningen  |Värde  |
    |---------|---------|
    |Prenumeration     | Välj en prenumeration för EA, CSP eller Azure-sponsring för Data Box-tjänsten. <br> Prenumerationen är kopplad till ditt faktureringskonto.       |
    |Överföringstyp     | Välj **Importera till Azure**.        |
    |Källans land/region    |    Välj landet/regionen där dina data finns.         |
    |Azure-målregion     |     Välj den Azure-region dit du vill överföra data.        |

5. Välj **Data Box**. Den högsta användbara kapaciteten för en enskild order är 80 TB. Du kan skapa flera beställningar för större datamängder.

      [![Välj Data Box-enhet alternativ 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. I **Order** (Beställa) anger du **Order details** (Beställningsinformation). Ange eller välj följande information och välj **Nästa**.

    |Inställning  |Värde  |
    |---------|---------|
    |Namn     |  Välj ett smeknamn så att du kan spåra beställningen. <br> Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. <br> Namnet måste börja och sluta med en bokstav eller en siffra.      |
    |Resursgrupp     |    Använd ett befintligt eller skapa ett nytt. <br> En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans.         |
    |Azure-målregion     | Välj en region för lagringskontot. <br> Mer information finns i [regional tillgänglighet](data-box-overview.md#region-availability).        |
    |Lagringsmål     | Välj mellanlagringskonto eller hanterade diskar eller båda. <br> Baserat på den angivna Azure-regionen väljer du ett eller flera lagringskonton från den filtrerade listan med befintliga lagringskonton. Data Box kan länkas med upp till 10 lagringskonton. <br> Du kan också skapa ett nytt konto för **Generell användning v1**, **Generell användning v2** eller **bloblagring**. <br>Lagringskonton med virtuella nätverk stöds. För att Data Box-tjänsten ska fungera med skyddade lagringskonton aktiverar du de betrodda tjänsterna i inställningarna för nätverksbrandväggen för lagringskontot. Mer information finns i så här [lägger du till Azure Data box som en betrodd tjänst](../storage/common/storage-network-security.md#exceptions).|

    Om du använder lagringskontot som lagringsmål visas följande skärmbild:

    ![Data Box-enhet ordning för lagrings konto](media/data-box-deploy-ordered/order-storage-account.png)

    Om du använder Data Box-enhet för att skapa hanterade diskar från lokala virtuella hård diskar (VHD: er), måste du också ange följande information:

    |Inställningen  |Värde  |
    |---------|---------|
    |Resursgrupper     | Skapa nya resursgrupper om du planerar att skapa hanterade diskar från lokala virtuella hårddiskar. Du kan bara använda en befintlig resurs grupp om resurs gruppen skapades tidigare när du skapade en Data Box-enhets ordning för den hanterade disken av Data Box-enhet-tjänsten. <br> Ange flera resursgrupper avgränsade med semikolon. Högst 10 resursgrupper stöds.|

    ![Data Box-enhet ordning för hanterad disk](media/data-box-deploy-ordered/order-managed-disks.png)

    Det angivna lagringskontot för hanterade diskar används som ett mellanlagringskonto. Data Box-tjänsten laddar upp de virtuella hårddiskarna som sidblobar till mellanlagringskontot innan de konverteras till hanterade diskar och flyttas till resursgrupperna. Mer information finns i [Verifiera dataöverföring till Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. I **Leveransadress** uppger du för- och efternamn, företagets postadress och ett giltigt telefonnummer. Klicka på **Verifiera adress**. Tjänsten verifierar leveransadressen och tjänstens tillgänglighet. Om tjänsten är tillgänglig för den angivna leveransadressen får du ett meddelande om det.

   Om du har valt självhanterad leverans får du ett e-postmeddelande när beställningen har placerats. Mer information om självhanterad leverans finns i [Använd självhanterad leverans](data-box-portal-customer-managed-shipping.md).

8. Klicka på **Nästa** när leverans informationen har verifierats.

9. Ange e-postadresser i **Notification details** (Information om meddelande). Tjänsten skickar e-postmeddelanden om alla uppdateringar rörande orderstatus.

    Vi rekommenderar att du använder en grupp-e-postadress, så att du kan fortsätta att ta emot meddelanden även om en gruppadministratör lämnar företaget.

10. Granska informationen **Summary** (Sammanfattning) som rör beställningen, kontakt, meddelanden och sekretess. Markera rutan för avtalet till sekretesspolicyn.

11. Klicka på **Order** (Ordning). Det tar några minuter att skapa beställningen.

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
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga<string>|
   |utförlig| Inkludera utförlig loggning. | --utförlig |

2. I kommando tolken för Choice eller Terminal använder du [dataaz-jobbet Skapa](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) för att skapa din Azure Data boxs order.

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

Om du vill ha spårnings information om en enskild, befintlig Azure Data Box ordning kör du [AZ Data Center-jobbet show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show). Kommandot visar information om ordningen, till exempel, men inte begränsat till: namn, resurs grupp, spårnings information, prenumerations-ID, kontakt information, leverans typ och enhets-SKU.

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
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga<string>|
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

Om du har ordnat flera enheter kan du köra [AZ](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) för att visa alla dina Azure Data boxs beställningar. Kommandot visar alla beställningar som tillhör en speciell resurs grupp. Visas också i utdata: order namn, leverans status, Azure-region, leverans typ, order status. Annullerade order ingår också i listan.
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
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga<string>|
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
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
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

Om du vill avbryta en Azure Data Boxs ordning kan du köra [AZ data-Job Cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel). Du måste ange orsaken till annulleringen av ordern.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   I följande tabell visas parameter informationen för `az databox job cancel` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resurs-grupp [obligatoriskt]| Namnet på resurs gruppen som är kopplad till den ordning som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |namn [obligatoriskt]| Namnet på den ordning som ska tas bort. | "mydataboxorder"|
   |Orsak [required]| Orsaken till att ordern annulleras. | "Jag har angett felaktig information och behövde avbryta beställningen." |
   |ja| Begär inte bekräftelse. | --Ja (-y)| --Ja-y |
   |felsökning| Inkludera felsöknings information för utförlig loggning | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga<string>|
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

Om du har avbrutit en Azure Data Box order kan du köra [AZ](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) för att ta bort ordern.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   I följande tabell visas parameter informationen för `az databox job delete` :

   | Parameter | Beskrivning |  Exempelvärde |
   |---|---|---|
   |resurs-grupp [obligatoriskt]| Namnet på resurs gruppen som är kopplad till den ordning som ska tas bort. En resursgrupp är en logisk container för de resurser som kan hanteras eller distribueras tillsammans. | myresourcegroup|
   |namn [obligatoriskt]| Namnet på den ordning som ska tas bort. | "mydataboxorder"|
   |prenumeration| Namnet eller ID (GUID) för din Azure-prenumeration. | "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" |
   |ja| Begär inte bekräftelse. | --Ja (-y)| --Ja-y |
   |felsökning| Inkludera felsöknings information för utförlig loggning | --Felsök |
   |Hjälp| Visa hjälp information för det här kommandot. | --hjälp-h |
   |endast-show-Errors| Visa endast fel, ignorera varningar. | --endast-show-Errors |
   |utdata-o| Anger utdataformatet.  Tillåtna värden: JSON, jsonc, none, Table, TSV, yaml, yamlc. Standardvärdet är JSON. | --Output "JSON" |
   |DocumentDB| Frågesträngen JMESPath. Mer information finns i [JMESPath](http://jmespath.org/). | --fråga<string>|
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
