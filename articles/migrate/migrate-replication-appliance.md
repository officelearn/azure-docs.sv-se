---
title: Azure Migrate-replikeringsinstallation
description: Lär dig mer om Azure Migrate-replikeringsverktyget för agentbaserad VMWare-migrering.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 85641f514fc4367f02901eb1dd394cfa204c3ec4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535221"
---
# <a name="replication-appliance"></a>Replikeringsverktyget

I den här artikeln beskrivs replikeringsverktyget som används av [Azure Migrate: Servermigreringsverktyg](migrate-services-overview.md#azure-migrate-server-migration-tool) när VMware-virtuella datorer migreras, fysiska datorer och privata/offentliga virtuella moln-datorer till Azure, med hjälp av agentbaserad migrering. 


## <a name="overview"></a>Översikt

Replikeringsverktyget distribueras när du ställer in agentbaserad migrering av virtuella datorer eller fysiska servrar. Den distribueras som en enda lokal dator, antingen som en virtuell virtuell VMware-dator eller en fysisk server. Den körs:

- **Replikeringsanordning:** Replikeringsverktyget samordnar kommunikation och hanterar datareplikering, för lokala virtuella datorer med VMware och fysiska servrar som replikeras till Azure.
- **Processserver:** Processservern, som installeras som standard på replikeringsverktyget, och gör följande:
    - **Replikeringsgateway:** Den fungerar som en replikeringsgateway. Den tar emot replikeringsdata från datorer som är aktiverade för replikering. Den optimerar replikeringsdata med cachelagring, komprimering och kryptering och skickar den till Azure.
    - **Agentinstallationsprogrammet**: Utför en push-installation av mobilitetstjänsten. Den här tjänsten måste installeras och köras på varje lokal dator som du vill replikera för migrering.

## <a name="appliance-deployment"></a>Distribution av apparater

**Används för** | **Detaljer**
--- |  ---
**VMware VM-agentbaserad migrering** | Du hämtar OVA-mallen från Azure Migrate-hubben och importerar till vCenter Server för att skapa den virtuella installationen.
**Fysisk maskinagentbaserad migrering** | Om du inte har en VMware-infrastruktur, eller om du inte kan skapa en virtuell VMware-dator med en OVA-mall, hämtar du en programinstallationsprogram från Azure Migrate-hubben och kör den för att konfigurera installationsdatorn.

> [!NOTE]
> Om du distribuerar i Azure Government använder du installationsfilen för att distribuera replikeringsverktyget.

## <a name="appliance-requirements"></a>Krav på apparater

När du konfigurerar replikeringsverktyget med hjälp av OVA-mallen som finns i Azure Migrate-hubben kör installationen Windows Server 2016 och uppfyller supportkraven. Om du ställer in replikeringsverktyget manuellt på en fysisk server kontrollerar du att den uppfyller kraven.

**Komponent** | **Krav**
--- | ---
 | **VM-apparat för VMware**
PowerCLI (på nytt) | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) ska installeras om replikeringsverktyget körs på en virtuell virtuell VMware-dator.
Typ av nätverkskort | VMXNET3 (om apparaten är en virtuell virtuell VMware-dator)
 | **Maskinvaruinställningar**
