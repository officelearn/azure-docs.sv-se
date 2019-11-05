---
title: Konfigurera en installation av Azure Migrate Server bedömning/migrering för virtuella VMware-datorer | Microsoft Docs
description: Beskriver hur du konfigurerar en installation för identifiering, utvärdering och migrering utan agent för virtuella VMware-datorer med hjälp av Azure Migrate Server bedömning/migrering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/10/2019
ms.author: raynew
ms.openlocfilehash: 77bf9a0f73519aa979da49614475daf70f582a9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467120"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurera en installation för virtuella VMware-datorer

I den här artikeln beskrivs hur du konfigurerar Azure Migrate-installationen om du utvärderar virtuella VMware-datorer med verktyget för att utvärdera Azure Migrate Server eller migrerar virtuella VMware-datorer till Azure med migrering utan agent med hjälp av verktyget Azure Migrate Migreringsverktyg.

VMware VM-installationen är en förenklad installation som används av Azure Migrate Server bedömning/migrering för att göra följande:

- Utforska lokala virtuella VMware-datorer.
- Skicka metadata och prestanda data för identifierade virtuella datorer till Azure Migrate Server bedömning/migrering.

[Läs mer](migrate-appliance.md) om Azure Migrate-enheten.


## <a name="appliance-deployment-steps"></a>Distributions steg för installationen

Så här konfigurerar du den apparat som du:
- Hämta en mall för en embryo-fil och importera den till vCenter Server.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-ova-template"></a>Ladda ned mallen för ägg

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **Identifiera datorer** > **Är dina datorer virtualiserade?** klickar du på **Ja, med VMware vSphere Hypervisor-programmet**.
3. Klicka på **Ladda ned** för att ladda ned .OVA-mallfilen.



### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash-värdet för de ägg:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. För version 1.0.0.5 ska den genererade hashen matcha de här inställningarna.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator.

1. I vSphere-klientkonsolen klickar du på **Arkiv** > **Distribuera OVF-mall**.
2. I guiden Distribuera OVF-mall > **källa**anger du platsen för ägg filen.
3. I **namn** och **plats**anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
5. I **värd/kluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
6. I **lagring**anger du lagrings målet för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverks mappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns dator kan ansluta till [Azure-URL: er](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
4. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
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
2. I **användar namn** och **lösen ord**anger du de skrivskyddade kontoautentiseringsuppgifter som installeras av enheten för att identifiera virtuella datorer på vCenter-servern. Kontrol lera att kontot har de [behörigheter som krävs för identifiering](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Du kan begränsa identifieringen genom att begränsa åtkomsten till vCenter-kontot enligt detta. Lär dig mer om omfattnings identifiering [här](tutorial-assess-vmware.md#scoping-discovery).
3. Klicka på **Verifiera anslutning** för att kontrol lera att installationen kan ansluta till vCenter Server.

### <a name="specify-vm-credentials"></a>Ange autentiseringsuppgifter för virtuell dator
För identifiering av program, roller och funktioner och visualisering av beroenden för de virtuella datorerna kan du ange en VM-autentiseringsuppgift som har åtkomst till de virtuella VMware-datorerna. Du kan lägga till en autentiseringsuppgift för virtuella Windows-datorer och en autentiseringsuppgift för virtuella Linux-datorer. [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) om de behörigheter som krävs.

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
