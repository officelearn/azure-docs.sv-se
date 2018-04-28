---
title: Migrera lokala arbetsbelastningar till Azure med Azure DMS och Site Recovery | Microsoft Docs
description: Lär dig hur du förbereder Azure för migrering av lokala datorer använder tjänsten Azure Database migrering och Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Scenario 2: Lift och SKIFT migrering till Azure

Contoso-företag överväger en migrering till Azure. Om du vill testa detta ut vill de utvärdera och migrera en liten lokalt resa app till Azure. Det är en app på två nivåer resa, med ett webbprogram som körs på en virtuell dator och en SQL Server-databas på den andra virtuella datorn. Programmet distribueras i VMware och miljön hanteras av en vCenter Server. 

I [Scenario 1: utvärdera migrering till Azure](migrate-scenarios-assessment.md), de används Data migrering Assistant (DMA) för att utvärdera SQL Server-databasen för appen. Dessutom kan använde de tjänsten Azure migrera för att utvärdera app virtuella datorer. Nu i det här scenariot vill när du har slutfört bedömningen de flytta databasen till en hanteras av Azure SQL-instans med hjälp av Azure-databas migrering Service (DMS) och lokala datorer till virtuella Azure-datorer med hjälp av Azure Site Recovery-tjänsten.

Om du vill testa [Scenario 1](migrate-scenarios-assessment.md), och sedan det här scenariot med den här appen vägledande resa, du kan ladda ned det från [github](https://github.com/Microsoft/SmartHotel360).



**Tjänst** | **Beskrivning** | **Kostnad**
--- | --- | ---
[Management-tjänsten för databasen](https://docs.microsoft.com/azure/dms/dms-overview) | DMS möjliggör sömlös migrering från flera databaskällor till Azure data plattformar, med minimal avbrottstid. | Tjänsten är för närvarande i förhandsversion (April 2018) och kan användas gratis kostnad under förhandsgranskningen.<br/><br/> Observera att DMS för förhandsversion är begränsad till en [antal regioner](https://docs.microsoft.com/azure/dms/dms-overview).
[Azure SQL hanteras instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Hanterade instans är en hanterad databas-tjänst som representerar en fullständigt hanterad SQL Server-instans i Azure-molnet. Den delar samma kod med den senaste versionen av SQL Server Database Engine och har de senaste funktionerna, prestanda och säkerhetskorrigeringar. | Med Azure SQL Database hanterade instanser körs i Azure betalar avgifter baserat på kapacitet. [Läs mer](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Tjänsten samordnar hanterar migrering och katastrofåterställning för virtuella datorer i Azure och lokala virtuella datorer och fysiska servrar.  | Azure Storage avgifter tas ut vid replikering till Azure.  Virtuella Azure-datorer skapas och debiteras när växling vid fel. [Lär dig mer](https://azure.microsoft.com/pricing/details/site-recovery/) om kostnader och priser.

I det här scenariot ska vi ställa in en plats-till-plats VPN-anslutning så att DMS kan ansluta till den lokala databasen, skapa en Azure SQL hanteras instans i Azure och migrera databasen. Site Recovery Vi förbereder lokal VMware-miljön, konfigurera replikering, och migrera de virtuella datorerna till Azure.  


> [!IMPORTANT]
> Du måste vara registrerade i SQL hanteras instans begränsad förhandsversion. Du behöver ett Azure-prenumeration för att [registrera](https://portal.azure.com#create/Microsoft.SQLManagedInstance). Registrering kan ta ett par dagar att slutföra så se till att du göra det innan du börjar distribuera det här scenariot.

## <a name="architecture"></a>Arkitektur

### <a name="site-recovery"></a>Webbplatsåterställning

![Webbplatsåterställning](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

I det här scenariot:

- Contoso är ett fictious namn som representerar en typisk företags-organisation. Contoso vill utvärdera och migrera två nivåer lokala resa appen.
- Contoso har ett lokalt datacenter (contoso-datacenter), med en lokal domänkontrollant (**contosodc1**).
- Internt resa appen nivåer mellan två virtuella datorer, WEBVM och SQLVM och finns på VMware ESXi-värd (**contosohost1.contoso.com**).
- VMware-miljön hanteras av vCenter Server (**vcenter.contoso.com**) som körs på en virtuell dator.

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra det här scenariot är här vad du bör ha.

Krav | Information
--- | ---
**Azure-prenumeration** | Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Om du skapar ett kostnadsfritt konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> Om du använder en befintlig prenumeration och du är inte administratör, måste du arbeta med administratören att tilldela behörigheter för ägare eller deltagare.<br/><br/> Om du behöver mer detaljerade behörigheter kan granska [i den här artikeln](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site recovery (lokalt)** | Ett lokalt vCenter-servern som kör version 5.5, 6.0 eller 6.5<br/><br/> En ESXi-värd som kör version 5.5, 6.0 eller 6.5<br/><br/> En eller flera virtuella VMware-datorer som körs på ESXi-värd.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Stöd för [nätverk](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) och [lagring](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) konfiguration.
**DMS** | För DMS måste en [kompatibel lokala VPN-enhet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> Du måste kunna konfigurera den lokala VPN-enheten. Den måste ha ett externt Internetriktade offentlig IPv4-adress och adressen får inte finnas bakom en NAT-enhet.<br/><br/> Kontrollera att du har åtkomst till dina lokala SQL Server-databas.<br/><br/> Windows-brandväggen ska kunna komma åt datakällan databasmotorn. [Läs mer](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Lägg till regler för att tillåta åtkomst till databasen och till filer med hjälp av SMB-port 445 om en brandvägg framför datorn databasen.<br/><br/> De autentiseringsuppgifter som används för att ansluta till datakällan SQL Server och hanterade målinstans måste vara medlemmar i serverrollen sysadmin.<br/><br/> Du behöver ett nätverk delar i din lokala databas som DMS kan använda för att säkerhetskopiera källdatabasen.<br/><br/> Kontrollera att tjänstkontot som kör SQL Server-instansen källa har skrivbehörighet på nätverksresursen.<br/><br/> Anteckna en Windows-användare (och lösenord) som har behörigheten Fullständig behörighet på nätverksresursen. Tjänsten Azure Database migrering personifierar dessa autentiseringsuppgifter för att överföra säkerhetskopieringsfiler till Azure storage-behållare.<br/><br/> Installationen av SQL Server Express anger TCP/IP-protokollet till **inaktiverad** som standard. Kontrollera att den är aktiverad.


# <a name="scenario-overview"></a>Scenarioöversikt

Det här ska vi göra:

> [!div class="checklist"]
> * **Steg 1: Konfigurera en plats-till-plats VPN-anslutning**: DMS ansluter till lokala databasen via en VPN-anslutning för plats-till-plats. Vi skapar ett virtuellt nätverk för VPN-anslutningen och senare, vi återanvända det här nätverket för Site Recovery. Nätverket som du skapar måste finnas i den region där du skapar en Recovery Services-valvet.
> * **Steg 2: Konfigurera en SQL Azure hanteras instans**: du behöver en förskapad hanterade instans som ska migrera lokala SQL Server-databasen.
> * **Steg 3: Ställ in SA URI för DMS**: en signatur för delad åtkomst (SAS) identifierare URI (Uniform Resource) ger delegerad åtkomst till resurser i ditt lagringskonto, så att du kan ge begränsade behörigheter till lagringsobjekt. Ställa in en SAS-URI så att DMS kan komma åt kontot lagringsbehållare som tjänsten Överför filer för SQL Server-säkerhetskopiering.
> * **Steg 4: Förbered DMS**: registrera providern Databasmigrering, skapa en instans och sedan skapa ett DMS-projekt.
> * **Steg 5: Förbered Azure Site Recovery**: du skapar ett Azure storage-konto för att lagra replikerade data.
> * **Steg 6: Förbereda lokal VMware för Site Recovery**: du förbereda konton för VM identifieringen och agentinstallationen på nytt och förbereda för att ansluta till virtuella Azure-datorer efter redundans. 
> * **Steg 7: Replikera VMs**: Konfigurera Site Recovery-miljö för källa och mål, ställa in en replikeringsprincip och starta replikera virtuella datorer till Azure storage.
> * **Steg 8: Migrera databasen med DMS**: kör en Databasmigrering.
> * **Steg 9: Migrera de virtuella datorerna med Site Recovery**: kör en redundansväxling för att migrera de virtuella datorerna till Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Steg 1: Konfigurera plats-till-plats VPN-anslutning

För att förbereda för plats-till-plats VPN-anslutningen, konfigurera ett virtuellt nätverk och undernät, skapa en VPN-gateway för det virtuella nätverket och konfigurera en lokal nätverksgateway så att Azure vet hur du ansluter till din lokala VPN. Skapar och verifiera plats-till-plats-anslutning.

### <a name="set-up-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Skapa ett virtuellt Azure-nätverk för att förse DMS med plats-till-plats-anslutning till din lokala SQL Server.


1. I den [Azure-portalen](https://portal.azure.com), klickar du på **skapar du en resurs**. Välj **nätverk** > **för virtuella nätverk**.
2. I **virtuellt nätverk** > **Välj en distributionsmodell**väljer **Resource Manager** > **skapa**
3. I **skapa virtuellt nätverk** > **namn**, ange ett unikt nätverksnamn. I vårt scenario, **ContosoVNET**.
4. I **adressutrymmet**, Lägg till IP-adressintervallet. Intervallet får inte överlappa adressutrymmet lokalt.
5. I **resursgruppen**, skapa en ny grupp eller välj en befintlig. I det här scenariot använder vi **ContosoRG**.
6. I **plats**, väljer du den region där du vill skapa virtuella nätverk. Vi använder **östra USA 2**.
7. I **undernät**, Lägg till undernätnamnet på första och adressintervall. Vi har skapat den **appar** undernät.
8. Inaktivera slutpunkter.

    ![Skapa ett virtuellt nätverk](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Välj **fäst på instrumentpanelen** så att du enkelt hitta nätverket i framtiden, och klicka sedan på **skapa**.
10. Det tar några sekunder att skapa ditt virtuella nätverk. När den skapas kontrollerar du den i Azure portal instrumentpanelen.
11. Kontrollera att portarna kommunikation tillåts i det virtuella nätverket: 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Konfigurera undernät

Vi kommer att ha fyra undernät i vårt Azure nätverk:

![Lista över undernät](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

Du kan ställa in det första undernätet (appar) när du skapade i nätverket. Nu skapa resten av undernäten. Gateway-undernätet används av VPN-gateway-anslutning för Azure för att skicka trafik via en VPN-anslutning till den lokala platsen.

1. I det virtuella nätverket under **inställningar**, klickar du på **undernät** > **+ undernät**.
2. Ställ in ett undernät **Datamigration**med den här adressintervall och klickar sedan på **OK**.

    ![Data undernät](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. I **+ undernät**, ställa in ett undernät **identitet**med den här adressintervall och klickar sedan på **OK**.
    ![Identitet undernät](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. I **undernät**, klickar du på **gatewayundernät**, och klicka sedan på **OK**.
    - Det här undernätet innehåller IP-adresser som din VPN-gateway används för VPN-anslutningar.
    - Namnet på det här undernätet anges automatiskt så att Azure kan identifiera den.
    - Justera automatiskt ifylld adressintervallet för att matcha det lokala undernätet. 

    ![Gateway-undernät](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Konfigurera en virtuell nätverksgateway

1. Till vänster på portalen klickar du på **+** och skriver ”Virtuell nätverksgateway” i sökrutan. I **resultat**, klickar du på **virtuell nätverksgateway**.
2. Klicka på **Skapa** längst ned på sidan ”Virtuell nätverksgateway”.
3. I **Skapa virtuell nätverksgateway** >  **namn**, ange ett namn för gateway-objektet.
4. I **Gateway-typ**väljer **VPN**.
5. I **VPN-typ**väljer **ruttbaserad**.
6. I **SKU**, markera gateway SKU i listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Aktivera inte aktivt-aktivt läge. [Lär dig mer](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) om SKU: er.
7. I **för virtuella nätverk** > **Välj ett virtuellt nätverk**, lägga till nätverket som du skapade. Gatewayen kommer att läggas till det här nätverket.
8. I **första IP-konfiguration**, ange en offentlig IP-adress.  En VPN-gateway måste ha en offentlig IP-adress. Den här adressen tilldelas dynamiskt till resursen när VPN-gateway har skapats. Endast dynamiska adresser stöds för närvarande men IP-adressen ändras inte när den är tilldelad.
    - Klicka på **skapa gateway IP-konfiguration**. I **Välj offentlig IP-adress**, klickar du på **+ Skapa nytt**.
    - I **skapa offentlig IP-adress**, ange ett namn för din offentliga IP-adress (Contoso-Gateway). Lämna SKU som **grundläggande**, och klicka på **OK** spara ändringarna.
        
    ![Virtuell nätverksgateway](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Klicka på **Skapa** för att börja skapa VPN-gatewayen. Inställningarna verifieras och ”distribuerar virtuell nätverksgateway” visas på instrumentpanelen.

    ![Validera virtuell nätverksgateway](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
Det kan ta upp till 45 minuter att skapa en gateway. Du kan behöva uppdatera din Portalsida för att se statusen som slutförd.

### <a name="set-up-a-local-network-gateway"></a>Konfigurera en lokal nätverksgateway

Konfigurera en lokal nätverksgateway i Azure, som representerar den lokala platsen.

1. Klicka på **+Skapa en resurs** i portalen.
2. I sökrutan skriver du **Lokal nätverksgateway** och trycker sedan på **Retur** för att söka. I sökresultaten klickar du på **lokal nätverksgateway** > **skapa**.
3. I **skapa lokal nätverksgateway** > **namn**, ange ett namn som Azure använder för din lokala gateway-objekt.
4. **IP-adress**, ange den offentliga IP-adressen för lokala VPN-enhet som ska ansluta till Azure. IP-adress får inte vara NAT'ed och Azure måste kunna nå den.
5. I **adressutrymmet**, typen i de lokala nätverket adressintervall som dirigeras via VPN-gateway till den lokala enheten. Kontrollera att de inte överlappar intervallen på virtuella Azure-nätverket.
6. **Konfigurera inställningar för BGP** inte är relevanta för det här scenariot.
7. I **prenumeration**, kontrollera att prenumerationen är korrekt.
8. I **resursgruppen**: Markera den resursgrupp som du använde för att skapa nätverk (ContosoRG).
9. I **plats**, väljer du den region där du skapade det virtuella nätverket.

    ![Lokal gateway](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Klicka på **skapa** att skapa lokala gateway.

### <a name="configure-your-on-premises-vpn-device"></a>Konfigurera din lokala VPN-enhet

Här är vad du behöver konfigurera på din lokala VPN-enhet:

- Offentliga IPv4-adressen för gateway för virtuella Azure-nätverket som du nyss skapade.
- I förväg delad nyckel (nyckel som du använde när du skapar VPN plats-till-plats-anslutning).  

Du kan du konfigurera din lokala VPN-enhet:

- Beroende på den lokala VPN-enhet som du har, kanske du kan hämta konfiguration för VPN-enhetsskriptet. [Läs mer](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Granska [fler användbara resurser](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Skapa VPN-anslutningen

Nu skapa plats-till-plats VPN-anslutning mellan den virtuella nätverksgatewayen och din lokala VPN-enhet.

1. Öppna sidan virtuellt nätverk i **virtuella nätverken** > **översikt** > **anslutna enheter**. 
2. Klicka på **anslutningar** > **+ Lägg till**.
3. I **Lägg till anslutning** > **namn**, ange ett namn för anslutningen.
4. I **anslutningstypen**väljer **plats-till-plats (IPSec)**.
5. Den **virtuell nätverksgateway** värdet låst eftersom du kopplar från den här gatewayen.
6. I **lokal nätverksgateway**, ange den lokala nätverksgateway som du skapade.
7. I **delad nyckel**, anger du den nyckel som matchar det värde som du använder för din lokala lokala VPN-enhet. I det här exemplet använder vi abc123. Du kan (och bör) dock använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.
8. Värdena för **prenumeration**, **resursgruppen**, och **plats** korrigeras.

    ![VPN-anslutning](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Klicka på **OK** att skapa anslutningen. Du ser ”Skapa anslutning” flash på skärmen.
5. När anslutningen har skapats kan du visa den på den **anslutningar** sida av den virtuella nätverksgatewayen. Status kommer att gå från okänd > ansluta > lyckades.

### <a name="verify-the-vpn-connection"></a>Verifiera VPN-anslutningen

Du kan visa anslutningsstatus för en Resource Manager VPN Gateway i Azure Portal genom att navigera till anslutningen. 

1. Klicka på **alla resurser**, och navigera till din virtuella nätverksgateway.
2. I det virtuella nätverket gateway klickar du på **anslutningar**. Du kan se status för varje anslutning.
3. Klicka på namnet på anslutningen och visa mer information finns i **Essentials**. Status är ”lyckades” och ”ansluten” när du har gjort en lyckad anslutning.

Nu ska du kontrollera att du kan ansluta till SQL Server-VM via VPN. Använd en fjärrskrivbordsanslutning och Anslut till VM-IP-adressen.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Steg 2: Förbered en hanterad Azure SQL-instans

### <a name="set-up-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

I det här scenariot behöver du skapa ett annat virtuellt nätverk som är dedikerad till Azure SQL hanteras-instans.  Observera följande krav:

- Du behöver ett dedikerat undernät för att skapa den hantera instansen.
- Undernätet måste vara tom och inte innehåller några andra Molntjänsten och det får inte vara ett gateway-undernät. När du har skapat den hantera instansen får inte du lägga till resurser till undernätet.
- Undernätet får inte ha en NSG som kopplats till den.
- Undernätet måste ha en användare väg tabellen (UDR) med 0.0.0.0/0 Internet för nästa hopp som endast väg tilldelats. 
- Valfri anpassad DNS: om anpassade DNS har angetts för det virtuella nätverket, Azures rekursiv matchare IP-adress (till exempel 168.63.129.16) måste läggas till i listan. [Läs mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- Undernätet får inte ha en tjänstens slutpunkt (lagring eller SQL) som är kopplade till den. Slutpunkter ska inaktiveras på det virtuella nätverket.
- Undernätet måste ha minst 16 IP-adresser. [Lär dig mer](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) om storleksanpassa hanterade instans undernätet.

Ställ in det virtuella nätverket enligt följande:

1.  Klicka på **Skapa en resurs** i Azure Portal. 
2. Välj **nätverk** > **för virtuella nätverk**.
3. I **virtuellt nätverk** > **Välj en distributionsmodell**väljer **Resource Manager** > **skapa**.
4. I **skapa virtuellt nätverk** > **namn**, ange ett unikt nätverksnamn. I vårt scenario, **ContosoVNETSQLMI**.
5. I **adressutrymmet**, Lägg till IP-adressintervall som visas nedan. Intervallet får inte överlappa dina lokala kataloger och ContosoVNET adressutrymme.
6. I **resursgruppen**, skapa en ny grupp eller välj en befintlig. I det här scenariot använder vi **ContosoRG**.
7. I **plats**, väljer du den region där du vill skapa virtuella nätverk. Vi använder **östra USA 2**.
8. I **undernät**, Lägg till undernätnamnet på första och adressintervall. Vi har skapat den **ManagedInstances** undernät.
9. Inaktivera slutpunkter.

    ![Instansen nätverk](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. När nätverket har distribuerats, måste du ändra DNS-inställningarna. I det här scenariot DNS först pekar på en domänkontrollant i Identity-undernätet med hjälp av en statisk privat IP-adress (172.16.3.4) och Azure DNS-matchare 168.63.129.16.

    ![Instansen nätverk](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Konfigurera routning

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal. Klicka på **routningstabellen** > **skapa på routningstabellen** sidan.
2. I **skapa routningstabellen** > **namn**, ange ett namn för tabellen.
3. Välj din prenumeration, resursgrupp och plats. Lämna BGP inaktiverad och klicka på **skapa**.
    ![Routningstabell](media/migrate-scenarios-lift-and-shift/route-table.png)

4. När routningstabellen har skapats kan öppna det och klickar på **vägar** > **+ Lägg till**.
5. I **Lägg till väg** > **namn**, ange ett namn för vägen.
6. I **adressprefixet**, ange 0.0.0.0/0.
7. I **nästa hopptyp**väljer **Internet**. Klicka sedan på **OK**.

     ![Routningstabell](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Tillämpa vägen till undernätet för hanterade instans

1. Öppna det virtuella nätverket som du skapade. Klicka på **undernät** > din undernätsnamn.
2. Klicka på **routningstabellen** > din tabellnamn. Klicka sedan på **Spara**.

     ![Routningstabell](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Skapa en hanterad instans

1. Klicka på **skapar du en resurs**, leta upp **hanteras instans**, och välj **Azure SQL-hanterade databasinstans (förhandsgranskning)**.
2. Klicka på **Skapa**.
3. I **SQL-instans som hanterade**, väljer din prenumeration och kontrollera att **Förhandsgranska villkoren** visa **godkända**.
4. I **hanteras instansnamn**, ange ett namn. 
5. Ange ett användarnamn och lösenord som administratör för instansen.
6. Välj resursgruppens namn, plats och det virtuella nätverket för instansen.
7. Klicka på **prisnivå** storlek beräkning och lagring. Som standard hämtar instansen 32 GB lagringsutrymme som är gratis (April 2018).
8. Ange lagringen, och antalet virtuella kärnor.
9. Klicka på **tillämpa** att spara inställningarna och **skapa** att distribuera den hantera instansen. Så här visar Distributionsstatus **meddelanden**, och **distribution pågår**.

    ![Hanterad instans](media/migrate-scenarios-lift-and-shift/mi-1.png)

Efter den hanterade instansen distributionen är klar två nya resurser visas i resursgruppen ContosoRG: virtuellt kluster för hanterade instanser och SQL-instans hanteras. Båda är på plats östra USA 2.

![Hanterad instans](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Steg 3: Hämta en SAS-URI för DMS

Hämta en SAS-URI så att DMS kan komma åt ditt konto lagringsbehållaren, säkerhetskopiera filer som används för att migrera databaser till hanterade Azure SQL Database-instans. 

1. Öppna Lagringsutforskaren (förhandsversion).
2. I den vänstra rutan, expanderar du lagringskontot som innehåller blob-behållare som du vill hämta en SAS. Expandera lagringskontot **Blobbbehållare**.
3. Högerklicka på behållaren och välj **hämta signatur för delad åtkomst**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. I **signatur för delad åtkomst**, anger du princip, start-och förfallodatum, tidszon och åtkomstnivåer som du vill använda för resursen. Klicka sedan på **Skapa**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. En andra dialogruta visas tillsammans med URL och QueryStrings som du kan använda för att komma åt resursen lagring blob-behållaren. Välj **kopiera** att kopiera värdena. Låt dem på en säker plats. Du behöver dem när du ställer in DMS.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Steg 4: Förbered DMS

Registrerar Databasmigrering providern och skapa en instans.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Registerresursleverantören Microsoft.DataMigration

1. I din prenumeration, Välj **Resursproviders**. 
2. Sök efter ”migreringen” och välj sedan till höger om Microsoft.DataMigration **registrera**. 


### <a name="create-an-instance"></a>Skapa en instans

1. Välj **+ skapa en resurs**, söka efter ”Azure-databas Migreringstjänst” och välj sedan **Azure migrering databastjänsten** från den nedrullningsbara listan.
2. På skärmen Azure databastjänsten migrering (förhandsgranskning) Välj **skapa**.
3. Ange ett namn för tjänsten, prenumerationen, resursgruppen, virtuella nätverk och prisnivå.
4. Välj **skapa** att skapa tjänsten.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Steg 5: Förbered Azure för Site Recovery-tjänsten

### <a name="set-up-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Du behöver ett Azure-nätverk som virtuella Azure-datorer kommer att finnas när de skapas efter växling vid fel och ett Azure storage-konto som replikerade data lagras.

- Vid tillämpningen av det här scenariot använder vi den Azure-nätverk som vi skapade tidigare, för DMS.
- Observera att nätverket som du använder måste vara i samma region som Site Recovery-valvet.


### <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Site Recovery replikerar VM-data till Azure-lagring. Virtuella Azure-datorer skapas från minnet vid redundansväxling från lokalt till Azure.

1. På menyn [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Lagring** > **Lagringskonto**.
2. I **Skapa lagringskonto** anger du ett namn för kontot. I dessa självstudier använder vi namnet **contosovmsacct1910171607**. Namnet måste vara unikt i Azure och mellan 3 och 24 tecken långt. Det får endast bestå av siffror och gemener.
3. För **Distributionsmodell** väljer du **Resource Manager**.
4. I **Typ av konto** väljer du **Generell användning**. I **Prestanda** väljer du **Standard**. Välj inte blobblagring.
5. I **Replikering** väljer du standardinställningen **Read-access geo-redundant storage** för lagringsredundans.
6. I **Prenumeration** väljer du den prenumeration som du vill skapa det nya lagringskontot i.
7. Ange ett nytt namn på resursgruppen i **Resursgrupp**. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I dessa självstudier ska du använda namnet **ContosoRG**.
8. Välj den geografiska platsen för ditt lagringskonto för **Plats**. Lagringskontot måste finnas i samma region som Recovery Services-valvet. I det här scenariot använder vi den **östra USA 2** region.

   ![skapar ett lagringskonto](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Skapa lagringskontot genom att välja **Skapa**.

### <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

1. I Azure-portalen markerar du **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
2. I **Namn** anger du ett eget namn som identifierar valvet. I den här självstudien använder du **ContosoVMVault**.
3. I **Resursgrupp** väljer du den befintliga resursgruppen med namnet **contosoRG**.
4. I **plats**, ange Azure-regionen **östra USA 2**.
5. För att snabbt komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** > **Skapa**.
Det nya valvet visas på **Instrumentpanelen** > **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Steg 6: Förbereda lokal VMware för Site Recovery

Förbereda VMware för Site Recovery, här vad du behöver göra:

- Förbereda ett konto på vCenter-servern eller vSphere ESXi-värden, för att automatisera VM-identifiering
- Förbereda ett konto för automatisk installation av mobilitetstjänsten på virtuella VMware-datorer
- Förbered lokala virtuella datorer om du vill ansluta till virtuella Azure-datorer efter redundans.


### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Automatiskt identifiera virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar och aktivering av virtuella datorer.

Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn som t.ex. **Azure_Site_Recovery**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

**Aktivitet** | **Roll/behörigheter** | **Detaljer**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).
**Fullständig replikering, redundans och återställning efter fel** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste installeras på den virtuella dator som du vill replikera.

- Site Recovery kan göra en automatisk push-installation av den här komponenten när du aktiverar replikering för den virtuella datorn.
- För automatisk push-installation måste du förbereda ett konto som Site Recovery för att komma åt den virtuella datorn.
- Du kan ange det här kontot när du konfigurerar replikering i Azure-konsolen.
- Behöver du en domän eller lokalt konto med behörighet för att installera på den virtuella datorn.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Efter en redundansväxling till Azure kan du vill ansluta till de replikerade virtuella datorerna i Azure från ditt lokala nätverk.

Om du vill ansluta till virtuella Windows-datorer med RDP efter en redundans, gör du följande:

1. För att få åtkomst via Internet aktiverar du RDP på den lokala virtuella datorn före redundansen. Kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
2. För åtkomst via plats-till-plats-VPN aktiverar du RDP på den lokala datorn. RDP ska tillåtas i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för nätverken **Domän och privat**.
3. Kontrollera att operativsystemets SAN-princip har angetts till **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).
4. Det får inte finnas några väntande Windows-uppdateringar på den virtuella datorn när du utlöser en redundans. Om det finns det kan du inte logga in på den virtuella datorn förrän uppdateringen är klar.
5. Efter en redundans av en virtuell Windows Azure-dator, kontrollerar du att **Startdiagnostik** visar en skärmbild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs. Granska sedan dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Steg 7: Replikera virtuella datorer med Site Recovery

### <a name="select-a-replication-goal"></a>Välj ett replikeringsmål

1. I **Recovery Services-valven** väljer du valvnamnet **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **Skyddsmål** > **Var finns dina datorer?** väljer du **Lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere Hypervisor**. Välj sedan **OK**.

    ![OVF-mall](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Bekräfta att planera distribution

I **distributionsplanering**, klickar du på **Ja, jag har gjort det**, i listrutan.

### <a name="set-up-the-source-environment"></a>Konfigurera källmiljön


Om du vill konfigurera källmiljön behöver du en fristående lokal dator med hög tillgänglighet och en lokal värd med Site Recovery-komponenter. Komponenter omfattar konfigurationsservern och processervern.

- Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- Processervern fungerar som en replikeringsgateway. Den tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage.
- Processervern installerar också mobilitetstjänsten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.


Ställa in konfigurationsservern som en högtillgänglig VMware VM:

- Hämta en mall för förberedda Open Virtualization Format (OVF).
- Importera mallen till VMware för att skapa den virtuella datorn.
- Ställ in konfigurationsservern och registrera den i valvet. 

Site Recovery identifierar lokala virtuella VMware-datorer efter registreringen.



#### <a name="download-the-vm-template"></a>Ladda ned VM-mallen

1. I valvet går du till **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** väljer du **+Konfigurationsserver**.

    ![Ladda ned mall](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. I **Lägg till server** kontrollerar du att **Konfigurationsserver för VMware** visas i **Servertyp**.
2. Ladda ner OVF-mallen för konfigurationsservern.

    ![Hämta OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  Du kan ladda ned den senaste versionen av konfigurationsservermallen direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

1. Logga in på VMware vCenter-servern med VMWare vSphere klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden Distribuera OVF-mall. 

     ![OVF-mall](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. På **Välj källa** anger du platsen för den nedladdade OVF:en.
4. På **Detaljer för recensionen** väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration** accepterar du standardinställningarna.
6. I **Välj lagring** väljer du för bästa prestanda **Thick Provision Eager Zeroed** i **Välj virtuellt diskformat**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klart att slutföras**:

    * Om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **Power on after deployment** (Slå på strömmen efter distributionen) > **Slutför**.

    * Om du vill lägga till ytterligare ett nätverksgränssnitt avmarkerar du alternativet för att **slå på strömmen efter distributionen**. Välj sedan **Slutför**. Konfigurationsservermallen distribueras som standard med ett enda nätverkskort. Du kan lägga till ytterligare nätverkskort efter distributionen.



#### <a name="register-the-configuration-server"></a>Registrera konfigurationsservern 

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör.
4. Första gången du loggar in startas Azure Site Recovery-konfigurationsverktyget.
5. Ange det namn som ska användas för att registrera konfigurationsservern med Site Recovery. Välj sedan **Nästa**.

    ![OVF-mall](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration. Autentiseringsuppgifterna måste ha åtkomst till det valv där du vill registrera konfigurationsservern.

    ![OVF-mall](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. Verktyget utför vissa konfigurationsåtgärder och startar sedan om datorn.
8. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Konfigurera inställningar och lägga till VMware-servern

1. I guiden Konfigurera serverhantering väljer du **Ställ in anslutning** och sedan väljer du det nätverkskort som ska ta emot replikeringstrafiken. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
2. I **Välj Recovery Services-valv** väljer du din Azure-prenumeration samt relevant resursgrupp och valv.

    ![Valvet](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
4. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
5. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
6. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
7. Ange de autentiseringsuppgifter som ska användas av konfigurationsservern för att ansluta till VMware-servern. Site Recovery använder dessa autentiseringsuppgifter för att automatiskt identifiera virtuella VMware-datorer som är tillgängliga för replikering. Välj **Lägg till** och välj sedan **Fortsätt**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. I **Konfigurera autentiseringsuppgifter för virtuell dator** anger du det användarnamn och lösenord som används för att automatiskt installera mobilitetstjänsten på datorer, när replikering har aktiverats. För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Välj **Slutför konfigurationen** för att slutföra registreringen. 
8. När registreringen är klar i Azure-portalen, kontrollerar du att konfigurationsservern och VMware-servern visas på sidan **Källa** i valvet. Välj sedan **OK** för att konfigurera målinställningarna.
9. Site Recovery ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer.

> [!NOTE]
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.

### <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. I **Förbered infrastruktur** > **mål**, Välj den Azure-prenumeration du vill använda.
2. Distributionsmodell mål väljer **Azure Resource Manager**.

3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. I **Förbered infrastruktur** > **replikeringsinställningarna** > **Replikeringsprincipen**, klickar du på **skapar och associerar** .
1. I **Skapa replikeringsprincip** anger du principnamnet **VMwareRepPolicy**.
2. I **Tröskelvärde för återställningspunktmål** använder du standardvärdet 60 minuter. Det här värdet anger hur ofta återställningspunkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
3. I **Återställningspunkt för kvarhållning** anger du standardvärdet 24 timmar som kvarhållningsperiod för varje återställningspunkt. Använd 72 timmar i den här självstudien. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster.
4. I **Appkompatibel ögonblicksbildsfrekvens** använder du standardvärdet 60 minuter som frekvens när programkonsekventa ögonblicksbilder skapas. Välj **OK** för att skapa principen.

    ![Skapa replikeringsprincip](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. Principen associeras automatiskt med konfigurationsservern. 

    ![Koppla replikeringsprincipen](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Aktivera replikering

Nu starta replikering av virtuella datorer. Site Recovery installerar Mobilitetstjänsten på varje virtuell dator när du aktiverar replikering för den. 


1. Välj **Replikera program** > **Källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **Datortyp** väljer du **Virtual Machines**.
4. I **vCenter/vSphere Hypervisor** väljer du den vCenter-server som hanterar vSphere-värden, eller så väljer du värden.
5. Välj processerver (konfigurationsserver). Välj sedan **OK**.

    ![Aktivera replikering](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. I **Mål** väljer du den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure (klassisk eller Resource Manager) för de redundansväxlade virtuella datorerna.
2. Välj det Azure-lagringskonto som du vill använda för att replikera data.
3. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
4. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator.
5. Välj undernätet i det virtuella nätverket. Välj sedan **OK**.

    ![Aktivera replikering](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. I **virtuella datorer** > **Välj virtuella datorer**, välj VM (WEBVM) som du vill. Du kan bara välja datorer som stöder replikering. 

    ![Aktivera replikering](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Om du vill övervaka de virtuella datorer som du lägger till, kan du se när de senast identifierades i **Konfigurationsservrar** > **Senaste kontakt**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**. Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
8. I **Egenskaper** > **Konfigurera egenskaper** väljer du det konto som ska användas av processervern för att automatiskt installera mobilitetstjänsten på datorn. 

    ![Aktivera replikering](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. I **Replikeringsinställningar** > **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip har valts.
10. Välj **Aktivera replikering**.

    ![Aktivera replikering](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Spåra förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**. 
12. Efter den **Slutför skydd** jobbet körs datorn redo för redundans och visas i **översikt** > **Essentials**.

    ![Essentials](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Steg 8: Migrera databasen med DMS

Skapa ett DMS-projekt och kör migreringen.


### <a name="create-a-database-migration-project"></a>Skapa en Databasmigrering-projekt

1. Leta upp DMS-resurs i Azure-portalen och öppna den.
2. Välj **+ nytt migreringsprojekt**.
3. I **nytt migreringsprojekt**, ange ett namn för projektet.
4. I **Typ av källserver** väljer du **SQL Server**. I **mål servertyp**väljer **Azure SQL-hanterade databasinstans**.
5. Klicka på **skapa** att skapa projektet.
6. I **datakällan information**, ange anslutningsinformationen för SQL Server av lokal datakälla. Klicka på **Spara**.
7. I **mål information**, ange anslutningsinformationen för mål, vilket är företablerad Azure SQL hanteras databasinstansen som den lokala databasen kommer att migreras. Klicka sedan på **Spara**.
8. I **översikt**, granska och kontrollera information som är kopplade till projektet migrering.

    ![DMS-projekt](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Migrera databasen

1. Guiden Migrera visas när du har skapat projektet.
2. Ange autentiseringsuppgifter för käll-och mål och klicka på **spara**. Guiden försöker logga in på den lokala SQL-servern.

    ![DMS-guiden](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. I **karta till måldatabaserna**, Välj källdatabasen som du vill migrera. Klicka sedan på **Spara**.

    ![DMS-guiden](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. I **mål information**väljer **jag vet mitt information om mål**. Ange DNS-namnet på din SQL hanterade instanser, tillsammans med autentiseringsuppgifterna. Klicka sedan på **Spara**.

    ![DMS-guiden](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. I det sparade projektet, Välj **+ ny aktivitet**. Välj sedan **kör migrering**.
6. När du uppmanas, anger du autentiseringsuppgifterna för käll-och mål. Klicka sedan på **Spara**.
7. I **karta till måldatabaserna**, Välj källdatabasen som du vill migrera. Klicka på **spara**
8. I **konfigurera migreringsinställningarna** > **återställningsplats**, ange en nätverksresurs på den lokala datorn. DMS tar källa säkerhetskopieringar till den här resursen.  Kom ihåg att tjänstkontot som kör SQL Server-instansen källa måste ha skrivbehörighet på den här resursen.
9. Ange användarnamn och lösenord som DMS kan personifiera för uppladdning av de säkerhetskopiera filerna till Azure storage-behållare för återställning.
10. Ange SAS-URI som ger tillgång till ditt konto lagringsbehållare som tjänsten Överför säkerhetskopiorna av DMS och som används för migrering till hanterade Azure SQL Database-instans.  Klicka sedan på **Spara**.
11. I **migrering sammanfattning**, ange ett namn för aktiviteten migrering > **kör migreringen**.
12. I projektet > **översikt**, övervaka Migreringsstatus. När migreringen har slutförts kan du kontrollera att måldatabaserna finns på hanterade instansen.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Steg 9: Migrera virtuella datorer med Site Recovery

Vi rekommenderar att du kör ett redundanstest för att se till att allt fungerar som förväntat, innan du kör en fullständig migrering. När du kör ett redundanstest händer följande:

1. En kravkontroll körs se till att alla villkor krävs för migrering på plats.
2. Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten skapas en återställningspunkt utifrån tillgängliga data.
3. En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

### <a name="run-a-test-failover"></a>Köra ett redundanstest

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.
2. Klicka på VM-egenskaper **+ testa redundans**.

    ![Testa redundans](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Välj **senaste bearbetas**, för att växla över den virtuella datorn till den senaste punkten i tid. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
2. I **Redundanstest** väljer du det Azure-målnätverk som de virtuella Azure-datorerna ska ansluta till efter redundans.
3. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet bin valvet > **inställningar** > **jobb** > **Redundanstest**.
4. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
5. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

### <a name="migrate-the-machines"></a>Migrera datorer

När du har kontrollerat att testa redundans fungerar som förväntat, skapa en återställningsplan för att migrera till Azure.

### <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. Välj i valvet, **Återställningsplaner (Site Recovery)** > **+ Återställningsplanen**.
2. I **skapa återställningsplan**, ange ett namn för schemat.
3. Välj källa konfigurationsservern och Azure som mål. Välj **Resource Manager** för distributionsmodell. Källplatsen måste ha datorer som har aktiverats för redundans och återställning. 
4. I **Markera objekt**, Lägg till datorer (WEBVM) i planen. Klicka sedan på **OK**.
5. Klicka på **OK** att skapa planen.

    ![Återställningsplan](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Köra en redundansväxling

1. I **Återställningsplaner**, klicka på planen > **redundans**.
2. I **redundans** > **återställningspunkt**, Välj den senaste återställningspunkten.
3. Inställningen för krypteringsnyckeln är inte relevant för det här scenariot.
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av virtuella källdatorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.

    ![Redundans](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.

    ![Redundans](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Slutför migrering**. Detta avslutar migreringsprocessen, stoppar replikeringen för den virtuella datorn och stoppar Site Recovery-debitering för den virtuella datorn.

    ![Redundans](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Uppdatera anslutningssträngen

Det sista steget i migreringsprocessen är att uppdatera anslutningssträngen för att peka på den migrerade databas som körs på SQL-MI.

1. Hitta den nya anslutningssträngen i Azure-portalen genom att klicka på **inställningar** > **anslutningssträngar**.

    ![Redundans](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. Du måste uppdatera den här strängen med användarnamn och lösenord för SQL-MI.
3. När strängen är konfigurerad, måste du ersätta den aktuella anslutningssträngen i web.config-filen för tillämpningsprogrammet.
4. Starta om IIS på WEBVM efter uppdatering av filen och spara filen. Detta kan göras med hjälp av IISRESET /RESTART från en kommandotolk.
5. När IIS har startats kommer tillämpningsprogrammet nu att använda den databas som körs på SQL-MI.
6. Då den SQLVM dator lokalt kan stängas av och migreringen är klar.



## <a name="conclusion"></a>Sammanfattning

I det här scenariot har vi:

> [!div class="checklist"]
> * Migrerade våra lokala-databasen till en Azure SQL hanteras-instans med DMS.
> * Migrerade våra lokala virtuella datorer till virtuella Azure-datorer med Azure Site Recovery-tjänsten.