Processorkärnor | 8
RAM | 16 GB
Antal diskar | Tre: OS-disken, processservercachedisken och kvarhållningsenheten.
Ledigt diskutrymme (cache) | 600 GB
Ledigt diskutrymme (kvarhållningsdisk) | 600 GB
**Inställningar för programvara** |
Operativsystem | Windows Server 2016 eller Windows Server 2012 R2
Licens | Installationen levereras med en utvärderingslicens för Windows Server 2016, som gäller i 180 dagar.<br/><br/> Om utvärderingsperioden är nära att löpa ut rekommenderar vi att du hämtar och distribuerar en ny installation, eller att du aktiverar operativsystemets licens för den virtuella datorn för den virtuella datorn för den virtuella datorn.
Nationella inställningar för operativsystem | Engelska (en-us)
TLS | TLS 1.2 bör aktiveras.
.NET Framework | .NET Framework 4.6 eller senare bör installeras på datorn (med stark kryptografi aktiverad.
MySQL | MySQL ska installeras på apparaten.<br/> MySQL bör installeras. Du kan installera manuellt eller site recovery kan installera det under installationen.
Andra appar | Kör inte andra appar på replikeringsverktyget.
Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Grupprinciper | Aktivera inte dessa grupprinciper: <br> - Förhindra åtkomst till kommandotolken. <br> - Förhindra åtkomst till registerredigeringsverktyg. <br> - Förtroendelogik för bifogade filer. <br> - Aktivera skriptkörning. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Ingen befintlig standardwebbplats <br> - Ingen befintlig webbplats /ansökan lyssna på port 443 <br>- Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Aktivera [FastCGI-inställning](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Nätverksinställningar** |
IP-adresstyp | Statisk
Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)
Typ av nätverkskort | VMXNET3

## <a name="mysql-installation"></a>MySQL-installation 

MySQL måste installeras på replikeringsapparatens dator. Det kan installeras med en av dessa metoder.

**Metod** | **Detaljer**
--- | ---
Ladda ned och installera manuellt | Ladda ner MySQL-programmet & placera det i mappen C:\Temp\ASRSetup och installera sedan manuellt.<br/> När du ställer in installationen visas MySQL som redan installerat.
Utan online nedladdning | Placera Programmet MySQL-installationsprogram i mappen C:\Temp\ASRSetup. När du installerar apparaten och klickar för att hämta och installera MySQL används installationsprogrammet som du har lagt till.
Hämta och installera i Azure Migrate | När du installerar apparaten och uppmanas att ange MySQL väljer du **Hämta och installera**.

## <a name="url-access"></a>URL-åtkomst

Replikeringsinstallationen behöver åtkomst till dessa url:er i det offentliga Azure-molnet.

**URL** | **Detaljer**
--- | ---
\*.backup.windowsazure.com | Används för replikerad dataöverföring och samordning
\*.store.core.windows.net | Används för replikerad dataöverföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagringskonto som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.com | Används för replikeringshantering och samordning
https:\//management.azure.com | Används för replikeringshantering och samordning
*.services.visualstudio.com | Används för telemetri ändamål (Det är valfritt)
time.windows.com | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Installationen av apparaterna måste komma åt dessa webbadresser. De används för åtkomstkontroll och identitetshantering av Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | För att slutföra MySQL nedladdning. I några få regioner kan hämtningen omdirigeras till CDN-URL:en. Kontrollera att CDN-URL:en också är tillåten om det behövs.


## <a name="azure-government-url-access"></a>Url-åtkomst för Azure Government

Replikeringsinstallationen behöver åtkomst till dessa URL:er i Azure Government.

**URL** | **Detaljer**
--- | ---
\*.backup.windowsazure.us | Används för replikerad dataöverföring och samordning
\*.store.core.windows.net | Används för replikerad dataöverföring och samordning
\*.blob.core.windows.net | Används för att komma åt lagringskonto som lagrar replikerade data
\*.hypervrecoverymanager.windowsazure.us | Används för replikeringshantering och samordning
https:\//management.usgovcloudapi.net | Används för replikeringshantering och samordning
*.services.visualstudio.com | Används för telemetri ändamål (Det är valfritt)
time.nist.gov | Används för att kontrollera tidssynkronisering mellan system och global tid.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Installation av apparat med OVA behöver åtkomst till dessa webbadresser. De används för åtkomstkontroll och identitetshantering av Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | För att slutföra MySQL nedladdning. I några få regioner kan hämtningen omdirigeras till CDN-URL:en. Kontrollera att CDN-URL:en också är tillåten om det behövs.

## <a name="port-access"></a>Tillträde till port

**Enhet** | **Anslutning**
--- | ---
Virtuella datorer | Mobilitetstjänsten som körs på virtuella datorer kommunicerar med den lokala replikeringsverktyget (konfigurationsservern) på inkommande port HTTPS 443 för replikeringshantering.<br/><br/> Virtuella datorer skickar replikeringsdata till processservern (som körs på konfigurationsservermaskinen) på inkommande port HTTPS 9443. Den här porten kan ändras.
Replikeringsverktyget | Replikeringsverktyget dirigerar replikering med Azure via port HTTPS 443 utgående.
Bearbeta server | Processservern tar emot replikeringsdata, optimerar och krypterar dem och skickar den till Azure-lagring via utgående port 443.<br/> Som standard körs processservern på replikeringsverktyget.


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den första replikeringen till Azure-lagring med den angivna replikeringsprincipen. 
2. Trafik replikerar till offentliga slutpunkter för Azure-lagring via internet. Det går inte att replikera trafik över ett virtuellt privat nätverk (VPN) från en lokal plats till Azure.
3. När den första replikeringen är klar börjar deltareplikeringen. Spårade ändringar för en dator loggas.
4. Kommunikation sker på följande sätt:
    - Virtuella datorer kommunicerar med replikeringsverktyget på inkommande port HTTPS 443 för replikeringshantering.
    - Replikeringsverktyget dirigerar replikering med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processservern (som körs på replikeringsverktyget) på inkommande port HTTPS 9443. Den här porten kan ändras.
    - Processservern tar emot replikeringsdata, optimerar och krypterar dem och skickar den till Azure-lagring via utgående port 443.
5. Replikeringsdataloggarna landar först i ett cachelagringskonto i Azure. Dessa loggar bearbetas och data lagras i en Azure-hanterad disk.

![Arkitektur](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Uppgraderingar av apparater

Installationen uppgraderas manuellt från Azure Migrate-hubben. Vi rekommenderar att du alltid kör den senaste versionen.

1. Klicka på **Konfigurationsservrar**i Azure Migrate >-servrar > Azure Migrate: Serverutvärdering, Infrastrukturservrar.
2. I **Konfigurationsservrar**visas en länk i **Agentversion** när en ny version av replikeringsverktyget är tillgänglig. 
3. Hämta installationsprogrammet till replikeringsapparatens dator och installera uppgraderingen. Installationsprogrammet känner av den version som körs på apparaten.
 
## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) konfigurerar replikeringsverktyget för agentbaserad VMware VM-migrering.
- [Lär dig hur du](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) konfigurerar replikeringsverktyget för fysiska servrar.
