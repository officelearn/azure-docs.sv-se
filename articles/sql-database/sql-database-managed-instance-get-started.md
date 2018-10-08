---
title: 'Azure Portal: Skapa en hanterad SQL-instans | Microsoft Docs'
description: Skapa en hanterad SQL-instans, en nätverksmiljö och en virtuell klientdator för åtkomst.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: c0c249ffe426e86049024122d9cbf786bb677220
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160646"
---
# <a name="create-an-azure-sql-database-managed-instance"></a>Skapa en hanterad Azure SQL Database-instans

I den här snabbstarten går vi igenom hur du skapar en [hanterad instans](sql-database-managed-instance.md) av Azure SQL Database i Azure-portalen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

I följande steg visas hur du skapar en hanterad instans.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp **Managed Instance** och välj sedan **Azure SQL Managed Instance**.
3. Klicka på **Skapa**.

   ![Skapa en hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Fyll i formuläret för den hanterade instansen med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   | **Prenumeration** | Din prenumeration | En prenumeration där du har behörighet att skapa nya resurser |
   |**Namn på hanterad instans**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörsinloggning för hanterad instans**|Giltigt användarnamn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.| 
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Resursgrupp**|En ny eller befintlig resursgrupp|Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Plats**|Platsen där du vill skapa den hanterade instansen|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Virtuellt nätverk**|Välj antingen **Skapa nytt virtuellt nätverk** eller ett virtuellt nätverk som du skapat tidigare i den resursgrupp som du angav tidigare i det här formuläret| Information om att konfigurera ett virtuellt nätverk för en hanterad instans med anpassade inställningar finns i [Konfigurera en mall för den virtuella nätverksmiljön i SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) i Github. Information om kraven för att konfigurera nätverksmiljön för en hanterad instans finns i [Konfigurera ett virtuellt nätverk för Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md) |

   ![formulär för hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Klicka på **Prisnivå** för att beräkna storlek på instanser och lagringsresurser samt granska alternativen för prisnivå. Standardvärdet är prisnivån Generell användning med 32 GB minne och 16 virtuella kärnor.
6. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor. 
7. När du är klar klickar du på **Verkställ** för att spara ditt val.  
8. Klicka på **Skapa** för att distribuera den hanterade instansen.
9. Klicka på **ikonen för meddelanden** för att visa status för distributionen.

    ![distributionsförlopp för hanterad instans](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. Klicka på **Distribution pågår** för att öppna fönstret för den hanterade instansen om du vill fortsätta övervaka förloppet för distributionen. 

> [!IMPORTANT]
> För den första instansen i ett undernät tar distributionen vanligtvis mycket längre tid än för efterföljande instanser. Avbryt inte distributionen om den tar längre tid än förväntat. Det tar bara några minuter att skapa den andra hanterade instansen i undernätet.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Granska resurser och hämta det fullständigt kvalificerade servernamnet

När distributionen är färdig kan du granska resurserna som har skapats och hämta det fullständigt kvalificerade servernamnet för användning i senare snabbstarter.

1. Öppna resursgruppen för din hanterade instans och visa resurserna som skapades åt dig i snabbstarten [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).

   ![Hanterade instansresurser](./media/sql-database-managed-instance-get-started/resources.png)Öppna din hanterade instansresurs i Azure-portalen.

2. Klicka på din hanterade instans.
3. På fliken **Översikt** letar du upp egenskapen **Värd** och kopierar den hanterade instansens fullständiga värdadress.


   ![Hanterade instansresurser](./media/sql-database-managed-instance-get-started/host-name.png)

   Namnet ser ut ungefär så här: **din_dator.neu15011648751ff.database.windows.net**.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du ansluter till en hanterad instans i:
  - En översikt över anslutningsalternativen för olika program finns i [Ansluta dina program till Managed Instance](sql-database-managed-instance-connect-app.md).
  - [Konfigurera en anslutning till en virtuell Azure-dator](sql-database-managed-instance-configure-vm.md) är en snabbstart där du får se hur du ansluter till en hanterad instans från en virtuell Azure-dator.
  - [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) är en snabbstart där du får se hur du ansluter till en hanterad instans från en lokal klientdator via punkt-till-plats-anslutning.
- Om du vill återställa en lokal befintlig SQL Server-databas till en hanterad instans kan du använda [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) till att återställa från en databassäkerhetskopia eller kommandot [T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) till att återställa från en databassäkerhetskopia.
