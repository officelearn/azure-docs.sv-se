---
title: "Skapa en klassiska virtuella Azure-datorn kör MySQL | Microsoft Docs"
description: "Skapa en Azure-dator som kör Windows Server 2012 R2 och MySQL-databas med hjälp av den klassiska distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: 11850e5ce20efae88a7af9c1d2e4761ed2b70cd7
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Installera MySQL på en virtuell dator som skapats med den klassiska distributionsmodellen som kör Windows Server 2016
[MySQL](https://www.mysql.com) är en populär öppen källkod, SQL-databas. Den här kursen visar hur du installerar och kör den **av MySQL 5.7.18** som en MySQL-Server på en virtuell dator som kör **Windows Server 2016**. Din upplevelse kan skilja sig något för andra versioner av MySQL eller Windows Server.

Anvisningar om hur du installerar MySQL Linux avser: [installera MySQL på Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Skapa en virtuell dator som kör Windows Server 2016
Om du inte redan har en virtuell dator som kör Windows Server 2016, du kan använda detta [kursen](./tutorial.md) att skapa den virtuella datorn.

## <a name="attach-a-data-disk"></a>Anslut en datadisk
När den virtuella datorn har skapats kan du ansluta en datadisk (valfritt). Lägger till en datadisk rekommenderas för produktionsarbetsbelastningar och för att undvika slut på utrymme på OS-enhet (C:), som innehåller operativsystemet.

Se [hur du kopplar en datadisk till en virtuell Windows-dator](../attach-managed-disk-portal.md) och följ instruktionerna för att bifoga en tom disk. Ange värden cachen om du **ingen** eller **skrivskyddad**.

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn
Därefter ska du [logga in på den virtuella datorn](./connect-logon.md) så att du kan installera MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installera och köra MySQL Community-Server på den virtuella datorn
Följ dessa steg för att installera, konfigurera och köra av MySQL-Server:

> [!NOTE]
> När du hämtar artiklar som använder Internet Explorer kan du ange IE **Förbättrad säkerhetskonfiguration** till inaktiverat och förenkla hämtningen. Start-menyn administrativa verktyg/Server Manager/lokal Server Klicka IE **Förbättrad säkerhetskonfiguration** och konfigurationen av).
>
>

1. När du har anslutit till den virtuella datorn via fjärrskrivbord, klickar du på **Internet Explorer** från startskärmen.
2. Välj den **verktyg** i det övre högra hörnet (ikonen cogged hjul) och klicka sedan på **Internetalternativ**. Klicka på den **säkerhet** klickar du på den **tillförlitliga platser** ikonen och klickar sedan på den **platser** knappen. Lägg till http://*.mysql.com i listan över betrodda platser. Klicka på **Stäng**, och klicka sedan på **OK**.
3. I adressfältet i Internet Explorer, ange https://dev.mysql.com/downloads/mysql/.
4. Använda MySQL-plats för att leta upp och hämta den senaste versionen av MySQL installationsprogrammet för Windows. När du väljer MySQL-installationsprogrammet ska hämta den version som har det fullständiga filen uppsättningen (till exempel den mysql-installer-community-5.7.18.0.msi med en storlek på 352.8 MB) och spara installationsprogrammet.
5. När installationsprogrammet har hämtats, klickar du på **kör** att starta installationen.
6. På den **licensavtalet** , Godkänn licensavtalet och klicka på **nästa**.
7. På den **att välja en installationstyp** klickar du på den installationstyp som du vill använda och klicka sedan på **nästa**. Följande steg gäller valet av den **endast Server** typ av installation.
8. Om den **Kontrollera krav** visas, klickar du på **kör** så att installationsprogrammet ska installera alla komponenter som saknas. Följ instruktionerna som visas, till exempel C++ Redistributable körningsmiljön.
9. På den **Installation** klickar du på **kör**. När installationen är klar klickar du på **nästa**.

10. På den **produktkonfigurationen** klickar du på **nästa**.

11. På den **typ och nätverk** anger du önskad konfiguration typ och anslutningen alternativen, inklusive TCP-porten om det behövs. Välj **visa avancerade alternativ**, och klicka sedan på **nästa**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. På den **konton och roller** anger du ett starkt lösenord för MySQL rot. Lägga till ytterligare användarkonton för MySQL efter behov och klicka sedan på **nästa**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. På den **Windows Service** sidan Ange ändringar av standardinställningarna för att köra MySQL-Server som en windowstjänst efter behov och klicka sedan på **nästa**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. Alternativen på den **plugin-program och tillägg** sidan är valfria. Klicka på **nästa** att fortsätta.
15. På den **avancerade alternativ** sidan Ange alternativ för loggning ändringar efter behov och klicka sedan på **nästa**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. På den **gäller serverkonfiguration** klickar du på **kör**. När konfigurationsstegen är klar klickar du på **Slutför**.
17. På den **produktkonfigurationen** klickar du på **nästa**.
18. På den **Installation Complete** klickar du på **kopiera loggen till Urklipp** om du vill undersöka den senare och klickar sedan på **Slutför**.
19. Ange från startskärmen **mysql**, och klicka sedan på **MySQL 5.7 kommandoradsverktyget klienten**.
20. Ange rotlösenordet som du angav i steg 12 och visas med en uppmaning där du kan utfärda kommandon för att konfigurera MySQL. Mer information om kommandon och syntax, finns [MySQL referens handböcker](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Du kan också konfigurera server standard konfigurationsinställningar, till exempel base och data och enheter. Mer information finns i [6.1.2 Server Configuration standardvärden](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Konfigurera slutpunkter

För MySQL-tjänsten ska vara tillgängliga för klientdatorer på Internet måste du konfigurerar en slutpunkt för TCP-porten och skapa en regel för Windows-brandväggen. Standard är-port som MySQL Server-tjänsten lyssnar för MySQL-klienter 3306. Du kan ange en annan port, så länge porten är konsekventa med värdet som angavs på den **typ och nätverk** (steg 11 i föregående procedur).

> [!NOTE]
> För produktion, Överväg säkerhetsriskerna med att göra tjänsten MySQL Server tillgängliga för alla datorer på Internet. Du kan definiera uppsättningen källans IP-adresser som ska kunna använda slutpunkt med en åtkomstkontrollista (ACL). Mer information finns i [så ange slutpunkter till en virtuell dator](setup-endpoints.md).
>
>

Så här konfigurerar du en slutpunkt för MySQL Server-tjänsten:

1. I Azure-portalen klickar du på **virtuella datorer (klassisk)**, klicka på namnet på den virtuella datorn MySQL och klicka sedan på **slutpunkter**.
2. I kommandofältet klickar du på **Lägg till**.
3. På den **lägga till slutpunkten** , ange ett unikt namn för **namn**.
4. Välj **TCP** som protokoll.
5. Ange portnummer som **3306**, i både **offentlig Port** och **privat Port**, och klicka sedan på **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Lägga till en regel för Windows-brandväggen så att MySQL-trafik
Om du vill lägga till en Windows-brandväggsregel som tillåter MySQL-trafik från Internet, kör du följande kommando vid en _Windows PowerShell-kommandotolk_ på den virtuella datorn på MySQL.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Testa din anslutning
Du måste ange DNS-namnet på den molntjänst som innehåller VN för att testa din fjärranslutning till den virtuella Azure-datorn kör tjänsten MySQL.

1. I Azure-portalen klickar du på **virtuella datorer (klassisk)**, klicka på namnet på den virtuella datorn på MySQL och klicka sedan på **översikt**.
2. Från instrumentpanelen virtuella Observera den **DNS-namnet** värde. Här är ett exempel:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. Kör följande kommando för att logga in som en MySQL-användare från en lokal dator som kör MySQL eller MySQL-klienten.

     MySQL -u <yourMysqlUsername> - p -h<yourDNSname>

   Till exempel med användarnamn MySQL _dbadmin3_ och _testmysql.cloudapp.net_ DNS-namn för den virtuella datorn, kan du starta MySQL med följande kommando:

     MySQL -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör MySQL finns i [MySQL dokumentationen](http://dev.mysql.com/doc/).
