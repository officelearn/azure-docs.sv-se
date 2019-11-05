---
title: Distribuera SQL Database Edge Preview med hjälp av Azure Portal | Microsoft Docs
description: Lär dig hur du distribuerar Azure SQL Database Edge med hjälp av Azure Portal
keywords: Distribuera SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514100"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Distribuera Azure SQL Database Edge-förhandsgranskning

Azure SQL Database Edge Preview är en Relations databas motor som är optimerad för IoT-och Azure IoT Edge-distributioner. Det innehåller funktioner för att skapa ett högpresterande lagrings-och bearbetnings lager för IoT-program och-lösningar. Den här snabb starten visar hur du kommer igång med att skapa en Azure SQL Database Edge-modul via Azure IoT Edge att använda Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Logga in på [Azure-portalen](https://portal.azure.com/).
* Skicka en begäran [här](https://azure.microsoft.com/services/sql-database-edge/#contact), om du vill att prenumerationen ska vara aktive rad för distribution av SQL Database Edge.
* Skapa ett [Azure-IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Registrera en [IoT Edge-enhet från Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Förbered IoT Edge-enheten för att [distribuera IoT Edge modul från Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Om du vill distribuera en virtuell Azure Linux-dator som en IoT Edge enhet, se den här [snabb starts guiden](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Distribuera SQL Database Edge-modul från Azure Marketplace

Azure Marketplace är ett online-program och tjänster för tjänster där du kan bläddra igenom en rad olika företags program och-lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Database Edge kan distribueras till en Edge-enhet via Marketplace.

1. Hitta Azure SQL Database Edge-modulen på Azure Marketplace.<br><br>

   ![SQL Database Edge i MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Välj den program varu plan som bäst motsvarar dina krav och klicka på **skapa**. <br><br>

   ![Välj rätt program varu plan](media/deploy-portal/pick-correct-plan.png)

3. På sidan mål enheter för IoT Edge modul anger du följande information och klickar sedan på **skapa**

   |**Fält**  |**Beskrivning**  |
   |---------|---------|
   |Prenumeration  |  Azure-prenumerationen som IoT Hub skapades under |
   |IoT Hub   |  Namnet på IoT Hub där IoT Edges enheten är registrerad och välj sedan alternativet för att distribuera till en enhet|
   |IoT Edge enhets namn  |  Namnet på den IoT Edge enhet där SQL Database kant skulle distribueras |

4. På sidan **Ange moduler** navigerar du till avsnittet om distributions moduler och klickar på **konfigurera** mot modulen SQL Database Edge. 

5. I fönstret **IoT Edge anpassade moduler** anger du önskade värden för miljövariablerna och/eller anpassar alternativen för att skapa och önskade egenskaper för modulen. En fullständig lista över miljövariabler som stöds refererar [SQL Server behållar miljö variabler](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**ProfileServiceApplicationProxy**  |**Beskrivning**|
   |---------|---------|
   | Namn | Namn för modulen. |
   |SA_PASSWORD  | Ange ett starkt lösen ord för SQL Database gräns administratörs kontot. |
   |MSSQL_LCID   | Anger språk-ID: t som ska användas för SQL Server. Till exempel är 1036 franska. |
   |MSSQL_COLLATION | Ställer in standard sortering för SQL Server. Den här inställningen åsidosätter standard mappningen för språk-ID (LCID) till sortering. |

   > [!NOTE]
   > Ändra eller uppdatera inte **bild-URI** eller **ACCEPT_EULA** -inställningar i modulen.

6. I fönstret **IoT Edge anpassade moduler** uppdaterar du det önskade värdet för behållarens skapa alternativ för **värd porten** och **målet** för monterings punkterna. Monterings punktens mål är där SQL-databasfilerna lagras på värden IoT Edge enhet.

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. I fönstret **IoT Edge anpassade moduler** uppdaterar du *önskade egenskaper för set-modulen* för att inkludera platsen för SQL-paketet och informationen om Stream Analytics-jobbet. Dessa två fält är valfria och ska användas om du vill distribuera modulen SQL Database Edge med en databas och ett strömmande jobb.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. I fönstret **IoT Edge anpassade moduler** anger du *starta om principen* till alltid och *önskad status* för att köra.
9. I fönstret **IoT Edge anpassade moduler** klickar du på **Spara**.
10. Klicka på **Nästa**på sidan **Ange moduler** .
11. På sidan **Ange väg (valfritt)** på sidan **Ange moduler** anger du vägarna för modulen till modulen eller modulen för att IoT Edge Hub-kommunikation se [distribuera moduler och upprätta vägar i IoT Edge](../iot-edge/module-composition.md).
12. Klicka på **Nästa**.
13. Klicka på **Skicka**.

I den här snabb starten har du distribuerat en SQL Database Edge-modul på en IoT Edge enhet.

## <a name="next-steps"></a>Nästa steg

- [Machine Learning och artificiell intelligens med ONNX i SQL Database Edge](onnx-overview.md).
- Skapa en komplett IoT-lösning med SQL Database Edge med hjälp av IoT Edge.