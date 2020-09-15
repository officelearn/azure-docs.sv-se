---
title: Identifiera AWS-instanser med Azure Migrate Server-utvärdering
description: Lär dig hur du identifierar AWS-instanser med Azure Migrate Server-utvärdering.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 6924182e6425b0927583e8c6498c7073c4ff1806
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064526"
---
# <a name="tutorial-discover-aws-instances-with-server-assessment"></a>Självstudie: identifiera AWS-instanser med Server utvärdering

Som en del av migreringen till Azure identifierar du dina servrar för utvärdering och migrering.

Den här självstudien visar hur du identifierar Amazon Web Services-instanser (AWS) med verktyget Azure Migrate: Server bedömning med en förenklad Azure Migrate-installation. Du distribuerar installationen som en fysisk server för att kontinuerligt identifiera metadata för dator och prestanda.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto.
> * Förbered AWS-instanser för identifiering.
> * Skapa ett Azure Migrate-projekt.
> * Konfigurera Azure Migrate-enheten.
> * Starta kontinuerlig identifiering.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ.  

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien måste du kontrol lera att du har dessa krav på plats.

**Krav** | **Information**
--- | ---
**Enhet** | Du behöver en virtuell EC2-dator för att köra Azure Migrate-installationen. Datorn ska ha:<br/><br/> – Windows Server 2016 installerat. Det finns inte stöd för att köra installationen på en dator med Windows Server 2019.<br/><br/> – 16 GB RAM, 8 virtuella processorer, cirka 80 GB disk lagring och en extern virtuell växel.<br/><br/> – En statisk eller dynamisk IP-adress, med Internet åtkomst, antingen direkt eller via en proxyserver.
**Windows-instanser** | Tillåt inkommande anslutningar på WinRM-port 5985 (HTTP), så att enheten kan hämta konfigurations-och prestanda-metadata.
**Linux-instanser** | Tillåt inkommande anslutningar på port 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett Azure Migrate-projekt och registrera Azure Migrate-enheten måste du ha ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory appar.

Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:

1. I Azure Portal söker du efter "prenumerationer" och under **tjänster**väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-aws/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt. 
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst**söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning**klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-aws/azure-account-access.png)

6. I **Lägg till roll tilldelning**väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-aws/assign-role.png)

7. I portalen söker du efter användare och under **tjänster**väljer **du användare**.
8. I **användar inställningar**kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-aws/register-apps.png)


## <a name="prepare-aws-instances"></a>Förbereda AWS-instanser

Konfigurera ett konto som kan användas av enheten för att komma åt AWS-instanser.

- För Windows-servrar konfigurerar du ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Lägg till användar kontot i följande grupper:-fjärr styrnings användare – prestanda övervaknings användare – prestanda loggar användare.
 - För Linux-servrar behöver du ett rotkonto på de Linux-servrar som du vill identifiera.


## <a name="set-up-a-project"></a>Konfigurera ett projekt

Skapa ett nytt Azure Migrate-projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt**väljer du **skapa projekt**.
5. I **skapa projekt**väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-aws/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills Azure Migrate-projektet har distribuerats.

Verktyget **Azure Migrate: Server bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-aws/added-tool.png)

## <a name="set-up-the-appliance"></a>Konfigurera installationen

