---
title: Konfigurera en Azure Migrate-apparat för VMware
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för att utvärdera och migrera virtuella VMware-datorer.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 24ba978d776da375b417fb67823651727836cb22
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386749"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurera en installation för virtuella VMware-datorer

Följ den här artikeln för att konfigurera Azure Migrate-installationen för utvärdering med verktyget [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool) och för återställning utan agent med hjälp av [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

[Azure Migrate](migrate-appliance.md) -installationen är en förenklad installation som används av Azure Migrate: Server utvärdering och Server migrering för att identifiera lokala virtuella VMware-datorer, skicka VM-metadata/prestanda data till Azure och för replikering av virtuella VMware-datorer under en agent lös migrering.

Du kan distribuera installationen på ett par olika sätt:

- Konfigurera på en virtuell VMware-dator med en Hämtad mall för ägg. Detta är den metod som beskrivs i den här artikeln.
- Konfigurera på en virtuell VMware-dator eller en fysisk dator med ett PowerShell-skript. [Den här metoden](deploy-appliance-script.md) ska användas om du inte kan konfigurera en virtuell dator med en behållar mall eller om du är i Azure-myndigheter.

När du har skapat enheten kontrollerar du att den kan ansluta till Azure Migrate: Server utvärdering, konfigurera den för första gången och registrera den med Azure Migrate projektet.


## <a name="appliance-deployment-ova"></a>Installation av utrustning (ägg)

Så här konfigurerar du installationen av en tjänstmall:
- Hämta en mall för en embryo-fil och importera den till vCenter Server.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-ova-template"></a>Ladda ned mallen för ägg

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **Ja, med VMware vSphere hypervisor**.
3. Klicka på **Ladda ned** för att ladda ned .OVA-mallfilen.

  ![Alternativ för att ladda ned en ägg fil](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash-värdet för de ägg:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. För den senaste versionen av produkten ska den genererade hashen matcha de här [inställningarna](./tutorial-assess-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator.

1. I klient konsolen för vSphere klickar du på **fil**  >  **distribution OVF mall**.
![Meny kommando för att distribuera en OVF-mall](./media/tutorial-assess-vmware/deploy-ovf.png)

2. I guiden Distribuera OVF-mall > **källa**anger du platsen för ägg filen.
3. I **namn** och **plats**anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
5. I **värd/kluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
6. I **lagring**anger du lagrings målet för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverks mappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


## <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.


## <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången. Om du distribuerar installationen med hjälp av ett skript i stället för en beredskaps mall, gäller inte de två första stegen i proceduren.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
4. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port i formuläret http://ProxyIPAddress eller http://ProxyFQDN .
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate kontrollerar att de senaste uppdateringarna har installerats.
    - **Installera vddk**: Azure Migrate kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats.
        - Azure-migreringar använder VDDK för att replikera datorer under migreringen till Azure.
        - Hämta VDDK 6,7 från VMware och extrahera det hämtade ZIP-innehållet till den angivna platsen på enheten.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **Logga**in. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På fliken nytt loggar du in med dina Azure-autentiseringsuppgifter.
    - Logga in med ditt användar namn och lösen ord.
    - Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
2. Välj den prenumeration där Azure Migrate projektet skapades. Välj sedan projektet.
3. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller mindre.
4. Klicka på **Registrera**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Starta kontinuerlig identifiering genom att tillhandahålla vCenter Server och autentiseringsuppgifter för virtuell dator

Installations programmet måste ansluta till vCenter Server för att identifiera konfigurations-och prestanda data för de virtuella datorerna.

### <a name="specify-vcenter-server-details"></a>Ange vCenter Server-information
1. I **ange vCenter Server information**anger du namnet (FQDN) eller IP-adressen för vCenter Server. Du kan lämna standard porten eller ange en anpassad port som vCenter Server lyssnar på.
2. I **användar namn** och **lösen ord**anger du de skrivskyddade kontoautentiseringsuppgifter som installeras av enheten för att identifiera virtuella datorer på vCenter-servern. Du kan begränsa identifieringen genom att begränsa åtkomsten till vCenter-kontot. [Läs mer](set-discovery-scope.md).
3. Klicka på **Verifiera anslutning** för att kontrol lera att installationen kan ansluta till vCenter Server.

### <a name="specify-vm-credentials"></a>Ange autentiseringsuppgifter för virtuell dator
För identifiering av program, roller och funktioner och visualisering av beroenden för de virtuella datorerna kan du ange en VM-autentiseringsuppgift som har åtkomst till de virtuella VMware-datorerna. Du kan lägga till en autentiseringsuppgift för virtuella Windows-datorer och en autentiseringsuppgift för virtuella Linux-datorer. [Läs mer](./migrate-support-matrix-vmware.md) om de behörigheter som krävs.

> [!NOTE]
> Den här indatamängden är valfri och behövs för att aktivera program identifiering och visualisering av beroenden för agenter.

1. I **identifiera program och beroenden på virtuella datorer**klickar du på **Lägg till autentiseringsuppgifter**.
2. Välj **operativ system**.
3. Ange ett eget namn för autentiseringsuppgiften.
4. I **användar namn** och **lösen ord**anger du ett konto som har minst gäst åtkomst på de virtuella datorerna.
5. Klicka på **Lägg till**.

När du har angett vCenter Server och autentiseringsuppgifter för virtuella datorer (valfritt), klickar du på **Spara och starta identifiering** för att starta identifieringen av den lokala miljön.

Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i portalen. Identifiering av installerade program, roller och funktioner tar lite tid, tiden beror på antalet virtuella datorer som identifieras. För 500 virtuella datorer tar det cirka 1 timme för program inventeringen att visas i Azure Migrate portalen.

## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för [VMware-utvärdering](tutorial-assess-vmware.md) och [migrering utan agent](tutorial-migrate-vmware.md).
