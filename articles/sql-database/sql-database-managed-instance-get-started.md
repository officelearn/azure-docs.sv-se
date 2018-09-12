---
title: 'Azure Portal: Skapa en SQL-hanterad instans | Microsoft Docs'
description: Skapa en SQL-hanterad instans, en nätverksmiljö och en virtuell klientdator för åtkomst.
keywords: sql database tutorial, create a sql managed instance
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/31/2018
ms.author: jovanpop-msft
ms.openlocfilehash: 4271f0cef31b0e028ed1f9408166c37d4cbbe109
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382006"
---
# <a name="create-an-azure-sql-managed-instance"></a>Skapa en Azure SQL-hanterad instans

Den här snabbstarten beskriver hur du skapar en SQL-hanterad instans i Azure. Azure SQL Database Managed Instance är en PaaS-baserad (Platform-as-a-Service) instans av en SQL Server-databasmotor som gör att du kan köra och skala SQL Server-databaser med hög tillgänglighet i Azure-molnet. Den här snabbstarten beskriver hur du kommer igång med att skapa en SQL-hanterad instans.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>Förbereda nätverksmiljön

SQL Managed Instance är en säker tjänst som placeras i ditt virtuella Azure-nätverk (VNet). Innan du kan skapa en hanterad instans måste du förbereda Azure-nätverksmiljön, med bland annat följande:
 - Virtuellt Azure-nätverk där den hanterade instansen ska placeras.
 - Undernät i det virtuella Azure-nätverket där hanterade instanser ska placeras.
 - Användardefinierad väg som gör att hanterade instanser kan kommunicera med de Azure-tjänster som styr och hanterar instanserna.

Undernätet är dedikerat för hanterade instanser och du kan inte skapa andra resurser (till exempel virtuella Azure-datorer) i det undernätet. I den här snabbstarten skapar du två undernät i ditt virtuella Azure-nätverk, så att du kan placera hanterade instanser i undernätet som är dedikerat för hanterade instanser, och andra resurser i standardundernätet.

1. Distribuera Azure-nätverksmiljön som förberetts för Azure SQL Database Managed Instance genom att klicka på följande knapp:

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    Den här knappen öppnar ett formulär på Azure Portal där du kan konfigurera din nätverksmiljö innan du distribuerar den.

