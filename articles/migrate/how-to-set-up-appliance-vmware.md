---
title: Konfigurera en apparat för Azure Migrate utvärdering/servermigrering för virtuella VMware-datorer | Microsoft Docs
description: Beskriver hur du ställer in en installation för identifiering, bedömning och migrering av virtuella VMware-datorer med hjälp av Azure Migrate utvärdering/servermigrering utan agenter.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811731"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurera en installation för virtuella VMware-datorer

Den här artikeln beskriver hur du ställer in Azure Migrate-installation om du utvärdera virtuella VMware-datorer med verktyget Azure Migrate Server-utvärdering, eller migrerar virtuella VMware-datorer till Azure med migrering utan agenter med hjälp av verktyget Azure Migrate servermigrering.

VMware VM-installationen är en enkel installation som används av Azure Migrate utvärdering/servermigrering för att göra följande:

- Upptäck lokala virtuella VMware-datorer.
- Skicka metadata och prestanda för identifierade virtuella datorer till Azure Migrate utvärdering/servermigrering.

[Läs mer](migrate-appliance.md) om Azure Migrate-installationen.


## <a name="appliance-deployment-steps"></a>Distributionsstegen för installation

Du ställer in enheten som du:
- Ladda ned en mall för OVA-filen och importera dem till vCenter-servern.
- Skapa och kontrollera att den kan ansluta till Azure Migrate Server-utvärdering. 
- Konfigurera programmet för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-ova-template"></a>Hämta OVA-mall

1. I **migrering mål** > **servrar** > **Azure Migrate: Server-utvärdering**, klickar du på **identifiera**.
2. I **identifiera datorer** > **är dina datorer virtualiserade?** , klickar du på **Ja, med VMWare vSphere hypervisor**.
3. Klicka på **hämta** att ladda ned den. OVA mallfilen.



### <a name="verify-security"></a>Kontrollera säkerheten

Kontrollera att OVA-filen är säker, innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando, för att generera en hash för ova-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha nedanstående inställningar för installation version 1.0.0.5. 

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Skapa virtuell dator

Importera den nedladdade filen och skapa en virtuell dator.

1. I vSphere-klientkonsolen klickar du på **Arkiv** > **Distribuera OVF-mall**.
2. I guiden Distribuera OVF-mall > **källa**, ange platsen för OVA-filen.
3. I **namn** och **plats**, ange ett eget namn för den virtuella datorn. Välj objektet lager där den virtuella datorn kommer att finnas.
5. I **värden/klustret**, anger du värden eller kluster som den virtuella datorn körs.
6. I **Storage**, ange lagringsplats för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverksmappning**, ange det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha en Internetanslutning för att kunna skicka metadata till Azure Migrate Server-utvärdering.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera installationen åtkomst till Azure

Kontrollera att installationen VM kan ansluta till [Azure URL: er](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurera programmet

Ställ in installationen för första gången.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösenord för installationen.
3. Öppna en webbläsare på en dator som kan ansluta till den virtuella datorn och öppna URL: en för webbappen installation: **https://*installationsnamnet eller IP-adress*: 44368**.

   Du kan också öppna appen från skrivbordet installation genom att klicka på genvägen till appen.
4. I webbapp > **konfigurera förhandskraven**, gör du följande:
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutningen**: Appen kontrollerar att den virtuella datorn har Internetåtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**, och anger proxyadress och lyssningsport, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tid synkronisering**: Tid har verifierats. Tiden på enheten som ska vara synkroniserad med internettiden för identifiering ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate kontrollerar att de senaste uppdateringarna för installationen är installerade.
    - **Installera VDDK**: Azure Migrate kontrollerar att VMWare vSphere virtuell Disk Development Kit (VDDK) är installerad.
        - Azure Migrates använder VDDK för att replikera virtuella datorer under migreringen till Azure.
        - Hämta VDDK 6.7 från VMware och extrahera hämtade zip-innehållet till den angivna platsen i installationen.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **logga In**. Om den inte visas kan du kontrollera att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På den nya fliken kan du logga in med dina autentiseringsuppgifter för Azure. 
    - Logga in med ditt användarnamn och lösenord.
    - Logga in med en PIN-kod stöds inte.
3. När du har loggat in kan gå tillbaka till webbappen.
2. Välj den prenumeration som Azure Migrate-projektet har skapats. Välj projektet.
3. Ange ett namn för produkten. Namnet bör vara alfanumeriskt med 14 tecken eller mindre.
4. Klicka på **registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifieringen

Nu kan ansluta från installationen till vCenter-servern och starta identifieringen av virtuella datorer. 

1. I **ange vCenter-serverinformationen**, ange namn (FQDN) eller IP-adress för vCenter-servern. Du kan lämna standardporten eller ange en anpassad port som vCenter-servern lyssnar.
2. I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som installationen använder för att identifiera virtuella datorer på vCenter-servern. Kontrollera att tjänstkontot har de [behörigheter som krävs för identifiering av](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Klicka på **Validera anslutning** att se till att installationen kan ansluta till vCenter-servern.
4. När anslutningen har upprättats klickar du på **spara och starta identifieringen**.


Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer att visas på portalen. 


## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för [VMware utvärdering](tutorial-assess-vmware.md) och [migrering utan agenter](tutorial-migrate-vmware.md).
