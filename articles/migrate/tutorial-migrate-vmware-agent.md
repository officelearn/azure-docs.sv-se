---
title: Migrera lokala virtuella VMware-datorer till Azure med agentbaserad Azure Migrate servermigrering | Microsoft Docs
description: Den här artikeln beskriver hur du utför en agentbaserad migrering av lokala datorer till Azure med Azure Migrate-servermigrering
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fe83cd5f38e8c091ee72875da370b6929a99b727
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854145"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrera virtuella VMware-datorer till Azure (agentbaserad)

Den här artikeln visar hur du migrerar lokala virtuella VMware-datorer till Azure, använda agentbaserad migrering med verktyget Azure Migrate servermigrering.

[Azure Migrate](migrate-services-overview.md) ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och AWS/GCP VM-instanser till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt tredjepartsprogram oberoende programvaruleverantörer (ISV)-erbjudanden.


I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera källmiljön och distribuera en Azure Migrate replikering apparat för agentbaserad migrering.
> * Konfigurera målmiljön för migrering.
> * Konfigurera en replikeringsprincip.
> * Aktivera replikering.
> * Kör en testmigrering för att kontrollera att allt fungerar som förväntat.
> * Kör en fullständig migrering till Azure.

> [!NOTE]
> Självstudiekurser visar den enklaste distribution sökvägen för ett scenario så att du snabbt kan ställa in proof-of-concept. Använd standardalternativen där det är möjligt självstudier och visas inte alla möjliga inställningar och sökvägar. Granska de detaljerade förklaringar för VMware-bedömning och migrering detaljerade anvisningar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Vi rekommenderar att du kan prova att använda VMware VM-utvärdering med Azure Migrate Server-utvärdering, innan du migrerar virtuella datorer till Azure. Ställ in en utvärdering på följande sätt:

1. Följ självstudiekursen och [förbereda Azure och VMware](tutorial-prepare-vmware.md) för utvärdering.
2. Följ [den här självstudien](tutorial-assess-vmware.md) att ställa in ett Azure Migrate-installationen för utvärdering, och identifiera och utvärdera virtuella datorer.


Vi rekommenderar att du kan prova att använda en utvärdering har du inte kör en utvärdering innan du migrerar virtuella datorer.

## <a name="migration-methods"></a>Metoder

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget Azure Migrate servermigrering. Det här verktyget erbjuder ett par alternativ för VMware-VM-migrering:

- Agentlös replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Agentbaserad migrering. eller replikering. Installera en agent (mobilitetsagenten services) på den virtuella datorn för replikering.

När du ska välja om du vill använda utan Agent eller agentbaserad migreringen, granska de här artiklarna:

