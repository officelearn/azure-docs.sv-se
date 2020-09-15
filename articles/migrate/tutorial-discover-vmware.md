---
title: Identifiera virtuella VMware-datorer med utvärdering av Azure Migrate Server
description: Lär dig att identifiera lokala virtuella VMware-datorer med verktyget för Azure Migrate Server bedömning
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 55a7f15233d32f5ee60a57ad34e8b0177ca29ff3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064574"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Självstudie: identifiera virtuella VMware-datorer med Server utvärdering

Som en del av migreringen till Azure identifierar du din lokala inventering och dina arbets belastningar. 

Den här självstudien visar hur du identifierar lokala virtuella VMware-datorer (VM: ar) med verktyget Azure Migrate: Server bedömning med en förenklad Azure Migrate-apparat. Du distribuerar installationen som en virtuell VMware-dator för att kontinuerligt identifiera VM-och prestanda-metadata, appar som körs på virtuella datorer och virtuella dator beroenden.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Förbered VMware-miljön för identifiering.
> * Skapa ett Azure Migrate-projekt.
> * Konfigurera Azure Migrate-enheten.
> * Starta kontinuerlig identifiering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ där det är möjligt.  

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du kontrol lera att du har dessa krav på plats.


**Krav** | **Information**
--- | ---
**vCenter Server/ESXi-värd** | Du behöver ett vCenter Server som kör version 5,5, 6,0, 6,5 eller 6,7.<br/><br/> Virtuella datorer måste finnas på en ESXi-värd som kör version 5,5 eller senare.<br/><br/> På vCenter Server tillåter du inkommande anslutningar på TCP-port 443, så att du kan samla in utvärderings data på enheten.<br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du ansluter från installations programmet till servern för att starta identifieringen.<br/><br/> På den EXSi-server som är värd för de virtuella datorerna kontrollerar du att inkommande åtkomst är tillåten på TCP-port 443 för identifiering av appar.
**Enhet** | vCenter Server behöver resurser för att allokera en virtuell dator för Azure Migrates apparaten:<br/><br/> - Windows Server 2016<br/><br/> – 32 GB RAM, åtta virtuella processorer och cirka 80 GB disk lagring.<br/><br/> – En extern virtuell växel och Internet åtkomst på för den virtuella datorn, direkt eller via en proxyserver.
**Virtuella datorer** | För att kunna använda den här självstudien måste virtuella Windows-datorer köra Windows Server 2016, 2012 R2, 2012 eller 2008 R2.<br/><br/> Virtuella Linux-datorer måste köra Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 eller CentOS 5/6/7.<br/><br/> Virtuella datorer behöver VMware-verktyg (en senare version än 10.2.0) installerade och körs.<br/><br/> Windows PowerShell 2,0 eller senare bör installeras på virtuella Windows-datorer.


## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett Azure Migrate-projekt och registrera Azure Migrate-enheten måste du ha ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory appar.

Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster**väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-vmware/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt. 
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst**söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning**klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-vmware/azure-account-access.png)

6. I **Lägg till roll tilldelning**väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-vmware/assign-role.png)

7. I portalen söker du efter användare och under **tjänster**väljer **du användare**.
8. I **användar inställningar**kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-vmware/register-apps.png)


## <a name="prepare-vmware"></a>Förbereda VMware

På vCenter Server skapar du ett konto som enheten kan använda för att få åtkomst till vCenter Server och kontrollerar att de portar som krävs är öppna. Du måste också ha ett konto som kan användas av enheten för att komma åt virtuella datorer. 

### <a name="create-an-account-to-access-vcenter"></a>Skapa ett konto för åtkomst till vCenter

I vSphere-webbklient skapar du ett konto på följande sätt:

