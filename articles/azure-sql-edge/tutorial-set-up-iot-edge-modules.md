---
title: Konfigurera IoT Edge moduler i Azure SQL Edge
description: I del två av den här tre delen av Azure SQL Edge-självstudierna för förutsägelse av järn Malms orenheter, konfigurerar du IoT Edge moduler och anslutningar.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235023"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Konfigurera IoT Edge moduler och anslutningar

I del två av den här självstudien i tre delar om förutsägelse av järn Malms orenheter i Azure SQL Edge ställer du in följande IoT Edge moduler:

- Azure SQL Edge
- IoT Edge modul för data Generator

## <a name="create-azure-stream-analytics-module"></a>Skapa Azure Stream Analytics modul

Skapa en Azure Stream Analytics-modul som ska användas i den här självstudien. Mer information om hur du använder strömnings jobb med SQL Edge finns i [använda strömnings jobb med SQL Edge](stream-analytics.md).

När Azure Stream Analytics jobb har skapats med värd miljön inställd som Edge, ställer du in indata och utdata för självstudien.

1. Skapa **indata**genom att klicka på **+ Lägg till strömmande data**. Fyll i avsnittet information med följande information:

   Fält|Värde
   -----|-----
   Format för händelse serialisering|JSON
   Kodning|UTF-8
   Händelse komprimerings typ|Ingen

2. Skapa **utdata**genom att klicka på **+ Lägg till** och välja SQL Database. Fyll i avsnittet information med följande information.

   > [!NOTE]
   > Lösen ordet som anges i det här avsnittet måste anges för SQL SA-lösenord när du distribuerar SQL Edge-modulen i avsnittet **Distribuera Azure SQL Edge-modulen**.

   Fält|Värde
   -----|-----
   Databas|IronOreSilicaPrediction
   servernamn|TCP:., 1433
   Användarnamn|sa
   lösenordsinställning|Ange ett starkt lösen ord
   Tabell|IronOreMeasurements1

3. Navigera till avsnittet **fråga** och Ställ in frågan enligt följande:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. Under **Konfigurera**väljer du **publicera**och väljer sedan knappen **publicera** . Spara SAS-URI: n för användning med modulen SQL Database Edge.

## <a name="specify-container-registry-credentials"></a>Ange autentiseringsuppgifter för container registret

Autentiseringsuppgifterna för behållar register som är värdar för modulblad måste anges. Dessa finns i behållar registret som skapades i din resurs grupp. Navigera till avsnittet **åtkomst nycklar** . Anteckna följande fält:

- Registernamn
- Inloggningsserver
- Användarnamn
- lösenordsinställning

Ange nu autentiseringsuppgifter för behållare i IoT Edge-modulen.

1. Navigera till IoT-hubben som skapades i din resurs grupp.

2. I avsnittet **IoT Edge** under **Automatisk enhets hantering**klickar du på **enhets-ID**. I den här självstudien är ID: t `IronOrePredictionDevice` .

3. Välj avsnittet **Ange moduler** .

4. Ange följande värden under **container Registry autentiseringsuppgifter**:

   _Fält_|_Värde_
   -------|-------
   Name|Registernamn
   Adress|Inloggningsserver
   Användarnamn|Användarnamn
   lösenordsinställning|lösenordsinställning
  
## <a name="deploy-the-data-generator-module"></a>Distribuera modulen data Generator

1. I avsnittet **IoT Edge moduler** klickar du på **+ Lägg till** och väljer **IoT Edge modul**.

2. Ange ett namn på IoT Edge modul och avbildnings-URI.
   Avbildnings-URI: n finns i behållar registret i resurs gruppen. Välj avsnittet **databaser** under **tjänster**. I den här självstudien väljer du den lagrings plats som heter `silicaprediction` . Välj lämplig tagg. Bild-URI: n kommer att ha formatet:

   *inloggnings Server för containerregistry* / *namn på databas*:*taggnamn*

   Ett exempel:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Klicka på **Lägg till**.

## <a name="deploy-the-azure-sql-edge-module"></a>Distribuera Azure SQL Edge-modulen

1. Distribuera Azure SQL Edge-modulen genom att följa stegen som beskrivs i [Distribuera Azure SQL Edge (för hands version)](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

2. På sidan **Ange väg** på sidan **Ange moduler** anger du vägarna för modulen till IoT Edge Hub-kommunikationen på följande sätt. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Ett exempel:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. I **modulens dubbla** inställningar ser du till att SQLPackage och ASAJonInfo uppdateras med relevanta SAS-URL: er som du sparade tidigare i självstudien.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Efterföljande moment

- [Distribuera ML-modell på Azure SQL Edge med ONNX](tutorial-run-ml-model-on-sql-edge.md)