- [Lär dig mer om](server-migrate-overview.md) migreringsalternativ VMware.
- [Granska begränsningarna](server-migrate-overview.md#agentless-migration-limitations) för migrering utan agenter.
- [Följ den här artikeln](tutorial-migrate-vmware.md) du prova att använda migrering utan agenter.



## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

1. [Granska](migrate-architecture.md) arkitektur för VMware-migrering.
2. Se till att ditt Azure-konto har tilldelats rollen virtuell Datordeltagare så att du har behörighet att:

    - Skapa en virtuell dator i den valda resursgruppen.
    - Skapa en virtuell dator i det valda virtuella nätverket.
    - Skriva till en Azure hanterade diskar. 

3. [Konfigurera ett virtuellt Azure nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lokala datorer replikeras till Azure hanterade diskar. När du redundansväxlar till Azure för migrering, virtuella Azure-datorer skapas från dessa hanterade diskar, och ansluten till ett virtuellt Azure nätverk du anger när du ställer in migreringen.


## <a name="prepare-azure"></a>Förbereda Azure

Om du redan har kört en utvärdering med Azure Migrate Server-utvärdering kan du hoppa över anvisningarna i det här avsnittet eftersom du redan har slutfört de här stegen. 

Om du inte kör en utvärdering kan behöva du ställa in Azure-behörigheter innan du kan migrera med Azure Migrate-servermigrering.

- **Skapa ett projekt**: Azure-kontot måste ha behörighet att skapa ett Azure Migrate-projekt. 
- **Registrera Azure Migrate replikering installationen**: Replikering installationen skapar och registrerar en app i Azure Active Directory i ditt Azure-konto. Du måste delegera behörigheter för den här.
- **Skapa Key Vault**: Om du vill migrera virtuella VMware-datorer med hjälp av Azure Migrate-servermigrering skapar Azure Migrate ett Nyckelvalv i resursgruppen, som ska hantera åtkomstnycklar till lagringskontot replikering i din prenumeration. Om du vill skapa valvet behöver du behörigheter för tilldelning på resursgruppen där Azure Migrate-projektet finns. 


### <a name="assign-permissions-to-create-project"></a>Tilldela behörigheter att skapa projekt

1. Ange prenumerationen i Azure-portalen och välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter.
3. Du bör ha **deltagare** eller **ägare** behörigheter.
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare av din prenumeration.
    - Om du inte är prenumerationsägaren, tillsammans med ägare att tilldela rollen.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Tilldela behörighet för att registrera installationen för replikering

Delegera behörigheter för Azure Migrate servermigrering att skapa och registrera en Azure AD-app i ditt konto för agent-baserad migrering. Du kan tilldela behörigheter med hjälp av någon av följande metoder:

- En klient/global administratör kan ge behörigheter till användare i klienten, skapa och registrera Azure AD-appar.
- En klient/global administratör kan tilldela rollen programutvecklare (som har behörigheter) till kontot.

Det är värt som:

- Apparna har inte några andra åtkomstbehörigheter för prenumerationen än de som beskrivs ovan.
- Du behöver bara dessa behörigheter när du registrerar en ny installation av replikering. Du kan ta bort behörigheter när installationen för replikering har konfigurerats. 


#### <a name="grant-account-permissions"></a>Bevilja behörighet

Klient/global administratör kan bevilja behörigheter på följande sätt

1. I Azure AD-klient/globala administratören bör gå till **Azure Active Directory** > **användare** > **användarinställningar**.
2. Administratören bör ange **appregistreringar** till **Ja**.

    ![Azure AD-behörigheter](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Det här är en standardinställning som inte är känsliga. [Läs mer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Tilldela programutvecklare roll 

Klient/global administratör kan tilldela rollen programutvecklare till ett konto. [Läs mer](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>Tilldela behörigheter för att skapa Key Vault

Tilldela behörigheter för tilldelning på resursgruppen där Azure Migrate-projektet finns, enligt följande:

1. I resursgrupp i Azure portal, Välj **åtkomstkontroll (IAM)** .
2. I **Kontrollera åtkomst**, hitta det aktuella kontot och klicka om du vill visa behörigheter. Du behöver **ägare** (eller **deltagare** och **administratör för användaråtkomst**) behörigheter.
3. Om du inte har behörigheterna som krävs, begär du dem från resursgruppägare. 


## <a name="prepare-on-premises-vmware"></a>Förbereda lokal VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>Förbereda ett konto för automatisk identifiering

Azure Migrate servermigrering behöver åtkomst till VMware-servrar till:

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

- Azure Migrate replikering installationen kan göra en push-installation av den här tjänsten när du aktiverar replikering för en dator, eller installera det manuellt, eller med hjälp av verktyg för installation.
- I den här självstudien, installerar vi mobilitetstjänsten med push-installation.
- För push-installation måste du förbereda ett konto som Azure Migrate servermigrering kan använda för att få åtkomst till den virtuella datorn.

Förbered kontot enligt följande:

1. Förbereda en domän eller ett lokalt konto med behörighet för att installera på den virtuella datorn.
2. För Windows-datorer om du inte använder ett domänkonto, inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy**, med värdet i registret under **HKEY_ LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Förbered ett rotkonto på Linux-källservern för virtuella Linux-datorer.


### <a name="check-vmware-requirements"></a>Kontrollera VMware-kraven

Kontrollera att VMware-servrar och virtuella datorer uppfyller kraven för migrering till Azure. 


> [!NOTE]
> Agentbaserad migrering med Azure Migrate-servermigrering är baserad på funktioner i Azure Site Recovery-tjänsten. Vissa krav länka till dokumentation om Site Recovery.

1. [Kontrollera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware-serverkraven.
2. [Kontrollera](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) VM stödja krav för migrering.
3. Kontrollera inställningarna för virtuella datorer. Lokala virtuella datorer som du replikerar till Azure måste uppfylla [kraven för Azure virtuella datorer](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Lägg till verktyget Azure Migrate-servermigrering

Om du inte har följt självstudierna för att utvärdera virtuella VMware-datorer måste ställa in ett Azure Migrate-projekt och Lägg sedan till verktyget Azure Migrate-servermigrering:

1. I Azure portal > **alla tjänster**, Sök efter **Azure Migrate**.
2. Under **Services**väljer **Azure Migrate**.

    ![Konfigurera Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. I **översikt**, klickar du på **utvärdera och migrera servrar för**.
4. Under **Upptäck, utvärdera och migrera servrar för**, klickar du på **utvärdera och migrera servrar för**.

    ![Identifiera och utvärdera servrar](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. I **Upptäck, utvärdera och migrera servrar för**, klickar du på **lägga till verktyg**.
2. I **migrera projektet**, Välj din Azure-prenumeration och skapa en resursgrupp om du inte har något.
3. I **projektinformation**, ange projektnamn och geografisk plats där du vill skapa projektet och klicka på **nästa**

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i någon av dessa områden.

    **Geografi** | **Region**
    --- | ---
    Asien | Sydostasien
    Europa | Europa, norra eller Europa, västra
    USA | USA, östra eller USA, västra centrala

    Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja en målregion för den faktiska migreringen.
4. I **väljer utvärderingsvertyget**väljer **hoppa över att lägga till en utvärderingsvertyget för tillfället** > **nästa**.
5. I **väljer Migreringsverktyget**väljer **Azure Migrate: Servermigrering** > **nästa**.
6. I **granska + Lägg till verktyg**, granskar du inställningarna och klickar på **lägga till verktyg**
7. När du lägger till verktyget, som det visas i Azure Migrate-projektet > **servrar** > **Migreringsverktyg**.

## <a name="set-up-the-replication-appliance"></a>Konfigurera replikering-installation

Det första steget i migrering är att konfigurera replikering-installation. Replikering installationen är en enda med hög tillgänglighet, en lokal VMware VM som är värd för dessa komponenter:

- **Konfigurationsservern**: Konfigurationsservern samordnar kommunikationen mellan den lokala miljön och Azure och hanterar datareplikering.
- **Processerver**: Processervern fungerar som en replikeringsgateway. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar det till ett cachelagringskonto i Azure. Processervern installerar också Mobilitetstjänsten-agenten på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer.


Om du vill konfigurera replikering-installation kan du hämta en förberedd Open Virtualization program (OVA)-mall. Du importerar mallen till VMware och skapa replikering VM. 

### <a name="download-the-replication-appliance-template"></a>Ladda ned mall för replikering-installation

Ladda ned mallen enligt följande:

1. I Azure Migrate-projektet klickar du på **servrar** under **migrering mål**.
2. I **Azure Migrate - servrar** > **Azure Migrate: Servermigrering**, klickar du på **identifiera**.

    ![Identifiera virtuella datorer](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. I **identifiera datorer** > **är dina datorer virtualiserade?** , klickar du på **Ja, med VMWare vSphere hypervisor**.
4. I **hur vill du migrera?** väljer **med agentbaserad replikering**.
5. I **målregionen**, Välj den Azure-region som du vill migrera datorerna.
6. Välj **bekräftar du att målregionen för migrering är Regionsnamn**.
7. Klicka på **skapa resurser**. Detta skapar ett Azure Site Recovery-valv i bakgrunden.
    - Du kan inte ändra målregion för det här projektet när du klickar på den här knappen.
    - Alla efterföljande migreringar är till den här regionen.

    ![Skapa Recovery Services-valv](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. I **vill du installera en ny installation av replikering?** väljer **installera en replikering installation**.
9. Klicka på **hämta**att ladda ned installationen för replikering. Då hämtas en OVF-mall som används för att skapa en ny VMware-VM som kör installationen.
    ![Hämta OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Anteckna namnet på resursgruppen och Recovery Services-valvet. Du behöver dem under distributionen av installationen.


### <a name="import-the-template-in-vmware"></a>Importera mallen i VMware

När du hämtat OVF-mall importera du den till VMware för att skapa programmet replikering på en VMware-VM som kör Windows Server 2016.

1. Logga in på VMware vCenter-servern eller vSphere ESXi-värden med VMWare vSphere-klienten.
2. På menyn **Arkiv** väljer du **Distribuera OVF-mallen** för att starta guiden **Distribuera OVF-mall**. 
3. I **Välj källa**, ange platsen för den nedladdade OVF.
4. I **detaljer för recensionen**väljer **nästa**.
5. I **Välj namn och mapp** och **Välj konfiguration**, accepterar du standardinställningarna.
6. I **Välj lagring** > **Välj virtuellt diskformat**väljer du för bästa prestanda **Thick Provision Eager Zeroed**.
7. Acceptera standardinställningarna på resten av sidorna i guiden.
8. I **klart att slutföras**, för att konfigurera den virtuella datorn med standardinställningarna väljer **slå på strömmen efter distributionen** > **Slutför**.

   > [!TIP]
   > Om du vill lägga till ytterligare ett NIC avmarkerar du alternativet för att **slå på strömmen efter distributionen** > **Slutför**. Mallen innehåller som standard en enda NIC. Du kan lägga till ytterligare nätverkskort efter distributionen.

### <a name="kick-off-replication-appliance-setup"></a>Starta installationsprogrammet för installation av replikering

1. Sätt på den virtuella datorn från VMWare vSphere-klientkonsolen.
2. Den virtuella datorn startas med en Windows Server 2016-installation. Godkänn licensavtalet och ange ett administratörslösenord.
3. När installationen är klar logga du in på den virtuella datorn som administratör med lösenordet för serveradministratören.
4. Första gången du loggar in startar installationsverktyget för replikering installationen (Azure Site Recovery-konfigurationsverktyget) inom några sekunder.
5. Ange ett namn som ska användas för att registrera installationen med Azure Migrate servermigrering. Klicka sedan på **Nästa**.
6. Verktyget kontrollerar att den virtuella datorn kan ansluta till Azure. När anslutningen har upprättats väljer du **Logga in** för att logga in i din Azure-prenumeration.
7. Vänta tills verktyget för att registrera en Azure AD-app för att identifiera installationen är klar. Installationen startas om.
1. Logga in på datorn igen. Guiden Konfigurera serverhantering startar automatiskt om några sekunder.

### <a name="register-the-replication-appliance"></a>Registrera installationen för replikering

Slut att konfigurera och registrera installationen för replikering.

1. I den guiden Konfigurera serverhantering, väljer **Ställ in anslutning**.
2. Markera nätverkskortet (som standard det finns bara ett nätverkskort) att replikering-installationen använder för identifieringen av virtuella datorer och för att göra en push-installation av mobilitetstjänsten på källdatorer.
3. Väljer du det nätverkskort som replikering-installationen använder för anslutning till Azure. Välj sedan **Spara**. Du kan inte ändra den här inställningen när den har konfigurerats.
4. Om installationen finns bakom en proxyserver, måste du ange proxyinställningar.
    - Ange proxyserverns namn som **http://ip-address** , eller **http://FQDN** . HTTPS-proxyservrar stöds inte.
5. När du tillfrågas om prenumerationen, resursgrupper och valvinformationen, lägger du till information som du antecknade när du har hämtat mallen installation.
6. Godkänn licensavtalet i **Installera programvara från tredje part**. Välj **Ladda ned och installera** för att installera MySQL Server.
7. Välj **Installera VMware PowerCLI**. Kontrollera att alla webbläsarfönster är stängda innan du gör detta. Välj sedan **Fortsätt**.
8. I **Verifiera installationskonfiguration** verifieras förutsättningarna innan du fortsätter.
9. I **Konfigurera vCenter Server/vSphere ESXi-server** anger du FQDN eller IP-adress för vCenter-servern eller vSphere-värden där de virtuella datorer som du vill replikera är placerade. Ange porten som servern lyssnar på. Ange ett eget namn som ska användas för VMware-servern i valvet.
10. Ange autentiseringsuppgifter för konton du [skapade](#prepare-an-account-for-automatic-discovery) för VMware-identifiering. Välj **lägga till** > **fortsätta**.
11. I **konfigurera autentiseringsuppgifter för virtuell dator**, anger du autentiseringsuppgifterna du [skapade](#prepare-an-account-for-mobility-service-installation) för push-installation av mobilitetstjänsten, när du aktiverar replikering för virtuella datorer.  
    - För Windows-datorer måste kontot ha lokal administratörsbehörighet på de datorer som du vill replikera.
    - För Linux anger du information för rotkontot.
12. Välj **Slutför konfigurationen** för att slutföra registreringen.


När replikering installationen är registrerad, migrera Server utvärdering av Azure ansluter till VMware-servrar med hjälp av de angivna inställningarna och identifierar virtuella datorer. Du kan visa identifierade virtuella datorer i **hantera** > **identifierade objekt**i den **andra** fliken.


## <a name="replicate-vms"></a>Replikera virtuella datorer

1. I Azure Migrate-projektet > **servrar**, **Azure Migrate: Servermigrering**, klickar du på **replikera**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. I **replikera**, > **datakällans inställningar** > **är dina datorer virtualiserade?** väljer **Ja, med VMware vSphere**.
3. I **lokala installationen**, Välj namnet på den Azure Migrate-installationen som du har konfigurerat.
4. I **vCenter-servern**, ange namnet på den vCenter-server som hanterar de virtuella datorerna eller vSphere-servern där de virtuella datorerna finns.
5. I **Processervern**, Välj namnet på installationen för replikering.
6. I **gäst autentiseringsuppgifter**, ange VM-administratörskonto som ska användas för push-installation av mobilitetstjänsten. Klicka sedan på **nästa: Virtuella datorer**.

    ![Replikera virtuella datorer](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. I **virtuella datorer**, Välj de datorer som du vill replikera.

    - Om du har kört en utvärdering för de virtuella datorerna, kan du använda VM-storlek och disk typ (premium/standard) rekommendationerna från resultaten. Att göra detta i **importera inställningar från en Azure Migrate-utvärdering?** väljer den **Ja** alternativet.
    - Om du inte kör en utvärdering, eller om du inte vill använda inställningar för utvärdering, väljer den **nr** alternativ.
    - Om du har valt för att använda utvärderingen, Välj grupp virtuella datorer och namn på utvärdering.

8. Kontrollera varje virtuell dator som du vill migrera. Klicka sedan på **nästa: Rikta inställningar**.
9. I **rikta inställningar**, Välj prenumerationen och målregion som du migrerar och ange den resursgrupp där virtuella Azure-datorer kommer att finnas efter migreringen.
10. I **virtuellt nätverk**, Välj Azure virtuellt nätverk/undernät som virtuella Azure-datorer ska anslutas efter migreringen.
11. In **Azure Hybrid Benefit**:

    - Välj **nr** om du inte vill använda Azure Hybrid-förmånen. Klicka sedan på **Nästa**.
    - Välj **Ja** om du har Windows Server-datorer som omfattas med aktiv Software Assurance eller Windows Server-prenumerationer och du vill använda förmånen med de datorer som du migrerar. Klicka sedan på **Nästa**.

12. I **Compute**, granska virtuella datorns namn, storlek, typ av OS-disk och tillgänglighetsuppsättning. Virtuella datorer måste överensstämma med [krav för Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **VM-storlek**: Om du använder rekommendationerna för innehåller VM-storlek listrutan den rekommenderade storleken. Annars hämtar Azure Migrate en storlek baserat på matchande poster i Azure-prenumeration. Du kan också välja en manuell storlek i **Azure VM-storlek**. 
    - **OS-disken**: Ange operativsystemdisken (Start) för den virtuella datorn. OS-disken är en disk som har operativsystemet startprogrammet och installationsprogrammet. 
    - **Tillgänglighetsuppsättning**: Om den virtuella datorn ska vara i en Azure tillgänglighetsuppsättning efter migreringen, ange en uppsättning. Uppsättningen måste finnas i målresursgruppen som du anger för migreringen.

13. I **diskar**, anger du om VM-diskarna ska replikeras till Azure och välj disktyp (standard SSD/HDD- eller premium hanterade diskar) i Azure. Klicka sedan på **Nästa**.
    - Du kan undanta diskar från replikering.
    - Om du undantar diskar, inte på den virtuella Azure-datorn efter migreringen. 

14. I **granska och startar replikeringen**, granskar du inställningarna och klicka på **replikera** att starta den inledande replikeringen för servrar.

> [!NOTE]
> Du kan uppdatera replikeringsinställningarna som helst innan replikeringen startar **hantera** > **datorer replikeras**. Inställningarna kan inte ändras när replikeringen startar.




## <a name="track-and-monitor"></a>Spåra och övervaka

- När du klickar på **replikera** ett jobb startar replikeringen börjar. 
- När starta replikeringen har slutförts startas datorerna sina inledande replikering till Azure.
- När den inledande replikeringen är klar startar deltareplikeringen. Inkrementella ändringar till lokala diskar replikeras med jämna mellanrum till replikeringsdiskar i Azure.


Du kan spåra status för återställningsjobb i portalen meddelanden.

![Spåra jobb](./media/tutorial-migrate-vmware-agent/jobs.png)

Du kan övervaka replikeringsstatus genom att klicka på **replikera servrar** i **Azure Migrate: Servermigrering**.
![Övervakare för replikering](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Kör en testmigrering


När deltareplikeringen börjar kör du en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du gör detta minst en gång för varje dator innan du migrerar den.

- Kör en testmigrering kontrollerar att migreringen fungerar som förväntat, utan att påverka de lokala datorerna som fortsätta att fungera och fortsätta med replikeringen. 
- Testmigrering simulerar migreringen genom att skapa en Azure-dator med hjälp av replikerade data (vanligtvis migrera till ett icke-produktion virtuellt nätverk i Azure-prenumerationen).
- Du kan använda testet replikerade virtuella Azure-datorer att validera migreringen testa appen och åtgärda eventuella problem innan fullständig migrering.

Gör en testmigrering på följande sätt:


1. I **migrering mål** > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **Test migrera servrar**.

     ![Testa migrerade servrar](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Högerklicka på den virtuella datorn för att testa och klicka på **Test migrera**.

    ![Testmigrering](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. I **testmigrering**, Välj den virtuella Azure-nätverket där den virtuella Azure-datorn kommer att finnas efter migreringen. Vi rekommenderar att du använder ett icke-produktion virtuellt nätverk.
4. Den **testa migrering** jobbet startar. Övervaka jobb i portalen meddelanden.
5. När migreringen är klar kan du visa den migrerade virtuella Azure-datorer i **virtuella datorer** i Azure-portalen. Namnet på datorn har ett suffix **-Test**.
6. När testet är klart, högerklickar du på Azure VM i **datorer replikeras**, och klicka på **Rensa testmigrering**.

    ![Rensa migrering](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat, kan du migrera lokala datorer.

1. I Azure Migrate-projektet > **servrar** > **Azure Migrate: Servermigrering**, klickar du på **replikera servrar**.

    ![Replikera servrar](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. I **datorer replikeras**, högerklicka på den virtuella datorn > **migrera**.
3. I **migrera** > **stänga av virtuella datorer och utföra en planerad migrering utan att förlora data**väljer **Ja** > **OK** .
    - Som standard Azure Migrate stänger av den lokala virtuella datorn och kör en på begäran-replikering om du vill synkronisera alla VM-ändringar som gjorts sedan den senaste replikeringen som utfördes. Detta säkerställer att inga data går förlorade.
    - Om du inte vill att stänga av den virtuella datorn, Välj **Nej**
4. Det startar ett migreringsjobb för den virtuella datorn. Spåra jobbet i Azure-aviseringar.
5. När jobbet har slutförts kan du visa och hantera den virtuella datorn från den **virtuella datorer** sidan.

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar högerklickar du på den virtuella datorn > **stoppa migrering**. Detta stoppar replikeringen för den lokala datorn och rensar replikering statusinformation för den virtuella datorn.
2. Installera Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) eller [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till de migrerade Azure VM-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metodtips för efter migrering

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Låsa och begränsa åtkomsten för inkommande trafik med [Azure Security Center – Just-in-time-administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuera [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
-  Överväg att distribuera [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) för att övervaka användning och utgifter.


## <a name="next-steps"></a>Nästa steg

Undersöka den [migrering molnresa](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) i Azure Cloud Adoption Framework.
