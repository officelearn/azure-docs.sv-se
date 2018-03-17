---
title: "Azure SQL Database hanteras instanskonfiguration av virtuellt nätverk | Microsoft Docs"
description: "Det här avsnittet beskriver konfigurationsalternativ för ett virtuellt nätverk (VNet) med en Azure SQL Database hanteras-instans."
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: e724a660f8ba2373cefdabe8595908b7bb42f4d6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurera ett virtuellt nätverk för Azure SQL-hanterade databasinstans

Azure SQL-hanterade databasinstans (förhandsgranskning) måste distribueras i en Azure [virtuella nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Den här distributionen gör följande scenarier: 
- Ansluta till en instans som hanteras direkt bilda ett lokalt nätverk 
- Ansluta en hanterad instans länkad server eller en annan lokal datalager 
- Ansluter en hanterad instans till Azure-resurser  

## <a name="plan"></a>Planera

Planera hur du distribuerar en instans av hanteras i virtuellt nätverk med dina svar på följande frågor: 
- Planerar att distribuera en eller flera hanterade instanser? 

  Antalet hanterade instanser avgör den minimala storleken för subnätet för att allokera för dina hanterade instanser. Mer information finns i [bestämma storleken på undernätet för hanterade instansen](#create-a-new-virtual-network-for-managed-instances). 
- Behöver du distribuera din instans som hanteras i ett befintligt virtuellt nätverk eller om du skapar ett nytt nätverk? 

   Om du planerar att använda ett befintligt virtuellt nätverk som du behöver ändra den nätverkskonfigurationen för att anpassa din hanteras instans. Mer information finns i [ändra befintligt virtuellt nätverk för hanterade instans](#modify-an-existing-virtual-network-for-managed-instances). 

   Om du planerar att skapa ett nytt virtuellt nätverk, se [Skapa nytt virtuellt nätverk för hanterade instansen](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Krav

För att skapa en hanterad instans måste du dedikerar undernät i VNet som uppfyller följande krav:
- **Tomt**: undernätet får inte innehålla andra molntjänst som är kopplade till den och det får inte vara Gateway-undernätet. Du kan inte skapa hanterade instans i undernät som innehåller resurser än hanterade instans eller lägga till andra resurser i undernätet senare.
- **Inga NSG**: undernätet får inte ha en Nätverkssäkerhetsgrupp som är kopplade till den.
- **Har specifika routningstabellen**: undernätet måste ha en användare väg tabellen (UDR) med 0.0.0.0/0 Internet för nästa hopp som endast vägen tilldelats. Mer information finns i [skapa nödvändiga routningstabellen och associera den](#create-the-required-route-table-and-associate-it)
3. **Valfri anpassad DNS**: om anpassade DNS har angetts för det virtuella nätverket, Azures rekursiv matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. Mer information finns i [konfigurera anpassade DNS-](sql-database-managed-instance-custom-dns.md).
4. **Inga tjänstslutpunkten**: undernätet får inte ha en tjänstens slutpunkt (lagring eller Sql) som är kopplade till den. Kontrollera att Tjänsteslutpunkter alternativet inaktiveras när du skapar virtuella nätverk.
5. **Tillräckligt IP-adresser**: undernätet måste ha minst 16 IP-adresser. Mer information finns i [bestämma storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Du kan inte distribuera nya hanterade instans om målundernätet inte är kompatibel med alla ovanstående krav. Målet Vnet och undernätet måste hållas i enlighet med kraven hanteras instansen (före och efter distributionen), eftersom varje överträdelse kan orsaka instans att ange felaktigt tillstånd och att vara tillgänglig. Återställa från att tillstånd måste du skapa en ny instans i ett VNet med kompatibla nätverk principer, återskapa nivån instansdata och återställa databaserna. Det här medför betydande driftstopp för dina program.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Bestämma storleken på undernätet för hanterade instanser

När du skapar en hanterad instans allokerar Azure ett antal virtuella datorer beroende på storleken för nivå som du väljer under etableringen. Eftersom dessa virtuella datorer är associerade med ditt undernät måste de IP-adresser. För att säkerställa hög tillgänglighet under normal drift och underhåll kan Azure allokera fler virtuella datorer. Antalet begärda IP-adresser i undernätet är därför större än antalet hanterade instanser i det undernätet. 

Avsiktligt förekomsten hanteras måste ha minst 16 IP-adresser i ett undernät och kan använda upp till 256 IP-adresser. Därför kan du använda nätmasker /28 /24 när du definierar dina undernät IP-adressintervall. 

Om du planerar att distribuera flera hanterade instanser i undernätet och behöver för att optimera på undernätets storlek, använder du parametrarna för att bilda en beräkning: 

- Azure använder fem IP-adresser i undernätet för egna behov 
- Varje instans generella måste två adresser 

**Exempel**: du planerar att ha åtta hanterade instanser. Att innebär du behöver 5 + 8 * 2 = 21 IP-adresser. Som IP-adressintervall definieras i upphöjt till 2 behöver IP-adressintervall 32 (2 ^ 5) IP-adresser. Du måste därför att reservera undernätet med minst/27 nätmask. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Skapa ett nytt virtuellt nätverk för hanterade instanser 

Skapa ett virtuellt Azure-nätverk är en förutsättning för att skapa en instans som hanteras. Du kan använda Azure-portalen [PowerShell](../virtual-network/quick-create-powershell.md), eller [Azure CLI](../virtual-network/quick-create-cli.md). I följande avsnitt beskrivs de steg som använder Azure portal. Informationen som beskrivs här gäller för var och en av dessa metoder.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp och klicka sedan på **virtuellt nätverk**, kontrollera den **Resource Manager** är markerad som distributionen-läge och klicka sedan på **skapa**.

   ![Skapa virtuellt nätverk](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Fyll i formuläret virtuellt nätverk med den begärda informationen på ett sätt som följande skärmbild:

   ![Skapa formulär för virtuellt nätverk](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Klicka på **Skapa**.

   Adressutrymmen och undernät har angetts i CIDR-notation. 

   > [!IMPORTANT]
   > Standardvärden skapa undernät som tar alla VNet-adressutrymmet. Om du väljer det här alternativet kan skapa du inte några resurser i det virtuella nätverket än hanteras instans. 

   Den rekommenderade metoden är följande: 
   - Beräkna undernätets storlek genom att följa [bestämma storleken på undernätet för hanterade instansen](#determine-the-size-of-subnet-for-managed-instances) avsnitt  
   - Utvärdera behoven för resten av virtuellt nätverk 
   - Fyll i VNet och undernät adressintervall därefter 

   Kontrollera att alternativet Tjänsteslutpunkter förblir **inaktiverade**. 

   ![Skapa formulär för virtuellt nätverk](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Skapa nödvändiga routningstabellen och associera den

1. Logga in på Azure Portal  
2. Leta upp och klicka sedan på **routningstabellen**, och klicka sedan på **skapa** på sidan väg tabell.

   ![routningstabellen skapa formulär](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Skapa en 0.0.0.0/0 nästa hopp Internet väg på ett sätt som följande skärmbilderna:

   ![Lägg till routningstabellen](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![Väg](./media/sql-database-managed-instance-tutorial/route.png)

4. Koppla den här vägen till undernätet för den hanterade instans på ett sätt som följande skärmbilderna:

    ![Undernät](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![Ange routningstabellen](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![Ange vägen tabell-spara](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


När ditt VNet har skapats är du redo att skapa din hanteras instans.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Ändra ett befintligt virtuellt nätverk för hanterade instanser 

Frågor och svar i det här avsnittet visar hur du lägger till en hanterad instans befintligt virtuellt nätverk. 

**Använder du klassiska eller Resource Manager-distributionsmodellen för befintliga virtuella nätverket?** 

Du kan bara skapa en instans som hanteras i hanteraren för virtuella nätverk. 

**Vill du skapa ett nytt undernät för hanterade SQL-instans eller använda befintlig?**

Om du vill skapa ny: 

- Beräkna undernätets storlek genom att följa riktlinjerna i den [bestämma storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances) avsnitt.
- Följ stegen i [Lägg till, ändra eller ta bort ett undernät för virtuellt nätverk](../virtual-network/virtual-network-manage-subnet.md). 
- Skapa en routingtabell som innehåller en enda post **0.0.0.0/0**, enligt nästa hopp Internet och koppla den till undernätet för den hanterade instansen.  

Om du vill skapa en instans som hanteras i ett befintligt undernät: 
- Kontrollera om undernätet är tom - en hanterad instans inte kan skapas i ett undernät som innehåller andra resurser inklusive Gateway-undernätet. 
- Beräkna undernätets storlek genom att följa riktlinjerna i den [bestämma storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances) och kontrollera storlek på lämpligt sätt. 
- Kontrollera att Tjänsteslutpunkter inte är aktiverade på undernätet.
- Kontrollera att det inte finns några nätverkssäkerhetsgrupper som är associerade med undernätet 

**Har du anpassade DNS-server konfigurerad?** 

Om Ja, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md). 

- Skapa nödvändiga routningstabellen och associera den: finns [skapa nödvändiga routningstabellen och associera den](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>Nästa steg

- En översikt finns [vad är en hanterad instans](sql-database-managed-instance.md)
- En självstudiekurs visar hur du skapar ett VNet, skapa en instans som hanteras och återställa en databas från en säkerhetskopia av databasen finns [att skapa en Azure SQL Database hanteras instans](sql-database-managed-instance-tutorial-portal.md).
- DNS-problem finns [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