Om du vill konfigurera installationen på en AWS-instans laddar du ned och kör ett installations skript på en [lämplig instans](#prerequisites). När du har skapat enheten kontrollerar du att den kan ansluta till Azure, konfigurera den för första gången och registrera den med Azure Migrate projektet.

### <a name="download-the-installer-script"></a>Ladda ned installations skriptet

Ladda ned den zippade filen för enheten.

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **inte virtualiserad/annan**.
3. Klicka på **Ladda ned** för att ladda ned den zippade filen.



### <a name="install-the-appliance"></a>Installera installationen

Installations skriptet gör följande:

- Installerar agenter och ett webb program för identifiering och utvärdering av AWS.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämta och installera en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständig inställnings information för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten.  Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp där innehållet har extraherats från den hämtade zippade filen.
4. Kör skriptet med namnet **AzureMigrateInstaller.ps1** genom att köra följande kommando:

    - För det offentliga molnet: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - För Azure Government: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skriptet startar webb programmet för installationen när det har slutförts.

Om du kommer över alla problem kan du komma åt skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log för fel sökning.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Kontrol lera åtkomsten till enheten enligt följande:

1. Se till att installations datorn kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.
2. Se till att portarna är öppna på installations datorn:

    - Tillåt inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.
    - Tillåt inkommande anslutningar på port 44368 för att fjärrans luta till appens webbapp med URL: en: https://<-IP-eller-Name>:44368.
    - Tillåt utgående anslutningar på port 443 (HTTPS) för att skicka metadata för identifiering och prestanda till Azure Migrate.


### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att välja genvägen till appen.
2. I Azure Migrate-appens webbapp > **konfigurerar du krav**, granskar/godkänner licens villkoren och läser informationen om tredje part.
3. Enheten kontrollerar att den virtuella datorn har Internet åtkomst och att tiden på den virtuella datorn är synkroniserad med Internet tid.
    - Om du använder en proxyserver klickar du på **Konfigurera proxy** och anger proxyadress och port (i formatet http://ProxyIPAddress eller http://ProxyFQDN) . 
    - Ange autentiseringsuppgifter om proxyn kräver autentisering. Endast HTTP-proxy stöds.
4. Installations programmet installerar de senaste Azure Migrate uppdateringarna
5. Granska inställningarna och klicka på **Fortsätt**.

       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. I **registrera med Azure Migrate**väljer du **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.

    ![Klicka på Logga in för att starta registreringen av enheten](./media/tutorial-discover-aws/register.png)

1. Logga in med ditt användar namn och lösen ord för Azure på sidan **Logga in** . Inloggning med en PIN-kod stöds inte.

    ![Knappen Logga in för att registrera installationen](./media/tutorial-discover-aws/sign-in.png)
1. När du har loggat in går du tillbaka till appen.
1. I **registrera med Azure Migrate**väljer du den prenumeration som Azure Migrate projektet skapades i och väljer sedan projektet.
1. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
3. Välj **Register** (Registrera). Klicka sedan på **Fortsätt**. Ett meddelande visar att registreringen är klar.

    ![Fyll i namn på prenumeration, projekt och apparat och registrera produkten](./media/tutorial-discover-aws/success-register.png)


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Enheten ansluter till AWS-instansen för identifiering.

1. Klicka på **Lägg till autentiseringsuppgifter** för att ange de kontoautentiseringsuppgifter som installationen ska använda för att identifiera servrar.  
2. Logga in med användar namn och lösen ord. Det finns inte stöd för att logga in med en nyckel. Användaren måste också vara en rot inloggning eller en del av den lokala administratörs gruppen.
3. Ange **operativ systemet**, ett eget namn för autentiseringsuppgifterna och användar namn och lösen ord. Klicka sedan på **Lägg till**.
Du kan lägga till flera autentiseringsuppgifter för Windows-och Linux-servrar.
4. Klicka på **Lägg till Server**och ange Server information – FQDN/IP-adress och eget namn på autentiseringsuppgifter (en post per rad) för att ansluta till servern.
5. Klicka på **Validate** (Validera). Efter verifieringen visas en lista över de servrar som kan identifieras.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort en server väljer du > **ta bort**.
6. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar vanligt vis mindre än två minuter per server för metadata att visas i Azure Portal.


## <a name="verify-discovered-vms-in-the-portal"></a>Verifiera identifierade virtuella datorer i portalen

Efter identifieringen kan du kontrol lera att de virtuella datorerna visas i Azure Portal:

1. Öppna instrument panelen för Azure Migrate.
2. I **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering**väljer du den ikon som visar antalet för **identifierade servrar**.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera fysiska servrar](tutorial-migrate-aws-virtual-machines.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---physical) som enheten samlar in under identifieringen.
