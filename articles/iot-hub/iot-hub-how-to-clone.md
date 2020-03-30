---
title: Klona en Azure IoT-hubb
description: Klona en Azure IoT-hubb
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: c54853717f7e0b234df013e5aee575682d0d3d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429159"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Klona en Azure IoT-hubb till en annan region

I den här artikeln beskrivs olika sätt att klona en IoT Hub och några frågor som du måste besvara innan du börjar. Här är flera anledningar till att du kanske vill klona en IoT-hubb:
 
* Du flyttar ditt företag från en region till en annan, till exempel från Europa till Nordamerika (eller vice versa), och du vill att dina resurser och data ska vara geografiskt nära din nya plats, så du måste flytta din hubb.

* Du skapar ett nav för en utvecklingsmiljö jämfört med produktionsmiljö.

* Du vill göra en anpassad implementering av hög tillgänglighet med flera nav. Mer information finns i [avsnittet Hur du uppnår ha-korsregion i IoT Hub med hög tillgänglighet och haveriberedskap](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Du vill öka antalet [partitioner](iot-hub-scaling.md#partitions) som konfigurerats för navet. Detta anges när du först skapar hubben och inte kan ändras. Du kan använda informationen i den här artikeln för att klona hubben och när klonen skapas ökar du antalet partitioner.

Om du vill klona ett nav behöver du en prenumeration med administrativ åtkomst till det ursprungliga navet. Du kan placera den nya hubben i en ny resursgrupp och region, i samma prenumeration som den ursprungliga hubben eller till och med i en ny prenumeration. Du kan bara inte använda samma namn eftersom hubbnamnet måste vara globalt unikt.

> [!NOTE]
> För närvarande finns det ingen funktion tillgänglig för kloning av ett IoT-nav automatiskt. Det är främst en manuell process, och därmed är ganska felbenägna. Komplexiteten i kloning av ett nav står direkt i proportion till hubbens komplexitet. Kloning av en IoT-hubb utan meddelanderoutning är till exempel ganska enkelt. Om du lägger till meddelanderoutning som bara en komplexitet blir kloning av navet åtminstone en storleksordning mer komplicerad. Om du också flyttar de resurser som används för routning slutpunkter, det är en annan ordning av magniture mer komplicerat. 

## <a name="things-to-consider"></a>Saker att tänka på

Det finns flera saker att tänka på innan kloning en IoT-hubb.

* Kontrollera att alla funktioner som är tillgängliga på den ursprungliga platsen också är tillgängliga på den nya platsen. Vissa tjänster är i förhandsversion och alla funktioner är inte tillgängliga överallt.

* Ta inte bort de ursprungliga resurserna innan du skapar och verifierar den klonade versionen. När du har tagit bort ett nav är det borta för alltid, och det finns inget sätt att återställa det för att kontrollera inställningarna eller data för att se till att navet replikeras korrekt.

* Många resurser kräver globalt unika namn, så du måste använda olika namn för de klonade versionerna. Du bör också använda ett annat namn för den resursgrupp som den klonade hubben tillhör. 

* Data för den ursprungliga IoT-hubben migreras inte. Detta inkluderar telemetrimeddelanden, C2D-kommandon (Cloud-to-device) och jobbrelaterad information som scheman och historik. Mått och loggningsresultat migreras inte heller. 

* För data eller meddelanden som dirigeras till Azure Storage kan du lämna data i det ursprungliga lagringskontot, överföra dessa data till ett nytt lagringskonto i den nya regionen eller lämna gamla data på plats och skapa ett nytt lagringskonto på den nya platsen för de nya data. Mer information om hur du flyttar data i Blob-lagring finns i [Komma igång med AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Data för händelsehubbar och servicebussavsnitt och -köer kan inte migreras. Detta är point-in-time-data och lagras inte efter att meddelandena har bearbetats.

* Du måste schemalägga driftstopp för migreringen. Klona enheterna till det nya navet tar tid. Om du använder import-/exportmetoden har benchmark-testning visat att det kan ta cirka två timmar att flytta 500 000 enheter och fyra timmar för att flytta en miljon enheter. 

* Du kan kopiera enheterna till den nya hubben utan att stänga av eller ändra enheterna. 

    * Om enheterna ursprungligen etablerades med DPS uppdateras anslutningsinformationen som lagras i varje enhet om de etableras på nytt. 
    
    * Annars måste du använda metoden Importera/exportera för att flytta enheterna, och sedan måste enheterna ändras för att kunna använda den nya hubben. Du kan till exempel konfigurera enheten så att den förbrukar IoT Hub-värdnamnet från de dubbla önskade egenskaperna. Enheten tar värdnamnet för IoT Hub, kopplar från enheten från det gamla navet och återansluter det till det nya.
    
* Du måste uppdatera alla certifikat som du använder så att du kan använda dem med de nya resurserna. Dessutom har du förmodligen navet definieras i en DNS-tabell någonstans - du måste uppdatera den DNS-informationen.

## <a name="methodology"></a>Metodik

Det här är den allmänna metoden som vi rekommenderar för att flytta en IoT-hubb från en region till en annan. För meddelanderoutning förutsätter detta att resurserna inte flyttas till den nya regionen. Mer information finns i [avsnittet meddelanderoutning](#how-to-handle-message-routing).

   1. Exportera navet och dess inställningar till en Resource Manager-mall. 
   
   1. Gör nödvändiga ändringar i mallen, till exempel uppdatera alla förekomster av namnet och platsen för den klonade hubben. Uppdatera nyckeln i mallen för resursen för alla resurser i mallen som används för slutpunkter för meddelanderoutning.
   
   1. Importera mallen till en ny resursgrupp på den nya platsen. Detta skapar klonen.

   1. Felsök efter behov. 
   
   1. Lägg till allt som inte exporterades till mallen. 
   
       Konsumentgrupper exporteras till exempel inte till mallen. Du måste lägga till konsumentgrupperna i mallen manuellt eller använda [Azure-portalen](https://portal.azure.com) när navet har skapats. Det finns ett exempel på att lägga till en konsumentgrupp i en mall i artikeln [Använd en Azure Resource Manager-mall för att konfigurera IoT Hub-meddelanderoutning](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Kopiera enheterna från det ursprungliga navet till klonen. Detta beskrivs i avsnittet [Hantera enheter som är registrerade i IoT-hubben](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Så här hanterar du meddelanderoutning

Om hubben använder [anpassad routning](iot-hub-devguide-messages-read-custom.md)innehåller exporten av mallen för navet routningskonfigurationen, men själva resurserna ingår inte. Du måste välja om du vill flytta routningsresurserna till den nya platsen eller lämna dem på plats och fortsätta att använda dem "i befintligt läge". 

Anta till exempel att du har ett nav i västra USA som dirigerar meddelanden till ett lagringskonto (även i västra USA) och du vill flytta navet till östra USA. Du kan flytta navet och låta den fortfarande dirigera meddelanden till lagringskontot i västra USA, eller flytta hubben och även flytta lagringskontot. Det kan finnas en liten prestandaträff från routningsmeddelanden till slutpunktsresurser i en annan region.

Du kan flytta en hubb som använder meddelanderoutning ganska enkelt om du inte också flyttar de resurser som används för routningslutpunkterna. 

Om navet använder meddelanderoutning har du två alternativ. 

1. Flytta de resurser som används för routningsslutpunkterna till den nya platsen.

    * Du måste själv skapa de nya resurserna antingen manuellt i [Azure-portalen](https://portal.azure.com) eller med hjälp av Resource Manager-mallar. 

    * Du måste byta namn på alla resurser när du skapar dem på den nya platsen, eftersom de har globalt unika namn. 
     
    * Du måste uppdatera resursnamnen och resursnycklarna i den nya hubbens mall innan du skapar den nya hubben. Resurserna ska finnas när det nya navet skapas.

1. Flytta inte de resurser som används för routningsslutpunkterna. Använd dem "på plats".

   * I det steg där du redigerar mallen måste du hämta nycklarna för varje routningsresurs och placera dem i mallen innan du skapar den nya hubben. 

   * Navet refererar fortfarande till de ursprungliga routningsresurserna och dirigerar meddelanden till dem som konfigurerade.

   * Du kommer att ha en liten prestandaträff eftersom navet och routningsslutpunktsresurserna inte finns på samma plats.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Förbereda att migrera navet till en annan region

Det här avsnittet innehåller specifika instruktioner för hur du migrerar navet.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Hitta det ursprungliga navet och exportera det till en resursmall.

1. Logga in på [Azure-portalen](https://portal.azure.com). 

1. Gå till **Resursgrupper** och välj den resursgrupp som innehåller det nav som du vill flytta. Du kan också gå till **Resurser** och hitta navet på det sättet. Välj navet.

1. Välj **Exportera mall** i listan över egenskaper och inställningar för navet. 

   ![Skärmbild som visar kommandot för att exportera mallen för IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Välj **Hämta** för att hämta mallen. Spara filen någonstans där du kan hitta den igen. 

   ![Skärmbild som visar kommandot för att hämta mallen för IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>Visa mallen 

1. Gå till mappen Hämtningar (eller till den mapp du använde när du exporterade mallen) och leta reda på zip-filen. Öppna zip-filen och hitta `template.json`filen som heter . Markera den och välj sedan Ctrl+C för att kopiera mallen. Gå till en annan mapp som inte finns i zip-filen och klistra in filen (Ctrl+V). Nu kan du redigera den.
 
    Följande exempel är för en allmän hubb utan routningskonfiguration. Det är en S1-nivåhubb (med 1 enhet) som kallas **ContosoTestHub29358** i region **westus**. Här är den exporterade mallen.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Redigera mallen 

Du måste göra vissa ändringar innan du kan använda mallen för att skapa den nya hubben i den nya regionen. Använd [VS-kod](https://code.visualstudio.com) eller en textredigerare för att redigera mallen.

#### <a name="edit-the-hub-name-and-location"></a>Redigera hubbnamn och plats

1. Ta bort parameteravsnittet högst upp - det är mycket enklare att bara använda hubbnamnet eftersom vi inte kommer att ha flera parametrar. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Ändra namnet så att det faktiska (nya) namnet används i stället för att hämta det från en parameter (som du tog bort i föregående steg). 

    För det nya navet använder du namnet på det ursprungliga navet plus *strängklonen* för att skapa det nya namnet. Börja med att rensa navnamnet och platsen.
    
    Gammal version:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Ny version: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Därefter kommer du att upptäcka att värdena för **sökvägen** innehåller det gamla navnamnet. Ändra dem så att de använder den nya. Det här är sökvägsvärdena under **eventHubEndpoints** som kallas **händelser** och **OperationsMonitoringEvents**.

    När du är klar bör avsnittet slutpunkter för händelsehubben se ut så här:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Uppdatera nycklarna för de routningsresurser som inte flyttas

När du exporterar resource manager-mallen för en hubb som har routning konfigurerad ser du att nycklarna för dessa resurser inte anges i den exporterade mallen – deras placering betecknas av asterisker. Du måste fylla i dem genom att gå till dessa resurser i portalen och hämta nycklarna **innan** du importerar den nya hubbens mall och skapar hubben. 

1. Hämta nycklarna som krävs för någon av routningsresurserna och placera dem i mallen. Du kan hämta nyckelerna från resursen i [Azure-portalen](https://portal.azure.com). 

   Om du till exempel dirigerar meddelanden till en lagringsbehållare letar du reda på lagringskontot i portalen. Under avsnittet Inställningar väljer du **Access-nycklar**och kopierar sedan en av nycklarna. Så här ser nyckeln ut när du exporterar mallen för första gången:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. När du har hämtat kontonyckeln för lagringskontot `AccountKey=****` placerar du den i mallen i satsen i stället för asterisker. 

1. För servicebussköer får du nyckeln delad åtkomst som matchar SharedAccessKeyName. Här är nyckeln `SharedAccessKeyName` och i json:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Detsamma gäller för servicebussavsnitt och eventnavanslutningar.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Skapa de nya routningsresurserna på den nya platsen

Det här avsnittet gäller bara om du flyttar de resurser som används av navet för routningslutpunkterna.

Om du vill flytta routningsresurserna måste du ställa in resurserna manuellt på den nya platsen. Du kan skapa routningsresurserna med hjälp av [Azure-portalen](https://portal.azure.com)eller genom att exportera Resource Manager-mallen för var och en av de resurser som används av meddelanderoutning, redigera dem och importera dem. När resurserna har konfigurerats kan du importera hubbens mall (som innehåller routningskonfigurationen).

1. Skapa varje resurs som används av operationsföljden. Du kan göra detta manuellt med hjälp av [Azure-portalen](https://portal.azure.com)eller skapa resurser med hjälp av Resource Manager-mallar. Om du vill använda mallar är det följande steg att följa:

    1. Exportera den till en Resource Manager-mall för varje resurs som används av operationsföljden.
    
    1. Uppdatera resursens namn och plats. 

    1. Uppdatera eventuella korsreferenser mellan resurserna. Om du till exempel skapar en mall för ett nytt lagringskonto måste du uppdatera lagringskontonamnet i mallen och alla andra mallar som refererar till det. I de flesta fall är routningsavsnittet i mallen för navet den enda andra mallen som refererar till resursen. 

    1. Importera var och en av mallarna som distribuerar varje resurs.

    När de resurser som används av operationsföljden har ställts in och körs kan du fortsätta.

1. I mallen för IoT-hubben ändrar du namnet på var och en av routningsresurserna till det nya namnet och uppdaterar platsen om det behövs. 

Nu har du en mall som skapar en ny hubb som ser nästan exakt ut som den gamla hubben, beroende på hur du bestämde dig för att hantera operationsföljden.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Flytta – skapa det nya navet i den nya regionen genom att läsa in mallen

Skapa den nya hubben på den nya platsen med hjälp av mallen. Om du har routningsresurser som ska flyttas ska resurserna ställas in på den nya platsen och referenserna i mallen uppdateras så att de matchar. Om du inte flyttar routningsresurserna bör de finnas i mallen med de uppdaterade nycklarna.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs**. 

1. I sökrutan placerar du i "malldistribution" och väljer Retur.

1. Välj **malldistribution (distribuera med anpassade mallar)**. Detta tar dig till en skärm för malldistributionen. Välj **Skapa**. Följande skärm visas:

   ![Skärmbild som visar kommandot för att skapa en egen mall](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Välj **Skapa en egen mall i redigeraren**, som gör att du kan ladda upp mallen från en fil. 

1. Välj **Läs in fil**. 

   ![Skärmbild som visar kommandot för att ladda upp en mallfil](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Bläddra efter den nya mallen som du redigerade och markera den och välj sedan **Öppna**. Mallen läses in i redigeringsfönstret. Välj **Spara**. 

   ![Skärmbild som visar inläsning av mallen](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Fyll i följande fält.

   **Prenumeration**: välj den prenumeration som ska användas.

   **Resursgrupp**: skapa en ny resursgrupp på en ny plats. Om du redan har konfigurerat en ny kan du markera den i stället för att skapa en ny.

   **Plats**: Om du har valt en befintlig resursgrupp fylls detta i så att du kan matcha resursgruppens plats. Om du har skapat en ny resursgrupp är det här dess plats.

   **Jag håller kryssruta:** detta säger i princip att du går med på att betala för de resurser (s) du skapar.

1. Välj knappen **Köp.**

Portalen validerar nu mallen och distribuerar den klonade hubben. Om du har routningskonfigurationsdata inkluderas den i den nya hubben, men pekar på resurserna på den tidigare platsen.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Hantera de enheter som är registrerade i IoT-hubben

Nu när du har din klon igång, måste du kopiera alla enheter från den ursprungliga navet till klonen. 

Det finns flera sätt att åstadkomma detta. Du använde antingen ursprungligen [DPS (Device Provisioning Service)](/azure/iot-dps/about-iot-dps)för att etablera enheterna eller så gjorde du inte det. Om du gjorde det, är detta inte svårt. Om du inte gjorde det kan detta vara mycket komplicerat. 

Om du inte använde DPS för att etablera dina enheter kan du hoppa över nästa avsnitt och börja med [Använda import/export för att flytta enheterna till den nya hubben](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Använda DPS för att återetablering av enheterna i det nya navet

Information om hur du använder DPS för att flytta enheterna till den nya platsen finns i [Så här etablerar du om enheter](../iot-dps/how-to-reprovision.md). När du är klar kan du visa enheterna i [Azure-portalen](https://portal.azure.com) och verifiera att de finns på den nya platsen.

Gå till den nya hubben med [Azure-portalen](https://portal.azure.com). Välj din hubb och välj sedan **IoT-enheter**. Du ser de enheter som har återupprättats till den klonade hubben. Du kan också visa egenskaperna för den klonade hubben. 

Om du har implementerat routning testar och kontrollerar du att dina meddelanden dirigeras till resurserna korrekt.

### <a name="committing-the-changes-after-using-dps"></a>Genomföra ändringarna efter att ha använt DPS

Den här ändringen har begåtts av DPS-tjänsten.

### <a name="rolling-back-the-changes-after-using-dps"></a>Återställa ändringarna efter att ha använt DPS. 

Om du vill återställa ändringarna återupprättar du enheterna från den nya hubben till den gamla.

Du har nu migrerat navet och dess enheter. Du kan hoppa till [Rensa .](#clean-up)

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Använda Import-Export för att flytta enheterna till det nya navet

Programmet riktar sig till .NET Core, så att du kan köra den på antingen Windows eller Linux. Du kan hämta exemplet, hämta anslutningssträngarna, ange de flaggor som du vill köra för och köra det. Du kan göra detta utan att någonsin öppna koden.

### <a name="downloading-the-sample"></a>Ladda ned exemplet

1. Använd ioT C#-exemplen från den här sidan: [Azure IoT Samples for C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Ladda ner zip-filen och packa upp den på din dator. 

1. Den relevanta koden finns i ./iot-hub/Samples/service/ImportExportDevicesSample. Du behöver inte visa eller redigera koden för att kunna köra programmet.

1. Om du vill köra programmet anger du tre anslutningssträngar och fem alternativ. Du skickar in dessa data som kommandoradsargument eller använder miljövariabler, eller använder en kombination av de två. Vi skickar alternativen som kommandoradsargument och anslutningssträngarna som miljövariabler. 

   Anledningen till detta är att anslutningssträngarna är långa och ungainly, och osannolikt att ändra, men du kanske vill ändra alternativen och köra programmet mer än en gång. Om du vill ändra värdet för en miljövariabel måste du stänga kommandofönstret och Visual Studio eller VS-kod, beroende på vilket du använder. 

### <a name="options"></a>Alternativ

Här är de fem alternativ som du anger när du kör programmet. Vi sätter de här på kommandoraden om en minut.

*   **addDevices** (argument 1) – ställ in detta på true om du vill lägga till virtuella enheter som genereras åt dig. Dessa läggs till i källhubben. Ange också **numToAdd** (argument 2) för att ange hur många enheter du vill lägga till. Det maximala antalet enheter som du kan registrera till ett nav är en miljon. Syftet med det här alternativet är att testa – du kan generera ett visst antal enheter och sedan kopiera dem till ett annat nav.

*   **copyDevices** (argument 3) – ställ in det på true för att kopiera enheterna från ett nav till ett annat. 

*   **deleteSourceDevices** (argument 4) – ange detta till true för att ta bort alla enheter som är registrerade i källhubben. Vi rekommenderar att du väntar tills du är säker på att alla enheter har överförts innan du kör detta. När du har tagit bort enheterna kan du inte få tillbaka dem.

*   **deleteDestDevices** (argument 5) – ange detta till true för att ta bort alla enheter som är registrerade till målhubben (klonen). Du kanske vill göra detta om du vill kopiera enheterna mer än en gång. 

Det grundläggande kommandot kommer att *dotnet köra* - detta talar om .NET att bygga den lokala csproj filen och sedan köra den. Du lägger till kommandoradsargumenten i slutet innan du kör det. 

Kommandoraden ser ut så här:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Använda miljövariabler för anslutningssträngarna

1. För att köra exemplet behöver du anslutningssträngarna till gamla och nya IoT-hubbar och till ett lagringskonto som du kan använda för tillfälliga arbetsfiler. Vi kommer att lagra värdena för dessa i miljövariabler.

1. Logga in på [Azure-portalen](https://portal.azure.com)om du vill hämta värdena för anslutningssträngen . 

1. Placera anslutningssträngarna någonstans där du kan hämta dem, till exempel Anteckningar. Om du kopierar följande kan du klistra in anslutningssträngarna direkt där de går. Lägg inte till blanksteg runt likhetstecknet eller ändra variabelnamnet. Du behöver inte heller dubbla citattecken runt anslutningssträngarna. Om du placerar offerter runt anslutningssträngen för lagringskontot fungerar det inte.

   För Windows är det så här du ställer in miljövariabler:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   För Linux är det så här du definierar miljövariabler:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. För IoT-hubbanslutningssträngarna går du till varje hubb i portalen. Du kan söka i **Resurser** efter navet. Om du känner till resursgruppen kan du gå till **Resursgrupper,** välja resursgrupp och sedan välja navet i listan över tillgångar i resursgruppen. 

1. Välj Principer för **delad åtkomst** i inställningarna för navet och välj sedan **iothubowner** och kopiera en av anslutningssträngarna. Gör samma sak för målhubben. Lägg till dem i lämpliga SET-kommandon.

1. Leta reda på lagringskontots anslutningssträng i **resursgruppen** eller under resursgruppen och öppna det för **anslutningssträngen** för lagringskontot. 
   
1. Under avsnittet Inställningar väljer du **Access-nycklar** och kopierar en av anslutningssträngarna. Placera anslutningssträngen i textfilen för lämpligt SET-kommando. 

Nu har du miljövariabler i en fil med SET-kommandona och du vet vad kommandoradsargumenten är. Vi kör provet.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Köra exempelprogrammet och använda kommandoradsargument

1. Öppna ett kommandotolksfönster. Välj Windows och `command prompt` skriv in för att hämta kommandotolksfönstret.

1. Kopiera de kommandon som anger miljövariabler, en i taget, och klistra in dem i kommandotolksfönstret och välj Retur. När du är klar `SET` skriver du i kommandotolksfönstret för att se dina miljövariabler och deras värden. När du har kopierat dessa till kommandotolksfönstret behöver du inte kopiera dem igen, såvida du inte öppnar ett nytt kommandotolksfönster.

1. I kommandotolksfönstret ändrar du kataloger tills du befinner dig i ./ImportExportDevicesSample (där filen ImportExportDevicesSample.csproj finns). Skriv sedan följande och inkludera kommandoradsargumenten.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Kommandot dotnet bygger och kör programmet. Eftersom du skickar in alternativen när du kör programmet kan du ändra värdena för dem varje gång du kör programmet. Du kanske till exempel vill köra den en gång och skapa nya enheter, sedan köra den igen och kopiera dessa enheter till en ny hubb och så vidare. Du kan också utföra alla steg i samma körning, även om vi rekommenderar att du inte tar bort några enheter förrän du är säker på att du är klar med kloningen. Här är ett exempel som skapar 1 000 enheter och sedan kopierar dem till den andra hubben.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    När du har kontrollerat att enheterna har kopierats kan du ta bort enheterna från källhubben så här:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Köra exempelprogrammet med Visual Studio

1. Om du vill köra programmet i Visual Studio ändrar du den aktuella katalogen till mappen där filen IoTHubServiceSamples.sln finns. Kör sedan det här kommandot i kommandotolksfönstret för att öppna lösningen i Visual Studio. Du måste göra detta i samma kommandofönster där du anger miljövariabler, så att dessa variabler är kända.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Högerklicka på projektet *ImportExportDevicesSample* och välj **Ange som startprojekt**.    
    
1. Ange variablerna högst upp i Program.cs i mappen ImportExportDevicesSample för de fem alternativen.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Välj F5 för att köra programmet. När den har körts klart kan du visa resultatet.

### <a name="view-the-results"></a>Visa resultatet 

Du kan visa enheterna i [Azure-portalen](https://portal.azure.com) och verifiera att de finns på den nya platsen.

1. Gå till den nya hubben med [Azure-portalen](https://portal.azure.com). Välj din hubb och välj sedan **IoT-enheter**. Du ser de enheter som du just kopierade från det gamla navet till den klonade hubben. Du kan också visa egenskaperna för den klonade hubben. 

1. Sök efter import-/exportfel genom att gå till Azure-lagringskontot `ImportErrors.log`i [Azure-portalen](https://portal.azure.com) och leta i behållaren `devicefiles` efter . Om den här filen är tom (storleken är 0) uppstod inga fel. Om du försöker importera samma enhet mer än en gång avvisas enheten andra gången och ett felmeddelande läggs till i loggfilen.

### <a name="committing-the-changes"></a>Begå ändringarna 

Nu har du kopierat hubben till den nya platsen och migrerat enheterna till den nya klonen. Nu måste du göra ändringar så att enheterna fungerar med den klonade hubben.

För att genomföra ändringarna, här är de steg du behöver för att utföra: 

* Uppdatera varje enhet för att ändra värdnamnet för IoT Hub så att värdnamnet för IoT Hub pekar på det nya navet. Du bör göra detta med samma metod som du använde när du först etablerade enheten.

* Ändra alla program som du har som refererar till den gamla hubben för att peka på den nya hubben.

* När du är klar ska det nya navet vara igång. Den gamla hubben ska inte ha några aktiva enheter och vara i frånkopplat tillstånd. 

### <a name="rolling-back-the-changes"></a>Återställa ändringarna

Om du bestämmer dig för att återställa ändringarna, här är stegen för att utföra:

* Uppdatera varje enhet för att ändra IoT Hub Hostname för att peka på IoT Hub Hostname för den gamla hubben. Du bör göra detta med samma metod som du använde när du först etablerade enheten.

* Ändra alla program som du har som refererar till den nya hubben för att peka på den gamla hubben. Om du till exempel använder Azure Analytics kan du behöva konfigurera om din [Azure Stream Analytics-indata](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Ta bort den nya hubben. 

* Om du har routningsresurser bör konfigurationen på den gamla hubben fortfarande peka på rätt routningskonfiguration och bör arbeta med dessa resurser när navet har startats om.

### <a name="checking-the-results"></a>Kontrollera resultaten 

Om du vill kontrollera resultatet ändrar du IoT-lösningen så att den pekar på hubben på den nya platsen och kör den. Med andra ord utför du samma åtgärder med den nya hubben som du utförde med föregående hubb och kontrollerar att de fungerar korrekt. 

Om du har implementerat routning testar och kontrollerar du att dina meddelanden dirigeras till resurserna korrekt.

## <a name="clean-up"></a>Sanering

Rensa inte tills du verkligen är säker på att det nya navet är igång och enheterna fungerar korrekt. Se också till att testa routningen om du använder den funktionen. När du är redo kan du rensa de gamla resurserna genom att utföra följande steg:

* Om du inte redan har gjort det tar du bort den gamla hubben. Detta tar bort alla aktiva enheter från navet.

* Om du har routningsresurser som du har flyttat till den nya platsen kan du ta bort de gamla routningsresurserna.

## <a name="next-steps"></a>Nästa steg

Du har klonat ett IoT-nav till ett nytt nav i en ny region, komplett med enheterna. Mer information om hur du utför massåtgärder mot identitetsregistret i en IoT Hub finns [i Importera och exportera IoT Hub-enhetsidentiteter i bulk](iot-hub-bulk-identity-mgmt.md).

Mer information om IoT Hub och utveckling för navet finns i följande artiklar.

* [Guide för IoT Hub-utvecklare](iot-hub-devguide.md)

* [Självstudiekurs för IoT-hubbdirigering](tutorial-routing.md)

* [Översikt över hantering av IoT Hub-enheter](iot-hub-device-management-overview.md)

* Om du vill distribuera exempelprogrammet läser du [.NET Core-programdistributionen](https://docs.microsoft.com/dotnet/core/deploying/index).
