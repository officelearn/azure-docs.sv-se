---
title: Migrera virtuella virtuella datorer med VMware med agentbaserad Azure Migrera servermigrering
description: Lär dig hur du kör en agentbaserad migrering av virtuella datorer med Virtuella datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 6855c3e81aece0358146608b6cf179fb923c54c8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535340"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrera virtuella virtuella datorer med VMware till Azure (agentbaserad)

Den här artikeln visar hur du migrerar lokala virtuella datorer med VMware till Azure med hjälp av agentbaserad migrering med verktyget Migrera server för Azure.This article shows how to migrate on-premises VMware VMs to Azure, using agent-based migration with the Azure Migrate Server Migration tool.


I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera källmiljön och distribuera en Azure Migrate-replikeringsinstallation för agentbaserad migrering.
> * Ställ in målmiljön för migrering.
> * Konfigurera en replikeringsprincip.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktionshanteringen för VMware-bedömning och migrering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Vi rekommenderar att du provar VMware VM-utvärdering med Azure Migrate Server Assessment, innan du migrerar virtuella datorer till Azure. Ställ in en bedömning på följande sätt:

1. Följ självstudien för att [förbereda Azure och VMware](tutorial-prepare-vmware.md) för utvärdering.
2. Följ sedan [den här självstudien](tutorial-assess-vmware.md) för att konfigurera en Azure Migrate-installation för bedömning och identifiera och utvärdera virtuella datorer.


Även om vi rekommenderar att du provar en utvärdering behöver du inte köra en utvärdering innan du migrerar virtuella datorer.

## <a name="migration-methods"></a>Migreringsmetoder

Du kan migrera virtuella virtuella datorer med VMware till Azure med hjälp av verktyget Migrera servermigrering av Azure.You can migrate VMware VMs to Azure using the Azure Migrate Server Migration tool. Det här verktyget erbjuder ett par alternativ för VMware VM-migrering:

- Agentlös replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Agentbaserad migrering. eller replikering. Installera en agent (Mobility services-agenten) på den virtuella datorn för replikering.

Om du vill bestämma om du vill använda agentless eller agent-baserad migrering läser du följande artiklar:

- [Läs mer om](server-migrate-overview.md) VMware-migreringsalternativen.
- [Jämför migreringsmetoder](server-migrate-overview.md#compare-migration-methods).
- [Följ den här artikeln](tutorial-migrate-vmware.md) om du vill prova agentlös migrering.



## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Granska](migrate-architecture.md) VMware-migreringsarkitekturen.
2. Kontrollera att ditt Azure-konto har tilldelats rollen Deltagare i den virtuella datorn, så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriv till en Azure-hanterad disk. 

3. [Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokala datorer replikeras till Azure-hanterade diskar. När du växlar över till Azure för migrering skapas virtuella Azure-datorer från dessa hanterade diskar och ansluts till ett Azure-nätverk som du anger när du konfigurerar migreringen.


## <a name="prepare-azure"></a>Förbereda Azure

Om du redan har kört en utvärdering med Azure Migrate Server Assessment kan du hoppa över instruktionerna i det här avsnittet eftersom du redan har slutfört dessa steg. 

Om du inte har kört en utvärdering måste du konfigurera Azure-behörigheter innan du kan migrera med Azure Migrate Server Migration.

- **Skapa ett projekt**: Ditt Azure-konto behöver behörigheter för att skapa ett Azure Migrate-projekt. 
- **Registrera Azure Migrate replication-enheten**: Replikeringsinstallationen skapar och registrerar en Azure Active Directory-app i ditt Azure-konto. Delegera behörigheter för detta.
- **Skapa nyckelvalv:** Om du vill migrera virtuella virtuella datorer med Hjälp av Azure Migrate Server Migration skapar Azure Migrate ett nyckelvalv i resursgruppen för att hantera åtkomstnycklar till replikeringslagringskontot i din prenumeration. För att skapa valvet behöver du behörigheter för rolltilldelning för resursgruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure-portalen och välj **Åtkomstkontroll (IAM)**.
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter.
3. Du bör ha **behörigheten Deltagare** eller **Ägare.**
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerationsägare arbetar du med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Tilldela behörigheter för att registrera replikeringsverktyget

För agentbaserad migrering delegerar du behörigheter för Azure Migrate Server Migration för att skapa och registrera en Azure AD-app i ditt konto. Du kan tilldela behörigheter med någon av följande metoder:

- En klient/global administratör kan bevilja behörigheter till användare i klienten för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen Programutvecklare (som har behörigheterna) till kontot.

Det är värt att notera att:

- Apparna har inga andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny replikeringsverktyget. Du kan ta bort behörigheterna när replikeringsverktyget har konfigurerats. 


#### <a name="grant-account-permissions"></a>Bevilja kontobehörigheter

Klienten/den globala administratören kan bevilja behörigheter enligt följande

1. I Azure AD bör klienten/den globala administratören navigera till**Användarinställningar för** **Azure Active Directory** > **Users** > .
2. Administratören ska ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela rollen Programutvecklare 

Klienten/den globala administratören kan tilldela rollen Programutvecklare till ett konto. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Tilldela behörigheter för att skapa Nyckelvalv

Tilldela rolltilldelningsbehörigheter för resursgruppen där Azure Migrate-projektet finns, enligt följande:

1. Välj **Åtkomstkontroll (IAM)** i resursgruppen i Azure-portalen .
2. Leta reda på det relevanta kontot i **Check access**och klicka på det för att visa behörigheter. Du behöver behörigheter **för ägare** (eller **deltagare** och **användaråtkomstadministratör).**
3. Om du inte har de behörigheter som krävs begär du dem från resursgruppens ägare. 


## <a name="prepare-on-premises-vmware"></a>Förbereda lokal VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Azure Migrera servermigrering måste komma åt VMware-servrar för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar samt starta virtuella datorer.

Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn som t.ex. **Azure_Site_Recovery**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

#### <a name="vmware-account-permissions"></a>Behörighet för VMware-konto

**Aktivitet** | **Roll/behörigheter** | **Detaljer**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med **objektet Propagate till det underordnade** objektet till de underordnade objekten (vSphere-värdar, datalager, virtuella datorer och nätverk).
**Fullständig replikering, redundans och återställning efter fel** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med **objektet Propagate till det underordnade** objektet till de underordnade objekten (vSphere-värdar, datalager, virtuella datorer, nd-nätverk).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste vara installerad på datorer som du vill replikera.

- Azure Migrate-replikeringsverktyget kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator, eller så kan du installera den manuellt eller med hjälp av installationsverktyg.
- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För push-installation måste du förbereda ett konto som Azure Migrate Server Migration kan använda för att komma åt den virtuella datorn. Det här kontot används endast för push-installationen om du inte installerar mobilitetstjänsten manuellt.

Förbered kontot enligt följande:

1. Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.
2. Om du inte använder ett domänkonto för Windows-datorer inaktiverar du kontrollen För fjärråtkomst på den lokala datorn genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet i registret, under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. För virtuella Linux-datorer förbereder du ett rotkonto på käll-Linux-servern.


### <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Se till att VMware-servrar och virtuella datorer uppfyller kraven för migrering till Azure. 


> [!NOTE]
> Agentbaserad migrering med Azure Migrate Server Migration baseras på funktioner i Azure Site Recovery-tjänsten. Vissa krav kan länka till dokumentationen för webbplatsåterställning.

1. [Kontrollera](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware-serverkraven.
2. [Verifiera](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Vm-stödkrav för migrering.
3. Verifiera vm-inställningar. Lokala virtuella datorer som du replikerar till Azure måste uppfylla [Kraven på Azure VM](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägga till verktyget migrera server för Azure-migrera server

Om du inte följde självstudien för att bedöma virtuella datorer med VMware konfigurerar du ett Azure Migrate-projekt och lägger sedan till verktyget För migrera server för Azure-migrera server:

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.

    ![Konfigurera Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **Upptäck, utvärdera och migrera servrar**klickar du på Utvärdera och migrera **servrar**.

    ! [Upptäck och bedöma servrar] (./media/tutorial-migrate-vmware-agent/assess-migrate.png

1. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
2. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
3. I **Projektinformation** anger du projektnamnet och geografin där du vill skapa projektet och klickar på **Nästa**. Granska geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [statliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. I **Välj bedömningsverktyg**väljer du **Hoppa över att lägga till ett bedömningsverktyg för nu** > **Nästa**.
5. I **Välj migreringsverktyg**väljer du **Azure Migrate: Servermigrering** > **Nästa**.
6. I **Review + add tools** (Granska + lägg till verktyg)
granskar du inställningarna och klickar på **Lägg till verktyg**
7. När du har lagt till verktyget visas det i Azure Migrate-projektet > **servrar** > **migreringsverktyg**.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikeringsverktyget

Det första steget i migreringen är att ställa in replikeringsverktyget. Replikeringsverktyget är en enda, mycket tillgänglig, lokal virtuell dator med VMware som är värd för dessa komponenter:

- **Konfigurationsserver:** Konfigurationsservern samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- **Processserver:** Processservern fungerar som en replikeringsgateway. Den tar emot replikeringsdata. optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cachelagringskonto i Azure. Processservern installerar också Mobilitetsserviceagenten på virtuella datorer som du vill replikera och utför automatisk identifiering av lokala virtuella datorer med VMware.


Du kan ställa in replikeringsverktyget på ett par olika sätt.

- Konfigurera med en nedladdad OVA-mall (Open Virtualization Application). Du importerar mallen till VMware och skapar den virtuella replikeringsverktyget. Detta är den metod som används i den här självstudien.
- Ställ in med ett skript.

### <a name="download-the-replication-appliance-template"></a>Hämta mallen för replikeringsverktyget

Ladda ner mallen enligt följande:

1. Klicka på **Servrar** under **Migreringsmål**i Azure Migrera-projektet .
2. I **Azure Migrate - Servrar** > **Azure Migrera: Servermigrering**klickar du på **Identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. I **Upptäck-datorer** >  **Yes, with VMWare vSphere hypervisor****Virtualiseras dina datorer?**
4. I **Hur vill du migrera?** **Using agent-based replication**
5. I **Målregionen**väljer du den Azure-region som du vill migrera datorerna till.
6. Välj **Bekräfta att målregionen för migrering är regionnamn**.
7. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Du kan inte ändra målområdet för det här projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

    ![Skapa Recovery Services-valv](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. I **Vill du installera en ny replikeringsinstallation?** väljer du Installera en **replikeringsinstallation**.
9. Klicka på **Hämta**om du vill hämta replikeringsverktyget. Detta hämtar en OVF-mall som du använder för att skapa en ny virtuell VMware-dator som kör installationen.
    ![Ladda ner OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Observera namnet på resursgruppen och valvet för Återställningstjänster. Du behöver dessa under installationen.


### <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

När du har hämtat OVF-mallen importerar du den till VMware för att skapa replikeringsprogrammet på en virtuell virtuell VMware-fil som kör Windows Server 2016.

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. På **Arkiv-menyn** väljer du **Distribuera OVF-mall** för att starta **guiden Distribuera OVF-mall**. 
3. Ange platsen för den nedladdade OVF i **Välj**källa.
4. Välj **Nästa**i **Granska information**.
5. Acceptera standardinställningarna i **Välj namn och mapp** och Välj **konfiguration.**
6. I **Välj lagring** > **Välj virtuellt diskformat**väljer du **Tjock etablering ivrig nollställd**för bästa prestanda .
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **Klar att slutföra**väljer du Aktivera den virtuella datorn med standardinställningarna efter**slutförd** **distribution** > .

   > [!TIP]
   > Om du vill lägga till ytterligare ett nätverkskort avmarkerar du **Power on efter att distributionen** > **har avslutats**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

### <a name="kick-off-replication-appliance-setup"></a>Starta konfigurationen av replikeringsverktyget

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör med hjälp av administratörslösenordet.
4. Första gången du loggar in startar inställningsverktyget för replikeringsverktyget (Azure Site Recovery Configuration Tool) inom några sekunder.
5. Ange ett namn som ska användas för att registrera installationen med Azure Migrate Server Migration. Klicka sedan på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.
7. Vänta tills verktyget har registrerat en Azure AD-app för att identifiera installationen. Apparaten startas om.
1. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.

### <a name="register-the-replication-appliance"></a>Registrera replikeringsverktyget

Slutför inrättandet och registreringen av replikeringsverktyget.

1. Välj Konfigurera serverhantering i guiden Hantering av **konfigurationsserver**.
2. Välj nätverkskortet (som standard finns det bara ett nätverkskort) som replikeringsverktyget använder för vm-identifiering och för att göra en push-installation av mobilitetstjänsten på källdatorer.
3. Välj det nätverkskort som replikeringsverktyget använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
4. Om apparaten finns bakom en proxyserver måste du ange proxyinställningar.
    - Ange proxynamnet **http://ip-address**som **http://FQDN**, eller . HTTPS-proxyservrar stöds inte.
5. När du uppmanas att ange prenumerations-, resursgrupper och valvinformation lägger du till den information som du noterade när du hämtade mallen för installationen.
6. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
7. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
8. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
9. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
10. Ange autentiseringsuppgifterna för kontot som du [skapade](#prepare-an-account-for-automatic-discovery) för VMware-identifiering. Välj **Lägg till** > **fortsätt**.
11. I **Konfigurera autentiseringsuppgifter för virtuella**datorer anger du de autentiseringsuppgifter som du har [skapat](#prepare-an-account-for-mobility-service-installation) för push-installation av mobilitetstjänsten när du aktiverar replikering för virtuella datorer.  
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
12. Välj **Slutför konfigurationen** för att slutföra registreringen.


När replikeringsinstallationen har registrerats ansluter Azure Migrate Server Assessment till VMware-servrar med de angivna inställningarna och identifierar virtuella datorer. Du kan visa identifierade virtuella datorer i **Hantera** > **identifierade objekt**på fliken **Annat.**


## <a name="replicate-vms"></a>Replikera virtuella datorer

Välj nu virtuella datorer för migrering.

> [!NOTE]
> Du kan replikera upp till 10 maskiner tillsammans. Om du behöver replikera mer replikeras de samtidigt i batchar om 10.

1. I Azure Migrate-projektet > **Servrar**klickar **Azure Migrate: Servermigrering**, på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I Lokalinstallation väljer du namnet på den Azure **Migrate-installation**som du konfigurerar.
4. I **vCenter-servern**anger du namnet på den vCenter-server som hanterar de virtuella datorerna eller vSphere-servern där de virtuella datorerna finns.
5. Välj namnet på replikeringsverktyget i **Process Server.**
6. I **Gästautentiseringsuppgifter**anger du det VM-administratörskonto som ska användas för push-installation av mobilitetstjänsten. Klicka sedan på **Nästa: Virtuella datorer**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. I **Virtuella datorer**väljer du de datorer som du vill replikera.

    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.

8. Kontrollera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: Målinställningar**.
9. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
10. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
11. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

12. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **VM-storlek:** Om du använder utvärderingsrekommendationer innehåller listrutan vm-storlek den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk:** Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure-tillgänglighetsuppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

13. I **Diskar**anger du om VM-diskarna ska replikeras till Azure och väljer disktypen (standard-SSD/HDD eller premiumhanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

14. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna när som helst innan replikeringen startar, **Hantera** > **replikeringsdatorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.




## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **Replikera** börjar ett Start Replication-jobb. 
- När startreplikeringsjobbet har slutförts börjar datorerna sin första replikering till Azure.
- När den första replikeringen är klar börjar deltareplikeringen. Inkrementella ändringar på lokala diskar replikeras regelbundet till replikdiskarna i Azure.


Du kan spåra jobbstatus i portalmeddelandena.

![Spåra jobb](./media/tutorial-migrate-vmware-agent/jobs.png)

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervaka replikering](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator, innan du migrerar den.

- Köra en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna, som förblir i drift, och fortsätta replikera. 
- Testmigrering simulerar migreringen genom att skapa en Virtuell Azure-dator med replikerade data (migrera vanligtvis till ett icke-produktions-virtuellt nätverk i din Azure-prenumeration).
- Du kan använda det replikerade testet Azure VM för att validera migreringen, utföra apptestning och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **Migreringsmål** > **Servrar** > Azure**Migrate: Servermigrering**, klicka på Testa **migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har verifierat att testmigrering fungerar som förväntat kan du migrera de lokala datorerna.

1. Klicka på **Replikera servrar**i Azure Migrate-projektet > **Servrar** > **Azure Migrate: Servermigrering**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3.  > Välj **Migrate** >  **Ja****OK**i Migrera Stäng av virtuella datorer och utför en**planerad migrering utan dataförlust**.
    - Som standard Azure Migrate stänger av den lokala virtuella datorn för att säkerställa minsta möjliga dataförlust. 
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella datorn > **Stoppa migreringen**. Det här gör följande:
    - Stoppar replikeringen för den lokala datorn.
    - Tar bort datorn från antalet **replikerande servrar** i Azure Migrate: Servermigrering.
    - Rensar replikeringstillståndsinformation för den virtuella datorn.
2. Installera Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Minska över trafiken till den migrerade Azure VM-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Bästa metoder för efter migreringen

- Lokal
    - Flytta över apptrafiken till den app som körs på den migrerade Azure-VM-instansen.
    - Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
    - Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
    - Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna.
- Justera Azure VM-inställningar efter migreringen:
    - [Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security. När du migrerar virtuella virtuella datorer med virtuella datorer med agentbaserad migrering installerar Installationsprogrammet för Mobilitetstjänsten Azure VM-agenten på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter migreringen.
    - Avinstallera mobilitetstjänsten manuellt från den virtuella Azure-datorn efter migreringen.
    - Avinstallera VMware-verktyg manuellt efter migreringen.
- I Azure:
    - Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
    - Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- Kontinuitet/haveriberedskap
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa åtkomsten för inkommande trafik med [Azure Security Center - Just in time administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.




 ## <a name="next-steps"></a>Nästa steg

Undersök [molnmigreringsresan](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
