---
title: Identifiera virtuella Hyper-V-datorer med Azure Migrate Server-utvärdering
description: Lär dig att identifiera lokala virtuella Hyper-V-datorer med verktyget för Azure Migrate Server bedömning.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: fdf96b5767b461953fa88923aaa5050aff4613bc
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064370"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Självstudie: identifiera virtuella Hyper-V-datorer med Server utvärdering

Som en del av migreringen till Azure identifierar du din lokala inventering och dina arbets belastningar. 

I den här självstudien lär du dig att identifiera lokala virtuella Hyper-V-datorer med verktyget Azure Migrate: Server bedömning med hjälp av en förenklad Azure Migrate-apparat. Du distribuerar installationen som en virtuell Hyper-V-dator för att kontinuerligt identifiera metadata för dator och prestanda.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure-konto
> * Förbered Hyper-V-miljön för identifiering.
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
**Hyper-V-värd** | Hyper-V-värdar som de virtuella datorerna finns i kan vara fristående eller i ett kluster.<br/><br/> Värden måste köra Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2.<br/><br/> Kontrol lera att inkommande anslutningar är tillåtna på WinRM-port 5985 (HTTP), så att enheten kan ansluta till att hämta VM-metadata och prestanda data med hjälp av en Common Information Model-session (CIM).
**Distribution av utrustning** | vCenter Server behöver resurser för att allokera en virtuell dator för enheten:<br/><br/> - Windows Server 2016<br/><br/> – 32 GB RAM-minne<br/><br/> – Åtta virtuella processorer<br/><br/> – Cirka 80 GB disk lagring.<br/><br/> – En extern virtuell växel.<br/><br/> – Internet åtkomst på för den virtuella datorn, direkt eller via en proxy.
**Virtuella datorer** | Virtuella datorer kan köra ett Windows-eller Linux-operativsystem. 