1. Med hjälp av ett konto med administratörs behörighet går du till vSphere-webbklienten > väljer **Administration**.
2. **Åtkomst**väljer du **SSO-användare och-grupper**.
3. I **användare lägger du**till en ny användare.
4. I **ny användare**, anger du konto informationen. Klicka sedan på **OK**.
5. I **globala behörigheter**väljer du användar kontot och tilldelar kontot den **skrivskyddade** rollen. Klicka sedan på **OK**.
6. I **roller** > väljer du **skrivskyddad** roll och i **privilegier**väljer du **gäst åtgärder**. De här behörigheterna krävs för att identifiera appar som körs på virtuella datorer och analysera VM-beroenden.
 
    ![Kryss ruta för att tillåta gäst åtgärder på den skrivskyddade rollen](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Skapa ett konto för åtkomst till virtuella datorer

Enheten får åtkomst till virtuella datorer för att identifiera appar och analysera virtuella dator beroenden. Installations programmet installerar inte några agenter på virtuella datorer.

1. Skapa ett lokalt administratörs konto som kan användas av enheten för att identifiera appar och beroenden på virtuella Windows-datorer.
2. För Linux-datorer skapar du ett användar konto med rot privilegier eller alternativt ett användar konto med dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate stöder en autentiseringsuppgift för app-Discovery på alla Windows-servrar och en autentiseringsuppgift för app-Discovery på alla Linux-datorer.


## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt Azure Migrate-projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt**väljer du **skapa projekt**.
5. I **skapa projekt**väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-vmware/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills Azure Migrate-projektet har distribuerats.

Verktyget **Azure Migrate: Server bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Konfigurera installationen

Den här självstudien konfigurerar installationen på en virtuell VMware-dator.
- Du laddar ned installations mal len och importerar den till vCenter Server för att skapa den virtuella dator enheten.
- När du har skapat installationen ställer du in den för första gången och registrerar den med Azure Migrate-projektet.

> [!NOTE]
> Om du av någon anledning inte kan konfigurera installationen med hjälp av mallen kan du konfigurera den med hjälp av ett PowerShell-skript. [Läs mer](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="download-the-ova-template"></a>Ladda ned mallen för ägg

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**väljer du **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor**.
3. Välj **Ladda ned** för att ladda ned filer för embryon.

   ![Val för nedladdning av en ägg fil](./media/tutorial-discover-vmware/download-ova.png)


### <a name="deploy-the-appliance-vm"></a>Distribuera VM-enheten

Importera den nedladdade filen och skapa en virtuell dator:

1. Logga in på vSphere-klient konsolen med det vCenter-konto som du skapade.
2. På fliken **Virtual Machines** > **åtgärder** väljer du **distribuera OVF-mallen**för att öppna guiden Distribuera OVF-mall.
3. I **Välj en OVF-mall**väljer du **lokal fil**och bläddrar till nedladdnings mal len. 
   ![vSphere meny kommando för att distribuera en OVF-mall](./media/tutorial-discover-vmware/deploy-ovf.png)
3. Ange resten av guidens inställnings guide, inklusive distributions platsen, värd/kluster där den virtuella datorn ska köras och inställningar för lagring/nätverk.
4. Klicka på **Slutför**i **redo att slutföra**. Systemet importerar och distribuerar mallen. 
5. Efter distributionen visas den virtuella datorn på fliken **Virtual Machines** .
6. Välj den virtuella datorns >- **effekt**.
7. I VMware-fjärrkonsolen > **licens villkor**, granska/Godkänn licens villkoren.
8. I **Anpassa inställningar**anger du ett lösen ord för användar kontot.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Kontrol lera den virtuella datorns åtkomst.

1. Kontrol lera att den virtuella datorn kan ansluta till Azure.
    - I det offentliga molnet ska installations datorn kunna ansluta till dessa [URL: er](migrate-appliance.md#public-cloud-urls).
    - I det offentliga molnet bör den virtuella datorn kunna ansluta till dessa [offentliga URL: er](migrate-appliance.md#government-cloud-urls).
2. Se till att portarna är öppna på installations datorn:

    - Tillåt inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.
    - Tillåt inkommande anslutningar på port 44368 för att fjärrans luta till appens webbapp med URL: en: https://<-IP-eller-Name>:44368.
    - Tillåt utgående anslutningar på port 443 (HTTPS) för att skicka metadata för identifiering och prestanda till Azure Migrate.

### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Logga in på den virtuella dator enheten. 
    - Installations programmets webbapp öppnas automatiskt i en webbläsare.
    - Alternativt kan du öppna appen från apparatens skriv bord med genvägen till appen.
2. I Azure Migrate-appens webbapp > **konfigurerar du krav**, granskar/godkänner licens villkoren och läser informationen om tredje part.
3. Enheten kontrollerar att den virtuella datorn har Internet åtkomst och att tiden på den virtuella datorn är synkroniserad med Internet tid.
    - Om du använder en proxyserver klickar du på **Konfigurera proxy** och anger proxyadress och port (i formatet http://ProxyIPAddress eller http://ProxyFQDN) . 
    - Ange autentiseringsuppgifter om proxyn kräver autentisering. Endast HTTP-proxy stöds.
4. Installations programmet installerar de senaste Azure Migrate uppdateringarna och kontrollerar om VMWare vSphere Virtual Disk Development Kit (VDDK) har installerats.
5. Om du vill installera VDDK 6,7 klickar du på **Ladda ned** för att ladda ned den från VMware och extraherar den hämtade ZIP-filens innehåll till den angivna platsen på enheten. Klicka sedan på **Verifiera och installera**.

    ![Krav sidan för att verifiera Internet åtkomst och inställningar för installation av enheten](./media/tutorial-discover-vmware/prerequisites.png)
  
3. När VDDK har installerats granskar du inställningarna och klickar på **Fortsätt**.

### <a name="register-the-appliance"></a>Registrera produkten 

1. I **registrera med Azure Migrate**väljer du **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.

    ![Klicka på Logga in för att starta registreringen av enheten](./media/tutorial-discover-vmware/register.png)

1. Logga in med ditt användar namn och lösen ord för Azure på sidan **Logga in** . Inloggning med en PIN-kod stöds inte.

    ![Knappen Logga in för att registrera installationen](./media/tutorial-discover-vmware/sign-in.png)
1. När du har loggat in går du tillbaka till appen.
1. I **registrera med Azure Migrate**väljer du den prenumeration som Azure Migrate projektet skapades i och väljer sedan projektet.
1. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
3. Välj **Register** (Registrera). Klicka sedan på **Fortsätt**. Ett meddelande visar att registreringen är klar.

    ![Fyll i namn på prenumeration, projekt och apparat och registrera produkten](./media/tutorial-discover-vmware/success-register.png)

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installationen måste ansluta till vCenter Server för att identifiera virtuella datorer.

### <a name="connect-to-vcenter-server"></a>Anslut till vCenter Server

Installationen måste ansluta till vCenter Server för att identifiera virtuella datorer.

1. I **ange vCenter Server**anger du namnet (FQDN) eller IP-adressen för vCenter Server.
2. Lämna standard porten eller ange en anpassad port som vCenter Server lyssnar på.
3. I **användar namn** och **lösen ord**anger du vCenter Server kontoautentiseringsuppgifter som installeras av enheten för att identifiera virtuella datorer på vCenter Server.
3. Välj **Verifiera anslutning**för att kontrol lera att installationen kan ansluta till vCenter Server. 

    ![vCenter Server-information och knapp för att verifiera en anslutning till vCenter Server](./media/tutorial-discover-vmware/vcenter.png)

1. I **identifiera program och beroenden på virtuella datorer**klickar du på **Lägg till autentiseringsuppgifter**.
1. Ange de Windows/Linux-autentiseringsuppgifter som du använder för identifiering av appar och beroende analyser.
1. Klicka på **Spara och starta identifiering**för att starta identifierings processen.

    ![Lägg till autentiseringsuppgifter för identifiering av appar och beroende analyser och starta kontinuerlig identifiering. ](./media/tutorial-discover-vmware/start-discovery.png)


När identifieringen startar:

- Det tar ungefär 15 minuter för identifierade VM-metadata som visas i portalen.
- Det tar lite tid att identifiera appar. Tiden beror på antalet virtuella datorer som identifieras. För 500 virtuella datorer tar det cirka en timme för program inventeringen att visas i Azure Migrate portalen.



## <a name="verify-discovered-vms-in-the-portal"></a>Verifiera identifierade virtuella datorer i portalen

Efter identifieringen kan du kontrol lera att de virtuella datorerna visas i Azure Portal:

1. Öppna instrument panelen för Azure Migrate.
2. I **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering**väljer du den ikon som visar antalet för **identifierade servrar**.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella VMware-datorer](tutorial-assess-vmware.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---vmware) som enheten samlar in under identifieringen.
