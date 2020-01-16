---
title: Migrera virtuella VMware-datorer med agent-baserad Azure Migrate Server-migrering
description: Lär dig hur du kör en agent-baserad migrering av virtuella VMware-datorer med Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: MVC
ms.openlocfilehash: c6e0b65a586bfd629244404933836cde7287ae29
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028949"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrera virtuella VMware-datorer till Azure (agent-baserad)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure med hjälp av en agent-baserad migrering med verktyget för migrering av Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).


I den här guiden får du lära dig hur man:
> [!div class="checklist"]
> * Konfigurera käll miljön och distribuera en Azure Migrate Replication-enhet för agent-baserad migrering.
> * Konfigurera mål miljön för migrering.
> * Konfigurera en replikeringsprincip.
> * Aktivera replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionen för VMware-utvärdering och migrering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Vi rekommenderar att du testar VMware VM-utvärdering med Azure Migrate Server-utvärdering innan du migrerar virtuella datorer till Azure. Konfigurera en utvärdering på följande sätt:

1. Följ själv studie kursen för att [förbereda Azure och VMware](tutorial-prepare-vmware.md) för utvärdering.
2. Följ sedan [den här självstudien](tutorial-assess-vmware.md) för att ställa in en Azure Migrate-apparat för utvärdering och identifiera och utvärdera virtuella datorer.


Även om vi rekommenderar att du testar en utvärdering behöver du inte köra en utvärdering innan du migrerar virtuella datorer.

## <a name="migration-methods"></a>Metoder för migrering

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget för migrering av Azure Migrate Server. Det här verktyget erbjuder ett par alternativ för migrering av virtuella VMware-datorer:

- Replikering utan agent. Migrera virtuella datorer utan att behöva installera något på dem.
- Agent-baserad migrering. eller replikering. Installera en agent (mobilitets tjänst agenten) på den virtuella datorn för replikering.

Om du vill bestämma om du vill använda en agent lös eller agent-baserad migrering kan du läsa följande artiklar:

