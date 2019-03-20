---
title: Erbjuder högtillgängliga MySQL-databaser i Azure Stack | Microsoft Docs
description: Lär dig hur du skapar en MySQL-Server-resursleverantör värddatorn och högtillgängliga MySQL-databaser med Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: ff1b137fb6461c4ebd17451b89a180364d833d4e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116800"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>Självstudier: Erbjud högtillgängliga MySQL-databaser

Du kan konfigurera server-datorer till värden MySQL Server-databaser som en Azure Stack-Operator. När du har en MySQL klustret är har skapas och hanteras av Azure Stack, användare som prenumererar på MySQL-tjänster kan enkelt skapa högtillgängliga MySQL-databaser.

Den här kursen visar hur du använder Azure Stack marketplace-objekt för att skapa en [MySQL med replikering kluster](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Den här lösningen använder flera virtuella datorer för att replikera databaserna från den överordnade noden till ett konfigurerbart antal repliker. När skapat klustret kan sedan läggas till som en Azure Stack MySQL som är värd för Server och användare kan sedan skapa en MySQL-databaser med hög tillgänglighet.

> [!IMPORTANT]
> Den **MySQL med replikering** Azure Stack marketplace-objekt kanske inte är tillgängliga för alla Azure-prenumeration molnmiljöer. Kontrollera att marketplace-objekt är tillgängliga i din prenumeration innan du försöker att följa resten av det här tutoral.

Vad du lära dig:

> [!div class="checklist"]
> * Skapa en MySQL-Server-kluster från marketplace-objekt
> * Skapa ett Azure Stack-MySQL på värdservern
> * Skapa en MySQL-databas med hög tillgänglighet

I den här självstudien ett tre Virtuella MySQL Server-kluster skapas och konfigureras med tillgängliga Azure Stack marketplace-objekt. 

Innan du börjar med stegen i den här självstudien, se till att den [MySQL Server-resursleverantör](azure-stack-mysql-resource-provider-deploy.md) har installerats och att följande objekt är tillgängliga i Azure Stack marketplace:

> [!IMPORTANT]
> Följande krävs för att skapa MySQL-kluster.

- [MySQL med replikering](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Det här är Bitnami-lösningsmallen som ska användas för klusterdistribution MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian). Debian 8 ”Jessie” med backportar kernel för Microsoft Azure tillhandahåller credativ. Debian GNU/Linux är en av de mest populära Linux-distributionerna.
- [Anpassat skript för linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Tillägget för anpassat skript är ett verktyg för att köra dina VM-anpassning uppgifter post VM etablera. När det här tillägget har lagts till en virtuell dator kan den hämta skript från Azure storage och kör dem på den virtuella datorn. Anpassade skripttillägg uppgifter kan också automatiseras med hjälp av Azure PowerShell-cmdlets och plattformsoberoende för Azure-kommandoradsgränssnittet (xPlat CLI).
- Åtkomst till virtuell dator för Linux-tillägget 1.4.7. VM Access-tillägg kan du återställa lösenordet eller SSH-nyckel SSH-konfigurationer, så att du kan få åtkomst till den virtuella datorn. Du kan också lägga till en ny användare med lösenord eller SSH-nyckel eller ta bort en användare som använder det här tillägget. Det här tillägget riktar sig mot virtuella Linux-datorer.

Mer information om att lägga till objekt i Azure Stack Marketplace finns på [översikt över Azure Stack Marketplace](azure-stack-marketplace.md).

Du måste också en SSH-klient som [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) att logga in på den virtuella Linux-datorer när de har distribuerats.

## <a name="create-a-mysql-server-cluster"></a>Skapa en MySQL-Server-kluster 
Använd stegen i det här avsnittet för att distribuera MySQL-Server-kluster med hjälp av den [MySQL med replikering](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) marketplace-objekt. Den här mallen distribuerar tre MySQL-Server-instanser som konfigurerats i en MySQL-kluster med hög tillgänglighet. Som standard skapas följande resurser:

- Ett virtuellt nätverk
- En nätverkssäkerhetsgrupp
- Ett lagringskonto
- En tillgänglighetsuppsättning
- Tre nätverksgränssnitt (en för var och en av de virtuella datorerna som standard)
- En offentlig IP-adress (för det primära MySQL klustret virtuell dator)
- Tre virtuella Linux-datorer som värd för MySQL-kluster

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Välj **\+** **skapa en resurs** > **Compute**, och sedan **MySQL med replikering**.

   ![Anpassad malldistribution](media/azure-stack-tutorial-mysqlrp/1.png)

3. Innehåller grundläggande distribution av information om den **grunderna** sidan. Granska standardinställningarna och ändra efter behov och klicka på **OK**.<br><br>Som ett minimum innehåller följande:
   - Distributionsnamn (standard är mymysql)
   - Rotlösenord för programmet. Ange ett alfanumeriskt lösenord 12 tecken med **några specialtecken**
   - Programdatabas (standard är bitnami)
   - Antal MySQL databasrepliken virtuella datorer för att skapa (standardvärdet är 2)
   - Välj prenumerationen som du använder
   - Välj resursgruppen som ska användas eller skapa ett nytt lösenord
   - Välj platsen (standard är lokala för ASDK)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Grunderna för distribution")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. På den **Miljökonfiguration** anger följande information och klicka sedan på **OK**: 
   - Lösenordet eller SSH offentlig nyckel för secure shell (SSH)-autentisering. Om du använder ett lösenord, måste den innehålla bokstäver, siffror och **kan** innehåller specialtecken
   - VM-storlek (standard är Standard D1 v2-datorer)
   - Data diskstorlek i GB Klicka **OK**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Miljökonfiguration")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Granska distributionen **sammanfattning**. Du kan ladda ned anpassad mall och parametrar, och klicka sedan på **OK**.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Sammanfattning")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Klicka på **skapa** på den **köpa** att starta distributionen.

   ![Köp](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > Distributionen tar ungefär en timme. Kontrollera att distributionen är klar och MySQL-kluster har konfigurerats klart innan du fortsätter. 

7. När alla distributioner har slutförts, granska resursobjekt för gruppen och välj den **mysqlip** offentliga IP-adress-objekt. Anteckna den offentliga IP-adress och fullständig FQDN för den offentliga IP-Adressen för klustret.<br><br>Du måste ge det till en Azure Stack-operatör så att de kan skapa en värdtjänst MySQL-server att använda MySQL-kluster.


### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen
Som standard konfigureras ingen offentlig åtkomst för MySQL i den Virtuella värddatorn. För Azure Stack MySQL-resursprovider att ansluta och hantera MySQL-kluster, måste en nätverkssäkerhetsgrupper (NSG) för inkommande säkerhetsregel som ska skapas.

1. I administratörsportalen kan navigera till resursgruppen som skapades när du distribuerar MySQL-kluster och välj den nya nätverkssäkerhetsgruppen (**standard-undernät-sg**):

   ![open (öppen)](media/azure-stack-tutorial-mysqlrp/6.png)

2. Välj **ingående säkerhetsregler** och klicka sedan på **Lägg till**.<br><br>Ange **3306** i den **Målportintervall** och du kan också ange en beskrivning i den **namn** och **beskrivning** fält. Klicka på Lägg till om du vill stänga dialogrutan inkommande regel.

   ![open (öppen)](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Konfigurera extern åtkomst till MySQL-kluster
Innan MySQL-kluster kan läggas till som en Azure Stack MySQL Server-värd, måste vara aktiverat extern åtkomst.

1. Med en SSH-klient kan det här exemplet används [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), logga in på den primära MySQL-datorn från en dator som har åtkomst till den offentliga IP-Adressen. Vanligtvis slutar det primära MySQL VM-namnet med **0** och tilldelas en offentlig IP-adress.<br><br>Använd den offentliga IP- och logga på den virtuella datorn med användarnamnet för **bitnami** och lösenordet för program som du skapade tidigare utan specialtecken.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. I fönstret SSH-klienten använder du följande kommando så bitnami-tjänst är aktiv och körs. Ange bitnami-lösenord igen när du uppmanas till detta:

   `sudo service bitnami status`

   ![Kontrollera tjänsten](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Skapa ett användarkonto för fjärråtkomst som ska användas av Azure Stack MySQL som är värd för servern att ansluta till MySQL och avsluta sedan SSH-klienten.<br><br>Kör följande kommandon för att logga in på MySQL som rot, med hjälp av rotlösenordet skapade tidigare, och skapa en ny administratörsanvändare, Ersätt *\<användarnamn\>* och *\<lösenord\>* som krävs för din miljö. I det här exemplet är användaren som ska skapas med namnet **sqlsa** och ett starkt lösenord som används:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Skapa administratörsanvändare](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Registrera den nya MySQL-användarinformationen.<br><br>Du måste ange den här användarnamn och lösenord, tillsammans med den offentliga IP-adress eller fullständiga FQDN för den offentliga IP-Adressen för klustret, till en Azure Stack-operatör så att de kan skapa en värdtjänst MySQL-server med MySQL-kluster.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Skapa ett Azure Stack-MySQL på värdservern
När MySQL-Server-klustret har skapats och konfigurerats korrekt, måste en Azure Stack-Operator skapa ett Azure Stack MySQL som är värd för servern för att göra den ytterligare kapaciteten tillgänglig för användare att skapa databaser. 

Se till att använda offentliga IP-adress eller fullständiga FQDN för den offentliga IP-Adressen för MySQL klustret primära virtuella datorn registreras tidigare när MySQL klusterresursgrupp skapades (**mysqlip**). Dessutom kan behöver operatören veta MySQL-Server-autentiseringsuppgifter som du skapade för att fjärransluta till klustret MySQL-databasen.

> [!NOTE]
> Det här steget måste köras från Azure Stack-administrationsportalen av en Azure Stack-operatör.

Med hjälp av MySQL-klustrets offentliga IP-Adressen och MySQL autentisering inloggningsinformation, en Azure Stack-operatör kan nu [skapa en som är värd för MySQL med det nya klustret MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Se också till att du har skapat planer och erbjudanden för att tillgängliggöra skapande av MySQL-databas för användare. En operatör behöver du lägga till den **Microsoft.MySqlAdapter** tjänsten till en plan och skapa en ny kvot specifikt för höggradigt tillgängliga databaser. Mer information om hur du skapar planer finns i [översikt över Plan, erbjudande, kvot och prenumeration](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> Den **Microsoft.MySqlAdapter** tjänsten kan inte läggas till planer tills den [resursprovidern för MySQL-servern har distribuerats](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Skapa en MySQL-databas med hög tillgänglighet
När MySQL-klustret har skapats, konfigurerats och läggas till som en Azure Stack MySQL som är värd för Server av en Azure Stack-operatör, kan en klientanvändare med en prenumeration som innehåller funktioner för MySQL-Server-databasen skapa högtillgängliga MySQL-databaser genom att följa stegen i det här avsnittet. 

> [!NOTE]
> Kör de här stegen från användarportalen för Azure Stack som en klientanvändare med en prenumeration att tillhandahålla funktioner för MySQL-Server (Microsoft.MySQLAdapter service).

1. 
   [!INCLUDE [azs-user-portal](../../includes/azs-user-portal.md)]

2. Välj **\+** **skapa en resurs** > **Data \+ Storage**, och sedan **MySQL-databas** .<br><br>Ange den egenskapen databasinformation som krävs, inklusive namn, sortering, prenumeration för att använda och plats som ska användas för distributionen. 

   ![Skapa MySQL-databas](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Välj **SKU** och välj sedan den lämpliga MySQL som är värd för Server-SKU du använder. I det här exemplet Azure Stack-operatör har skapat den **MySQL-HA** SKU för hög tillgänglighet för MySQL-databaser för klustret.

   ![Välj SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Välj **inloggning** > **skapa en ny inloggning** och anger sedan autentiseringsuppgifterna för MySQL-autentisering som ska användas för den nya databasen. När du är klar klickar du på **OK** och sedan **skapa** att starta distributionsprocessen för databasen.

   ![Lägga till inloggningen](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. När MySQL-databas distributionen är klar kan du granska egenskaperna för databasen för att identifiera strängen som du använder för att ansluta till den nya databasen med hög tillgänglighet. 

   ![Visa anslutningssträng](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en MySQL-Server-kluster från marketplace-objekt
> * Skapa ett Azure Stack-MySQL på värdservern
> * Skapa en MySQL-databas med hög tillgänglighet

Fortsätt till nästa självstudie och lär dig hur du:
> [!div class="nextstepaction"]
> [Web apps-erbjudande](azure-stack-tutorial-app-service.md)