Innan du börjar kan du [Granska de data](migrate-appliance.md#collected-data---hyper-v) som samlas in under identifieringen.

## <a name="prepare-an-azure-user-account"></a>Förbereda ett Azure-användarkonto

Om du vill skapa ett Azure Migrate-projekt och registrera Azure Migrate-enheten måste du ha ett konto med:
- Deltagar-eller ägar behörigheter för en Azure-prenumeration.
- Behörighet att registrera Azure Active Directory appar.

Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration. Om du inte är prenumerations ägare kan du arbeta med ägaren för att tilldela behörigheterna på följande sätt:


1. I Azure Portal söker du efter "prenumerationer" och under **tjänster**väljer du **prenumerationer**.

    ![Sök i rutan för att söka efter Azure-prenumerationen](./media/tutorial-discover-hyper-v/search-subscription.png)

2. På sidan **prenumerationer** väljer du den prenumeration där du vill skapa ett Azure Migrate-projekt. 
3. I prenumerationen väljer du **åtkomst kontroll (IAM)**  >  **kontrol lera åtkomst**.
4. I **kontrol lera åtkomst**söker du efter det relevanta användar kontot.
5. I **Lägg till en roll tilldelning**klickar du på **Lägg till**.

    ![Sök efter ett användar konto för att kontrol lera åtkomst och tilldela en roll](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. I **Lägg till roll tilldelning**väljer du rollen deltagare eller ägare och väljer kontot (azmigrateuser i vårt exempel). Klicka sedan på **Spara**.

    ![Öppnar sidan Lägg till roll tilldelning för att tilldela kontot en roll](./media/tutorial-discover-hyper-v/assign-role.png)

7. I portalen söker du efter användare och under **tjänster**väljer **du användare**.
8. I **användar inställningar**kontrollerar du att Azure AD-användare kan registrera program (anges till **Ja** som standard).

    ![Verifiera i användar inställningar som användare kan registrera Active Directory appar](./media/tutorial-discover-hyper-v/register-apps.png)

## <a name="prepare-hyper-v-hosts"></a>Förbereda Hyper-V-värdar

Konfigurera ett konto med administratörs åtkomst på Hyper-V-värdarna. Enheten använder det här kontot för identifiering.

- Alternativ 1: Förbered ett konto med administratörs åtkomst till Hyper-V-värddatorn.
- Alternativ 2: Förbered ett lokalt administratörs konto eller ett domän administratörs konto och Lägg till kontot i dessa grupper: fjärrhanterings användare, Hyper-V-administratörer och användare av prestanda övervakning.


## <a name="set-up-a-project"></a>Konfigurera ett projekt

skapa ett nytt Azure Migrate-projekt.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt**väljer du **skapa projekt**.
5. I **skapa projekt**väljer du din Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-discover-hyper-v/new-project.png)

7. Välj **Skapa**.
8. Vänta några minuter tills Azure Migrate-projektet har distribuerats.

Verktyget **Azure Migrate: Server bedömning** läggs till som standard i det nya projektet.

![Sida som visar verktyget för Server bedömning som har lagts till som standard](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Konfigurera installationen

Den här självstudien konfigurerar installationen på en virtuell Hyper-V-dator.
- Du laddar ned installations mal len och importerar mallen till vCenter Server för att skapa den virtuella dator enheten.
- När du har skapat enheten ställer du in den för första gången och registrerar den med Azure Migrate-projektet.

> [!NOTE]
> Om du av någon anledning inte kan konfigurera installationen med hjälp av en mall kan du konfigurera den med hjälp av ett PowerShell-skript. [Läs mer](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="download-the-vhd"></a>Ladda ned den virtuella hård disken

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**väljer du **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med Hyper-V**.
3. Välj **Ladda ned** för att ladda ned VHD-filen.

   ![Alternativ för att ladda ned en ägg fil](./media/tutorial-discover-hyper-v/download-vhd.png)



### <a name="deploy-the-appliance-vm"></a>Distribuera VM-enheten

Importera den nedladdade filen och skapa en virtuell dator:

1. Ladda ned den zippade VHD-filen till den Hyper-V-värd där den virtuella installations datorn ska placeras.
2. Extrahera den zippade filen.

    - På den extraherade platsen packas filen upp i en mapp med namnet AzureMigrateAppliance_VersionNumber.
    - Den här mappen innehåller en undermapp, som även kallas AzureMigrateAppliance_VersionNumber.
    - Den här undermappen innehåller tre ytterligare undermappar – ögonblicks bilder, virtuella hård diskar och Virtual Machines.

3. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator**i **åtgärder**.

    ![Meny alternativ i Hyper-V Manager för att importera den virtuella hård disken](./media/tutorial-discover-hyper-v/deploy-vhd.png)

4. I guiden Importera virtuell dator > **innan du börjar klickar du**på **Nästa**.
5. I **hitta mapp**väljer du mappen **Virtual Machines** . Klicka på **Nästa**.
6. I **Välj virtuell dator**klickar du på **Nästa**.
7. I **Välj import typ**klickar **du på Kopiera den virtuella datorn (skapa ett nytt unikt ID)**. Klicka på **Nästa**.
8. I **Välj mål**låter du standardvärdet vara kvar. Klicka på **Nästa**.
9. Lämna standardinställningen i **Storage Folders**. Klicka på **Nästa**.
10. I **Välj nätverk**anger du den virtuella växel som den virtuella datorn ska använda. Växeln behöver Internet anslutning för att skicka data till Azure. [Lär dig mer](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) om att skapa en virtuell växel.
11. Granska inställningarna i **Sammanfattning**. Klicka sedan på **Slutför**.
12. Starta den virtuella datorn i Hyper-V Manager > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Kontrol lera åtkomsten till enheten enligt följande:

1. Kontrol lera att den virtuella datorn kan ansluta till Azure.
    - I det offentliga molnet ska installations datorn kunna ansluta till dessa [URL: er](migrate-appliance.md#public-cloud-urls).
    - I det offentliga molnet bör enheten kunna ansluta till dessa [offentliga URL: er](migrate-appliance.md#government-cloud-urls).
2. Se till att portarna är öppna på installations datorn:

    - Tillåt inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.
    - Tillåt inkommande anslutningar på port 44368 för att fjärrans luta till appens webbapp med URL: en: https:// \<appliance-ip-or-name> : 44368.
    - Tillåt utgående anslutningar på port 443 (HTTPS) för att skicka metadata för identifiering och prestanda till Azure Migrate.


### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

> [!NOTE]
> Om du ställer in installationen med hjälp av ett [PowerShell-skript](deploy-appliance-script.md) i stället för den hämtade virtuella hård disken är de två första stegen i den här proceduren inte relevanta.

1. I Hyper-V Manager > **Virtual Machines**högerklickar du på den virtuella datorn > **Anslut**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
1. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
      - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port i formuläret http://ProxyIPAddress eller http://ProxyFQDN .
      - Ange autentiseringsuppgifter om proxyn kräver autentisering.
      - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet-tid för att VM-identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server Assessment kontrollerar att installations programmet har de senaste uppdateringarna installerade.

### <a name="register-the-appliance"></a>Registrera produkten 

1. I **registrera med Azure Migrate**väljer du **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.

    ![Klicka på Logga in för att starta registreringen av enheten](./media/tutorial-discover-hyper-v/register.png)

1. Logga in med ditt användar namn och lösen ord för Azure på sidan **Logga in** . Inloggning med en PIN-kod stöds inte.

    ![Knappen Logga in för att registrera installationen](./media/tutorial-discover-hyper-v/sign-in.png)
1. När du har loggat in går du tillbaka till appen.
1. I **registrera med Azure Migrate**väljer du den prenumeration som Azure Migrate projektet skapades i och väljer sedan projektet.
1. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
3. Välj **Register** (Registrera). Klicka sedan på **Fortsätt**. Ett meddelande visar att registreringen är klar.

    ![Fyll i namn på prenumeration, projekt och apparat och registrera produkten](./media/tutorial-discover-hyper-v/success-register.png)


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB-VHD: er

Om du kör virtuella hård diskar på SMB: er måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdarna. Om du vill göra det aktiverar du varje värd för att agera som ombud för-enheten. Om du har följt självstudierna i ordning gjorde du detta i föregående självstudie när du för beredde Hyper-V för utvärdering och migrering. Du bör antingen konfigurera CredSSP för värdarna [manuellt](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials)eller genom att [köra ett skript](tutorial-prepare-hyper-v.md#run-the-script) som gör detta.

Aktivera på enheten enligt följande:

#### <a name="option-1"></a>Alternativ 1

Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempel värd namn.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Exempel: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Alternativ 2

Du kan också göra detta i redigerare för lokalt grupprincipobjekt på enheten:

1. I dator konfiguration för **lokal dator princip**  >  **Computer Configuration**klickar du på **administrativa mallar**  >  **System**  >  **delegering av systemautentiseringsuppgifter**.
2. Dubbelklicka på **Tillåt att delegera nya autentiseringsuppgifter**och välj **aktive rad**.
3. I **alternativ**klickar du på **Visa**och lägger till varje Hyper-V-värd som du vill identifiera i listan med **WSMan/** som prefix.
4. I **delegering av autentiseringsuppgifter**dubbelklickar du sedan på **Tillåt att delegera nya AUTENTISERINGSUPPGIFTER med endast NTLM-** serverautentisering. Lägg återigen till varje Hyper-V-värd som du vill identifiera i listan, med **WSMan/** som prefix.



## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Enheten måste ansluta till Hyper-V-värdar för att identifiera virtuella datorer.


### <a name="connect-to-hyper-v-hosts"></a>Anslut till Hyper-V-värdar

1. I **användar namn** och **lösen ord**anger du de kontoautentiseringsuppgifter som installationen ska använda för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **Spara information**.
2. Klicka på **Lägg till värd**och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validate** (Validera). Efter verifieringen visas antalet virtuella datorer som kan identifieras på varje värd/kluster.
    - Om verifieringen Miss lyckas för en värd granskar du felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort värdar eller kluster väljer du > **ta bort**.
    - Du kan inte ta bort en speciell värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster även om det finns problem med specifika värdar i klustret.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar cirka 1,5 minuter per värd för metadata för identifierade servrar som visas i Azure Portal.


### <a name="verify-discovered-vms-in-the-portal"></a>Verifiera identifierade virtuella datorer i portalen

Efter identifieringen kan du kontrol lera att de virtuella datorerna visas i Azure Portal:

1. Öppna instrument panelen för Azure Migrate.
2. I **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering**väljer du den ikon som visar antalet för **identifierade servrar**.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md) för migrering till virtuella Azure-datorer.
- [Granska de data](migrate-appliance.md#collected-data---hyper-v) som enheten samlar in under identifieringen.