2. Du kan också ändra namnen på virtuella nätverk och undernät och justera IP-adressintervallen som associeras med dina nätverksresurser. Tryck på knappen ”Köp” för att skapa och konfigurera din miljö:

    ![Skapa en miljö för hanterade instanser](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Den här Azure Resource Manager-distributionen skapar två undernät i det virtuella nätverket – ett för hanterade instanser med namnet **ManagedInstances**, och ett med namnet **Default** för andra resurser, till exempel den virtuella klientdatorn som kan användas för att ansluta till hanterade instanser. Om du inte behöver två undernät kan du ta bort standardundernätverket senare. Observera dock att du i så fall inte kan utföra steg 3 i den här snabbstartsguiden – [Förbereda klientdatorn](#prepare-client-machine).

    > [!Note]
    > Om du ändrar namn på det virtuella nätverket och undernäten måste du komma ihåg de nya namnen eftersom du behöver dem i efterföljande avsnitt. I resten av självstudiekursen förutsätter vi att du har skapat ett virtuellt nätverk med namnet **MyNewVNet**, undernätet **ManagedInstances** för SQL-hanterade instanser, samt undernätet **Default** för virtuella datorer och andra resurser.

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

Följande steg visar hur du skapar en hanterad instans när förhandsversionen har godkänts.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp den **hanterade instansen** och välj sedan **Azure SQL Database Managed Instance (förhandsversion)**.
3. Klicka på **Skapa**.

   ![Skapa en hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Välj din prenumeration och kontrollera att villkoren för förhandsversionen är **godkända**.

   ![managed instance preview accepted](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Fyll i formuläret för den hanterade instansen med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn på hanterad instans**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörsinloggning för hanterad instans**|Giltigt användarnamn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.| 
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 16 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Resursgrupp**|Den resursgrupp som du skapade tidigare||
   |**Plats**|Den plats som du tidigare valt|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   |**Virtuellt nätverk**|Det virtuella nätverk som du skapade tidigare| Välj objektet **MyNewVNet/ManagedInstances** om du inte ändrade namnen i föregående steg. Annars väljer du namnet på det virtuella nätverket och undernätet för hanterade instanser som du angav i föregående avsnitt. **Använd inte standardundernätet eftersom det inte är konfigurerat att vara värd för hanterade instanser**. |

   ![managed instance create form](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

6. Klicka på **Prisnivå** för att beräkna storlek på instanser och lagringsresurser samt granska alternativen för prisnivå. Som standard får din instans 32 GB lagringsutrymme kostnadsfritt, **vilket kanske inte är tillräckligt för dina program**.
7. Använd skjutreglagen eller textrutorna för att ange mängden lagringsutrymme och antalet virtuella kärnor. 
   ![prisnivå för hanterad instans](./media/sql-database-managed-instance-get-started/managed-instance-pricing-tier.png)

8. När du är klar klickar du på **Verkställ** för att spara ditt val.  
9. Klicka på **Skapa** för att distribuera den hanterade instansen.
10. Klicka på **ikonen för meddelanden** för att visa status för distributionen.
11. Klicka på **Distribution pågår** för att öppna fönstret för den hanterade instansen om du vill fortsätta övervaka förloppet för distributionen.

Under distributionen kan du fortsätta till nästa procedur.

> [!IMPORTANT]
> För den första instansen i ett undernät tar distributionen vanligtvis mycket längre tid än för efterföljande instanser. Avbryt inte distributionen om den tar längre tid än förväntat. Det tar några minuter att skapa den andra hanterade instansen i undernätet.

## <a name="prepare-client-machine"></a>Förbereda klientdatorn

Eftersom den SQL-hanterade instansen placeras i ditt privata virtuella nätverk måste du skapa en virtuell Azure-dator med ett installerat SQL-klientverktyg, t.ex. SQL Server Management Studio eller SQL Operations Studio, för att ansluta till den hanterade instansen och köra frågor.

> [!Note]
> I stället för en virtuell Azure-klientdator kan du konfigurera [punkt-till-plats](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)-anslutning och ansluta till den hanterade instansen från din lokala dator.

Det enklaste sättet att skapa en virtuell klientdator med alla nödvändiga verktyg är att använda Azure Resource Manager-mallar.

1. Klicka på följande knapp (se till att du är inloggad på Azure Portal på en annan webbläsarflik):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. I formuläret som öppnas anger du namnet på den virtuella datorn, samt administratörsanvändarnamnet och administratörslösenordet som du använder för att ansluta till den virtuella datorn.

    ![Skapa en virtuell klientdator](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Om du inte har ändrat namnet på det virtuella nätverket eller standardundernätet behöver du inte ändra de två sista parametrarna. Annars måste du ändra dessa värden till de värden som du angav när du konfigurerade nätverksmiljön.

3. Klicka på knappen ”Köp” så distribueras den virtuella Azure-datorn i nätverket som du förberett.

4. Anslut till den virtuella datorn med hjälp av Anslutning till fjärrskrivbord och leta upp SQL Server Management Studio eller SQL Operation Studio som installeras automatiskt på den virtuella datorn.

5. Öppna SSMS och ange **värdnamnet** för den hanterade instansen i rutan **Servernamn**. Välj **SQL Server-autentisering**, ange ditt inloggningslösenord i dialogrutan **Anslut till server** och klicka på **Anslut**.

    ![ssms anslut](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

 - [Ansluta dina program till Managed Instance](sql-database-managed-instance-connect-app.md).
 - [Migrera dina databaser från en lokal plats till Managed Instance](sql-database-managed-instance-migrate.md).


