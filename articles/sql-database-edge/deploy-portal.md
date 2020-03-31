---
title: Distribuera förhandsversionen av SQL Database Edge med Azure-portalen | Microsoft-dokument
description: Lär dig hur du distribuerar Azure SQL Database Edge med Azure-portalen
keywords: distribuera sql-databaskant
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246729"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Distribuera förhandsversionen av Azure SQL Database Edge

Azure SQL Database Edge Preview är en relationsdatabasmotor som är optimerad för IoT- och Azure IoT Edge-distributioner. Det ger funktioner för att skapa ett högpresterande datalagrings- och bearbetningslager för IoT-program och lösningar. Den här snabbstarten visar hur du kommer igång med att skapa en Azure SQL Database Edge-modul via Azure IoT Edge med Azure-portalen.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Logga in på [Azure-portalen](https://portal.azure.com/).
* Skicka en begäran [här](https://azure.microsoft.com/services/sql-database-edge/#contact)om du vill att prenumerationen ska vara aktiverad för distribution av SQL Database Edge.
* Skapa en [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Registrera en [IoT Edge-enhet från Azure-portalen](../iot-edge/how-to-register-device-portal.md).
* Förbered IoT Edge-enheten för att [distribuera IoT Edge-modulen från Azure-portalen](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Information om hur du distribuerar en Virtuell Azure Linux-dator som en IoT Edge-enhet finns i den här [snabbstartsguiden](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Distribuera SQL Database Edge-modul från Azure Marketplace

Azure Marketplace är en marknadsplats för onlineprogram och tjänster där du kan bläddra igenom ett brett utbud av företagsprogram och lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge-moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Database Edge kan distribueras till en kantenhet via marketplace.

1. Hitta Azure SQL Database Edge-modulen på Azure Marketplace.<br><br>

   ![SQL Database Edge på MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Välj den programplan som bäst matchar dina krav och klicka på **Skapa**. <br><br>

   ![Välj rätt programvaruplan](media/deploy-portal/pick-correct-plan.png)

3. På sidan Målenheter för IoT-edgemodul anger du följande information och klickar sedan på **Skapa**

   |**Field**  |**Beskrivning**  |
   |---------|---------|
   |Prenumeration  |  Azure-prenumerationen som IoT Hub skapades under skapades |
   |IoT Hub   |  Namn på IoT Hub där IoT Edge-enheten är registrerad och välj sedan alternativet "Distribuera till en enhet"|
   |Namn på IoT-kantenhet  |  Namnet på IoT Edge-enheten där SQL Database Edge skulle distribueras |

4. På sidan **Ange moduler navigerar** du till avsnittet om distributionsmoduler och klickar på **Konfigurera** mot SQL Database Edge-modulen. 

5. I fönstret **Anpassade moduler i IoT Edge** anger du önskade värden för miljövariablerna och/eller anpassar modulens skapa alternativ och önskade egenskaper. En fullständig lista över miljövariabler som stöds finns [variabler för SQL Server Container Environment](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametern**  |**Beskrivning**|
   |---------|---------|
   | Namn | Namn på modulen. |
   |SA_PASSWORD  | Ange ett starkt lösenord för administrationskontot för SQL Database Edge. |
   |MSSQL_LCID   | Anger vilket språk-ID som ska användas för SQL Server. Till exempel är 1036 franska. |
   |MSSQL_COLLATION | Anger standardsortering för SQL Server. Den här inställningen åsidosätter standardmappningen av språk-ID (LCID) till sortering. |

   > [!NOTE]
   > Ändra eller uppdatera inte **bild-URI:n** eller **ACCEPT_EULA** inställningarna på modulen.

6. Uppdatera önskade värde för behållaren för att skapa önskade värde för **värdporten**i fönstret **Anpassade moduler i IoT Edge** . Om du behöver distribuera mer än en SQL DB Edge-modul, se till att uppdatera monteringsalternativet för att skapa en ny källa & målpar för den beständiga volymen. Mer information om fästen och volym finns [i Använd volymer i](https://docs.docker.com/storage/volumes/) dockerdokumentationen. 

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

7. I fönstret **Anpassade moduler i IoT Edge** uppdaterar du ange *modultvillingens önskade egenskaper* så att de innehåller platsen för SQL-paketet och jobbinformationen för strömanalys. Dessa två fält är valfria och bör användas om du vill distribuera SQL Database Edge-modulen med en databas och ett direktuppspelningsjobb.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. I fönstret **Anpassade moduler i IoT Edge** ställer du in *omstartsprincipen* till alltid och *önskad status* för att köras.
9. Klicka på **Spara**i fönstret **Anpassade moduler för IoT Edge.**
10. Klicka på **Nästa**på sidan **Ange moduler** .
11. På **sidan** **Ange flöde (valfritt)** anger du vägarna för modul till modul till IoT Edge Hub-kommunikation se [Distribuera moduler och upprätta vägar i IoT Edge](../iot-edge/module-composition.md).
12. Klicka på **Nästa**.
13. Klicka på **Skicka**.

I den här snabbstarten har du distribuerat en SQL Database Edge-modul på en IoT Edge-enhet.

## <a name="next-steps"></a>Efterföljande moment

- [Maskininlärning och artificiell intelligens med ONNX i SQL Database Edge](onnx-overview.md).
- Skapa en end to end IoT-lösning med SQL Database Edge med IoT Edge.