- [Lär dig mer om](server-migrate-overview.md) alternativen för VMware-migrering.
- [Jämför metoder för migrering](server-migrate-overview.md#compare-migration-methods).
- [Följ den här artikeln](tutorial-migrate-vmware.md) om du vill testa en agent lös migrering.



## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Granska](migrate-architecture.md) arkitekturen för VMware-migrering.
2. Se till att ditt Azure-konto har tilldelats rollen virtuell dator deltagare, så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriv till en Azure-hanterad disk. 

3. [Konfigurera ett Azure-nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokala datorer replikeras till Azure Managed disks. När du växlar över till Azure för migrering skapas virtuella Azure-datorer från dessa hanterade diskar och anslutna till ett Azure-nätverk som du anger när du konfigurerar migrering.


## <a name="prepare-azure"></a>Förbereda Azure

Om du redan har kört en utvärdering med Azure Migrate Server-utvärdering kan du hoppa över instruktionerna i det här avsnittet eftersom du redan har slutfört de här stegen. 

Om du inte har kört en utvärdering måste du konfigurera Azure-behörigheter innan du kan migrera med Azure Migrate Server-migrering.

- **Skapa ett projekt**: ditt Azure-konto måste ha behörighet att skapa ett Azure Migrate-projekt. 
- **Registrera Azure Migrate Replication**-enhet: replikeringstjänsten skapar och registrerar en Azure Active Directory-app på ditt Azure-konto. Du måste delegera behörigheter för detta.
- **Skapa Key Vault**: om du vill migrera virtuella VMware-datorer med Azure Migrate Server-migrering skapar Azure Migrate en Key Vault i resurs gruppen för att hantera åtkomst nycklar till replikeringens lagrings konto i din prenumeration. Om du vill skapa valvet behöver du Roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter för att skapa projekt

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Tilldela behörigheter för att registrera replikerings enheten

För agent-baserad migrering delegerar du behörigheter för Azure Migrate Server-migrering för att skapa och registrera en Azure AD-App i ditt konto. Du kan tilldela behörigheter med någon av följande metoder:

- En klient/global-administratör kan bevilja behörigheter till användare i klienten, för att skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörighet) till kontot.

Det är värt att notera att:

- Apparna har inte någon annan åtkomst behörighet för prenumerationen förutom de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny replikeringsprincip. Du kan ta bort behörigheterna när replikeringsprincipen har kon figurer ATS. 


#### <a name="grant-account-permissions"></a>Bevilja konto behörigheter

Klient organisationen/den globala administratören kan bevilja behörigheter enligt följande

1. I Azure AD bör klient organisationen/den globala administratören navigera till **Azure Active Directory** > **användare** > **användar inställningar**.
2. Administratören bör ange **Appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Detta är en standardinställning som inte är känslig. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela rollen program utvecklare 

Klient organisationen/den globala administratören kan tilldela rollen programutvecklare till ett konto. [Läs mer](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Tilldela behörigheter för att skapa Key Vault

Tilldela roll tilldelnings behörigheter för resurs gruppen där Azure Migrate-projektet finns, enligt följande:

1. I resurs gruppen i Azure Portal väljer du **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter. Du behöver behörighet som **ägare** (eller **deltagare** och **användar åtkomst administratör**).
3. Om du inte har de behörigheter som krävs kan du begära dem från resurs gruppens ägare. 


## <a name="prepare-on-premises-vmware"></a>Förbereda lokal VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Migrering av Azure Migrate server behöver åtkomst till VMware-servrar för att:

- Identifiera automatiskt virtuella datorer. Minst ett skrivskyddat konto krävs.
- Samordna replikering, redundans och återställning efter fel. Du behöver ett konto som kan köra åtgärder som till exempel att skapa och ta bort diskar samt starta virtuella datorer.

Skapa kontot enligt följande:

1. Om du vill använda ett särskilt konto skapar du en roll på vCenter-nivå. Ge rollen ett namn som t.ex. **Azure_Site_Recovery**.
2. Tilldela rollen behörigheterna som sammanfattas i tabellen nedan.
3. Skapa en användare på vCenter-servern eller vSphere-värden. Tilldela rollen till användaren.

#### <a name="vmware-account-permissions"></a>Behörighet för VMware-konto

**Aktivitet** | **Roll/behörigheter** | **Detaljer**
--- | --- | ---
**VM-identifiering** | Minst en skrivskyddad användare<br/><br/> Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).
**Fullständig replikering, redundans och återställning efter fel** |  Skapa en roll (Azure_Site_Recovery) med behörigheterna som krävs och tilldela sedan rollen till en VMware-användare eller grupp<br/><br/> Datacenterobjekt –> Sprid till underordnat objekt, roll=Azure_Site_Recovery<br/><br/> Datalager -> Allokera utrymme, bläddra i datalagret, filåtgärder på låg nivå, ta bort filen, uppdatera filer för virtuella datorer<br/><br/> Nätverk -> Tilldela nätverk<br/><br/> Resurs -> Tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM<br/><br/> Uppgifter -> Skapa uppgift, uppdatera uppgift<br/><br/> Virtuell dator -> Konfiguration<br/><br/> Virtuell dator -> Interagera -> Besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, sätta på, installera VMware-verktyg<br/><br/> Virtuell dator -> Lager -> Skapa, registrera, avregistrera<br/><br/> Virtuell dator -> Etablering -> Tillåt nedladdning till virtuell dator, tillåt filuppladdning till virtuell dator<br/><br/> Virtuell dator -> Ögonblicksbilder -> Ta bort ögonblicksbilder | Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.<br/><br/> Om du vill begränsa åtkomsten tilldelar du rollen **Ingen åtkomst** med objektet **Sprid till underordnad** till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitetstjänsten måste vara installerad på datorer som du vill replikera.

- Azure Migrate Replication-enheten kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator, eller så kan du installera den manuellt eller använda installations verktyg.
- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För push-installation måste du förbereda ett konto som Azure Migrate Server-migrering kan använda för att få åtkomst till den virtuella datorn.

Förbered kontot enligt följande:

1. Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.
2. För virtuella Windows-datorer, om du inte använder ett domän konto, inaktiverar du åtkomst kontroll för fjärran vändare på den lokala datorn genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet i registret, under **HKEY_LOCAL_MACHINE \software\microsoft\windows\currentversion\policies\system**
3. Förbered ett rot konto på käll-Linux-servern för virtuella Linux-datorer.


### <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Se till att VMware-servrar och virtuella datorer uppfyller kraven för migrering till Azure. 


> [!NOTE]
> Agent-baserad migrering med Azure Migrate Server-migrering baseras på funktionerna i tjänsten Azure Site Recovery. Vissa krav kan vara länkade till Site Recovery-dokumentationen.

1. [Kontrollera](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware-serverkraven.
2. [Verifiera](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Krav för VM-support för migrering.
3. Verifiera inställningarna för virtuella datorer. Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för [virtuella Azure-datorer](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget för migrering av Azure Migrate Server

Om du inte följer självstudien för att utvärdera virtuella VMware-datorer, konfigurerar du ett Azure Migrate-projekt och lägger sedan till Migreringsverktyg för Azure Migrate Server:

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.

    ![Konfigurera Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **identifiera, utvärdera och migrera servrar**klickar du på **utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
2. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
3. I **projekt information**anger du det projekt namn och geografi som du vill skapa projektet i och klickar på **Nästa**

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i någon av dessa geografiska områden.

    **Geografi** | **Region**
    --- | ---
    Asien | Asien, sydöstra
    Europa | Europa, norra eller Europa, västra
    USA | USA, östra eller USA, västra centrala

    Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja valfri mål region för den faktiska migreringen.
4. I **Välj bedömnings verktyg**väljer du **hoppa över Lägg till ett utvärderings verktyg för tillfället** > **Nästa**.
5. I **Välj Migreringsverktyg**väljer du **Azure Migrate: Server Migration** > **Nästa**.
6. I **Granska + Lägg till verktyg**granskar du inställningarna och klickar på **Lägg till verktyg**
7. När du har lagt till verktyget visas det i Azure Migrate Project >- **servrar** > **Migreringsverktyg**.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikerings enheten

Det första steget i migreringen är att konfigurera replikerings enheten. Replikeringen är en enskild virtuell VMware-VM med hög tillgänglighet som är värd för dessa komponenter:

- **Konfigurations**Server: konfigurations servern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- **Processerver**: processervern fungerar som en gateway för replikering. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering och skickar den till ett cache Storage-konto i Azure. Processervern installerar också mobilitets tjänst agenten på de virtuella datorer som du vill replikera och utför automatisk identifiering av lokala virtuella VMware-datorer.


Om du vill konfigurera replikerings enheten laddar du ned en för beredd mall för att öppna Virtualization-program (ägg). Du importerar mallen till VMware och skapar replikeringen av den virtuella datorn. 

### <a name="download-the-replication-appliance-template"></a>Ladda ned mallen för replikerings apparaten

Hämta mallen på följande sätt:

1. Klicka på **servrar** under **mål för migrering**i Azure Migrate projektet.
2. I **Azure Migrate-servrar** > **Azure Migrate: Server-migrering**klickar du på **identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. I **Identifiera datorer** > **Är dina datorer virtualiserade?** klickar du på **Ja, med VMware vSphere Hypervisor-programmet**.
4. I **Hur vill du migrera? väljer du** **Använd agent-baserad replikering**.
5. I **mål region**väljer du den Azure-region som du vill migrera datorerna till.
6. Välj **Bekräfta att mål regionen för migrering är regions namn**.
7. Klicka på **Skapa resurser**. Detta skapar ett Azure Site Recovery valv i bakgrunden.
    - Du kan inte ändra mål region för projektet när du har klickat på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

    ![Skapa Recovery Services-valv](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. I vill **du installera en ny replikeringsprincip? väljer du** **installera en replikeringsprincip**.
9. Klicka på **Ladda ned**för att ladda ned replikerings enheten. Detta laddar ned en OVF-mall som du använder för att skapa en ny virtuell VMware-dator som kör-enheten.
    ![Hämta ägg](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Anteckna namnet på resurs gruppen och Recovery Services valvet. Du behöver dessa under installationen av produkten.


### <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

När du har hämtat OVF-mallen importerar du den till VMware för att skapa replikeringssystemet på en virtuell VMware-dator som kör Windows Server 2016.

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden **Distribuera OVF-mall**. 
3. I **Välj källa**anger du platsen för den nedladdade OVF.
4. I **Granska information**väljer du **Nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration**accepterar du standardinställningarna.
6. I **Välj lagrings** > **väljer du virtuellt disk format**för bästa prestanda väljer du **tjocka etablerings Eager noll**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **klart att slutföra**, om du vill konfigurera den virtuella datorn med standardinställningarna väljer du **ström efter distribution** > **Slutför**.

   > [!TIP]
   > Om du vill lägga till ytterligare ett NIC avmarkerar du alternativet för att **slå på strömmen efter distributionen** > **Slutför**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

### <a name="kick-off-replication-appliance-setup"></a>Installation av installations programmet för replikering

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar loggar du in på den virtuella datorn som administratör med hjälp av administratörs lösen ordet.
4. Första gången du loggar in startas installations verktyget för replikeringstjänsten (Azure Site Recovery konfigurations verktyget) inom några sekunder.
5. Ange ett namn som ska användas för att registrera enheten med Azure Migrate Server-migrering. Klicka sedan på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.
7. Vänta tills verktyget har slutfört registreringen av en Azure AD-App för att identifiera installationen. Enheten startas om.
1. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.

### <a name="register-the-replication-appliance"></a>Registrera replikerings enheten

Slutför konfigurationen och registrera replik enheten.

1. I guiden konfigurations Server hantering väljer du **Konfigurera anslutning**.
2. Välj NÄTVERKSKORTet (som standard finns bara ett nätverkskort) som replikeringstjänsten använder för identifiering av virtuella datorer och för att göra en push-installation av mobilitets tjänsten på käll datorer.
3. Välj det nätverkskort som Replication-enheten använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har kon figurer ATS.
4. Om enheten finns bakom en proxyserver måste du ange proxyinställningar.
    - Ange namnet på proxyn som **http://ip-address** eller **http://FQDN** . HTTPS-proxyservrar stöds inte.
5. När du tillfrågas om prenumerationen, resurs grupperna och valvet lägger du till den information som du antecknade när du laddade ned mallen installation.
6. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
7. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
8. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
9. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
10. Ange autentiseringsuppgifterna för det konto som du [skapade](#prepare-an-account-for-automatic-discovery) för VMware-identifiering. Välj **Lägg till** > **Fortsätt**.
11. I **Konfigurera autentiseringsuppgifter för virtuell dator**anger du de autentiseringsuppgifter som du [skapade](#prepare-an-account-for-mobility-service-installation) för push-installation av mobilitets tjänsten när du aktiverar replikering för virtuella datorer.  
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
12. Välj **Slutför konfigurationen** för att slutföra registreringen.


När replikeringstjänsten har registrerats ansluter Azure Migrate Server utvärderingen till VMware-servrar med de angivna inställningarna och identifierar virtuella datorer. Du kan visa identifierade virtuella datorer i **hantera** > **identifierade objekt**på fliken **övrigt** .


## <a name="replicate-vms"></a>Replikera virtuella datorer

Välj nu virtuella datorer för migrering.

> [!NOTE]
> Du kan replikera upp till 10 datorer tillsammans. Om du behöver replikera mer replikerar du dem samtidigt i batchar med 10.

1. I Azure Migrate Project >- **servrar** **Azure Migrate: Server-migrering**klickar du på **Replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. I **Replikera** > **Källinställningar** > **Är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere**.
3. I **lokal**installation väljer du namnet på Azure Migrate-installationen som du konfigurerar.
4. I **vCenter Server**anger du namnet på den vCenter-Server som hanterar de virtuella datorerna eller den vSphere-server som de virtuella datorerna finns på.
5. I **processerver**väljer du namnet på replikerings enheten.
6. I **autentiseringsuppgifter för gäst**anger du det administratörs konto för den virtuella datorn som ska användas för push-installation av mobilitets tjänsten. Klicka sedan på **Nästa: virtuella datorer**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. I **Virtual Machines**väljer du de datorer som du vill replikera.

    - Om du har kört en utvärdering av de virtuella datorerna, kan du tillämpa storleksändring av virtuella datorer och disktypsrekommendationer (premium/standard) från utvärderingsresultaten. Gör detta i **Vill du importera migreringsinställningar från en Azure Migrate-utvärdering?** och välj alternativet **Ja**.
    - Om du inte har kört någon utvärdering, eller om du inte vill använda utvärderingsinställningarna, väljer du alternativet **Nej**.
    - Om du valde att använda utvärderingen väljer du VM-grupp och utvärderingsnamn.

8. Markera varje virtuell dator som du vill migrera. Klicka sedan på **Nästa: mål inställningar**.
9. I **Målinställningar** väljer du prenumeration och den målregion som du vill migrera till. Ange sedan den resursgrupp där du vill att de virtuella Azure-datorerna ska finnas efter migreringen.
10. I **Virtuellt nätverk** väljer du det Azure VNet/undernät som de virtuella Azure-datorerna ska anslutas till efter migreringen.
11. I **Azure Hybrid-förmån**:

    - Välj **Nej** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas av aktiva Software Assurance- eller Windows Server-prenumerationer och du vill tillämpa förmånen på de datorer som du migrerar. Klicka sedan på **Nästa**.

12. I **Compute** granskar du namnet på den virtuella datorn, storlek, disktyp för operativsystemet och tillgänglighetsuppsättningen. De virtuella datorerna måste följa [Azures krav](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **VM-storlek**: om du använder utvärderings rekommendationer kommer List rutan VM-storlek att innehålla den rekommenderade storleken. Annars väljer Azure Migrate en storlek baserat på den närmaste matchningen i Azure-prenumerationen. Du kan också välja en storlek manuellt i **Storlek på virtuell Azure-dator**. 
    - **OS-disk**: Ange OS-disken (start) för den virtuella datorn. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns. 
    - **Tillgänglighets uppsättning**: om den virtuella datorn ska finnas i en Azure-tillgänglighets uppsättning efter migreringen anger du uppsättningen. Uppsättningen måste finnas i målets resursgrupp som du anger för migreringen.

13. I **diskar**anger du om de virtuella dator diskarna ska replikeras till Azure och väljer disk typ (standard SSD/HDD eller Premium Managed Disks) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar kommer de inte att synas i den virtuella Azure-datorn efter migreringen. 

14. I **Granska och starta replikering** kontrollerar du inställningarna och klickar på **Replikera** för att påbörja den första replikeringen för servrarna.

> [!NOTE]
> Du kan uppdatera replikeringsinställningar varje tid innan replikeringen startar, **hantera** > **Replikera datorer**. Det går inte att ändra inställningarna efter att replikeringen har startat.




## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **Replikera** startar du jobbet starta replikering. 
- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.


Du kan spåra jobb status i Portal meddelanden.

![Spåra jobb](./media/tutorial-migrate-vmware-agent/jobs.png)

Du kan övervaka replikeringsstatus genom att klicka på **Replikera servrar** i **Azure Migrate: Server-migrering**.
![övervaka replikering](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Genom att köra en test-migrering kontrollerar du att migreringen fungerar som förväntat, utan att det påverkar de lokala datorerna, som fortsätter att fungera och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Gör en testmigrering enligt följande:


1. I **mål för migrering** > **servrar** > **Azure Migrate: Server migrering**klickar du på **test migrerade servrar**.

     ![Testmigrerade servrar](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Högerklicka på den virtuella dator som ska testas och klicka på **Testmigrera**.

    ![Testmigrera](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. I **Testmigrera** väljer du det Azure VNet där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett VNet för icke-produktion.
4. **Testmigreringen** startas. Övervaka jobbet i portalmeddelanden.
5. När migreringen är klar kan du se den migrerade virtuella Azure-datorn i **Virtual Machines** i Azure Portal. Datornamnet har suffixet **-Test**.
6. När testet är klart högerklickar du på den virtuella Azure-datorn i **Replikera datorer** och klickar på **Rensa upp i testmigreringen**.

    ![Rensa migrering](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera de lokala datorerna.

1. I Azure Migrate Project >- **servrar** > **Azure Migrate: Server-migrering**klickar du på **Replikera servrar**.

    ![Servrarna replikeras](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. I **Replikera datorer** högerklickar du på den virtuella datorn > **Migrera**.
3. I **Migrera** > **Stäng av virtuella datorer och utför en planerad migrering utan dataförlust** väljer du **Ja** > **OK**.
    - Som standard stänger Azure Migrate av den lokala virtuella datorn och kör en replikering på begäran som synkroniserar eventuella VM-ändringar som har inträffat sedan den senaste replikeringen utfördes. Detta säkerställer att ingen dataförlust sker.
    - Om du inte vill stänga av den virtuella datorn väljer du **Nej**
4. Ett migreringsjobb startas för den virtuella datorn. Spåra jobbet i Azure-meddelanden.
5. När jobbet är klart kan du se och hantera den virtuella datorn på sidan **Virtual Machines**.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är färdig högerklickar du på den virtuella datorn > **stoppa migreringen**. Detta stoppar replikeringen för den lokala datorn och rensar information om replikeringstillståndet för den virtuella datorn.
2. Installera [Azure VM-](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- Lokalt
    - Flytta över apptrafiken till den app som körs på den migrerade Azure-VM-instansen.
    - Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
    - Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
    - Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna.
- Justera inställningar för virtuella Azure-datorer efter migreringen:
    - [Azure VM-agenten](../virtual-machines/extensions/agent-windows.md) hanterar VM-interaktion med Azure-infrastrukturkontrollanten. Det krävs för vissa Azure-tjänster, som Azure Backup, Site Recovery och Azure Security. När du migrerar virtuella VMare-datorer med agent-baserad migrering installerar mobilitets tjänstens installations program Azure VM-agent på Windows-datorer. På virtuella Linux-datorer rekommenderar vi att du installerar agenten efter migreringen.
    - Avinstallera mobilitets tjänsten manuellt från den virtuella Azure-datorn efter migreringen.
    - Avinstallera VMware-verktyg manuellt efter migreringen.
- I Azure:
    - Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
    - Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
- Verksamhets kontinuitet/haveri beredskap
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa inkommande trafik åtkomst med [Azure Security Center – just-in-Time-administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
    - Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.




 ## <a name="next-steps"></a>Nästa steg

Undersök [resan för migrering i molnet](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud adoption Framework.
