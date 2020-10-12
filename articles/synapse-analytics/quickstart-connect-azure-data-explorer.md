---
title: 'Snabb start: ansluta Azure Datautforskaren till en Synapse-arbetsyta'
description: Så här ansluter du ett Azure Datautforskaren-kluster till en Synapse-arbetsyta med Azure Synapse Apache Spark
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 53092f5c49073098f28d2fd06f38391e858b2b9d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946425"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>Ansluta till Azure Datautforskaren med Synapse Apache Spark

Den här artikeln beskriver hur du kommer åt Azure Datautforskaren-databaser från Synapse Studio med Synapse Apache Spark. 

## <a name="prerequisites"></a>Förutsättningar

* [Skapa ett Azure datautforskaren-kluster och-databas](/azure/data-explorer/create-cluster-database-portal).
* Befintlig arbets yta i Synapse eller skapa en ny arbets yta efter den här [snabb](./quickstart-create-workspace.md) starten 
* Befintlig Synapse Apache Spark pool eller skapa en ny pool efter den här [snabb](./quickstart-create-apache-spark-pool-portal.md) starten
* [Skapa en Azure AD-app genom att tillhandahålla ett Azure AD-program.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Ge din Azure AD-app åtkomst till din databas genom att [Hantera behörigheter för azure datautforskaren Database](/azure/data-explorer/manage-database-permissions)

## <a name="navigate-to-synapse-studio"></a>Gå till Synapse Studio

Från en Synapse-arbetsyta väljer du **Starta Synapse Studio**. På Start sidan för Synapse Studio väljer du **data**, vilket leder dig till **data Object Explorer**.

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>Ansluta en Azure Datautforskaren-databas till en Synapse-arbetsyta

Att ansluta en Azure Datautforskaren-databas till en arbets yta görs via den länkade tjänsten. Med den länkade tjänsten Azure Data Explorer kan användare söka efter och utforska data, läsa och skriva från Apache Spark för Azure Synapse Analytics och köra integrations jobb i en pipeline.

Från data Object Explorer följer du stegen för att ansluta ett Azure Datautforskaren-kluster direkt:

1. Välj **+** ikon nära data
2. Välj **Anslut** till externa data
3. Välj **Azure-datautforskaren (Kusto)**
5. Välj **Fortsätt**
6. Namnge den länkade tjänsten. Namnet visas i Object Explorer och används av Synapse körnings tider för att ansluta till databasen. Vi rekommenderar att du använder ett eget namn
7. Välj Azure Data utforska kluster från din prenumeration eller ange URI: n.
8. Ange "tjänstens huvud namns-ID" och "tjänstens huvud namn" (kontrol lera att tjänstens huvud namn har åtkomst till databasen för Läs-och Skriv behörighet för att mata in data)
9. Ange namnet på Azure Datautforskaren-databasen
10. Klicka på **Testa anslutning** för att se till att du har rätt behörigheter
11. Välj **Skapa**

    ![Ny länkad tjänst](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > Valfritt Test anslutningen verifierar inte skriv åtkomst, se till att tjänstens huvud namns-ID har Skriv behörighet till Azure Datautforskaren-databasen.

12. Azure Datautforskaren-kluster och databaser visas under den  **länkade** fliken i Azure datautforskaren-avsnittet. 

    ![Bläddra bland kluster](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > I den aktuella versionen fylls databas objekt i baserat på ditt AAD-kontos behörigheter på Azure Datautforskaren-databaserna. När du kör Apache Spark antecknings böcker eller integrations jobb används autentiseringsuppgiften i länk tjänsten (dvs. tjänstens huvud namn).


## <a name="quickly-interact-with-code-generated-actions"></a>Interagera snabbt med kod genererade åtgärder

* När du högerklickar på en databas eller tabell har du en lista med gester som utlöser en exempel Spark-anteckningsbok för att läsa data, skriva data, strömma data till Azure Datautforskaren. 
    [![Nya exempel antecknings böcker](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* Här är ett exempel på att läsa data. Bifogade antecknings boken till Spark-poolen och kör cellen [ ![ ny Läs antecknings bok](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE] 
   > Första körningen kan ta mer än tre minuter att initiera Spark-sessionen. Efterföljande körningar blir betydligt snabbare.  


## <a name="limitations"></a>Begränsningar
Azure Datautforskaren Connector stöds för närvarande inte med Azure Synapse Managed VNET.


## <a name="next-steps"></a>Nästa steg

* [Exempel kod med avancerade alternativ](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Datautforskaren (Kusto) Spark-anslutning](https://github.com/Azure/azure-kusto-spark)