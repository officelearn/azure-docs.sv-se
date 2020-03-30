---
title: Azure VMware-lösning från CloudSimple - Private Cloud-behörighetsmodell
description: Beskriver cloudsimple private cloud-behörighetsmodellen, grupperna och kategorierna
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014954"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple Private Cloud-behörighetsmodell för VMware vCenter

CloudSimple behåller fullständig administrativ åtkomst till den privata molnmiljön. Varje CloudSimple-kund beviljas tillräckliga administrativa privilegier för att kunna distribuera och hantera de virtuella datorerna i sin miljö.  Om det behövs kan du tillfälligt eskalera dina privilegier för att utföra administrativa funktioner.

## <a name="cloud-owner"></a>Molnägare

När du skapar ett privat moln skapas en **CloudOwner-användare** i domänen vCenter Single Sign-On, med åtkomst till **molnet-ägarroll** för att hantera objekt i det privata molnet. Den här användaren kan också ställa in ytterligare [vCenter-identitetskällor](set-vcenter-identity.md)och andra användare till Private Cloud vCenter.

> [!NOTE]
> Standardanvändare för ditt CloudSimple Private cloudowner@cloudsimple.local Cloud vCenter är när ett privat moln skapas.

## <a name="user-groups"></a>Användargrupper

En grupp som kallas **Cloud-Owner-Group** skapas under distributionen av ett privat moln. Användare i den här gruppen kan administrera olika delar av vSphere-miljön i det privata molnet. Den här gruppen får automatiskt **behörighet för cloud-owner-roll** och **CloudOwner-användaren** läggs till som medlem i den här gruppen.  CloudSimple skapar ytterligare grupper med begränsade privilegier för enkel hantering.  Du kan lägga till alla användare i dessa förskapade grupper och de privilegier som definieras nedan tilldelas automatiskt till användarna i grupperna.

### <a name="pre-created-groups"></a>Förskapade grupper

| Gruppnamn | Syfte | Roll |
| -------- | ------- | ------ |
| Moln-Ägare-Grupp | Medlemmar i den här gruppen har administratörsbehörighet till Private Cloud vCenter | [Moln-Ägare-Roll](#cloud-owner-role) |
| Moln-Global-Cluster-Admin-Group | Medlemmar i den här gruppen har administratörsbehörighet i Private Cloud vCenter Cluster | [Moln-kluster-admin-roll](#cloud-cluster-admin-role) |
| Moln-Global-Lagring-Admin-Grupp | Medlemmar i den här gruppen kan hantera lagring på Private Cloud vCenter | [Moln-lagring-admin-roll](#cloud-storage-admin-role) |
| Moln-Globalt nätverk-admin-grupp | Medlemmar i den här gruppen kan hantera nätverks- och distribuerade portgrupper på Private Cloud vCenter | [Moln-nätverk-admin-roll](#cloud-network-admin-role) |
| Moln-Global-VM-admin-grupp | Medlemmar i den här gruppen kan hantera virtuella datorer på Private Cloud vCenter | [Moln-VM-administratör-roll](#cloud-vm-admin-role) |

Om du vill ge enskilda användare behörighet att hantera det privata molnet skapar du användarkonton som lägger till i lämpliga grupper.

> [!CAUTION]
> Nya användare får endast läggas till *i Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* eller *Cloud-Global-VM-Admin-Group*.  Användare som läggs till i gruppen *Administratörer* tas bort automatiskt.  Endast tjänstkonton får läggas till *i grupp-* och tjänstkonton för administratörer får inte användas för att logga in på vSphere-webbgränssnittet.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Lista över vCenter-privilegier för standardroller

### <a name="cloud-owner-role"></a>Moln-Ägare-Roll

| **Kategori** | **Privilegium** |
|----------|-----------|
| **Larm** | Bekräfta larm <br> Skapa larm <br> Inaktivera larmåtgärd <br> Ändra larm <br> Ta bort larm <br> Ange larmstatus |
| **Behörigheter** | Ändra behörighet |
| **Innehållsbibliotek** | Lägga till biblioteksobjekt <br> Skapa lokalt bibliotek <br> Skapa bibliotek som prenumererar <br> Ta bort biblioteksobjekt <br> Ta bort lokalt bibliotek <br> Ta bort bibliotek som prenumererar <br> Hämta filer <br> Vräka biblioteksobjekt <br> Vräka prenumerera bibliotek <br> Importera lagring <br> Avsökningsprenumerationsinformation <br> Läs lagring <br> Synkronisera biblioteksobjekt <br> Synkronisera bibliotek som prenumererar <br> Skriv introspektion <br> Uppdatera konfigurationsinställningar <br> Uppdatera filer <br> Uppdatera bibliotek <br> Uppdatera biblioteksobjekt <br> Uppdatera lokalt bibliotek <br> Uppdatera biblioteket som prenumererar <br> Visa konfigurationsinställningar |
| **Kryptografiska operationer** | Lägg till disk <br> Klona <br> Avkryptera <br> Direktåtkomst <br> Kryptera <br> Kryptera nya <br> Hantera KMS <br> Hantera krypteringsprinciper <br> Hantera nycklar <br> Migrera <br> Omkryptera <br> Registrera vm <br> Registrera värd |
| **dvPort-gruppen** | Skapa <br> Ta bort <br> Ändra <br> Åtgärd för princip <br> Åtgärden Omfattning |
| **Datalager** | Allokera utrymme <br> Bläddra i datalager <br> Konfigurera datalager <br> Filåtgärder på låg nivå <br> Flytta datalager <br> Ta bort datalager <br> Ta bort fil <br> Byt namn på datalager <br> Uppdatera filer för virtuella datorer <br> Uppdatera metadata för virtuella datorer |
| **ESX Agent Manager** | Konfigurera <br> Ändra <br> Visa |
| **Anknytning** | Registrera tillägg <br> Avregistrera tillägg <br> Uppdateringstillägg |
| **Leverantör av extern statistik**| Registrera dig <br> Avregistrera <br> Uppdatering |
| **Mapp** | Skapa mapp <br> Ta bort mapp <br> Flytta mapp <br> Byta namn på mappen |
| **Global** | Avbryt uppgift <br> Kapacitetsplanering <br> Diagnostik <br> Inaktivera metoder <br> Aktivera metoder <br> Global tagg <br> Hälsa <br> Licenser <br> Logga händelse <br> Hantera anpassade attribut <br> Proxy <br> Skriptåtgärd <br> Servicechefer <br> Ange anpassat attribut <br> Systemtagg |
| **Leverantör av hälsouppdatering** | Registrera dig <br> Avregistrera <br> Uppdatering |
| **Konfiguration för > värd** | Konfiguration av lagringspartition |
| **Värd > lager** | Ändra kluster |
| **vSphere-taggning** | Tilldela eller ta bort vSphere-tagg <br> Skapa vSphere-tagg <br> Skapa vSphere-taggkategori <br> Ta bort vSphere-tagg <br> Ta bort vSphere-taggkategori <br> Redigera vSphere-tagg <br> Redigera vSphere-taggkategori <br> Ändra usedby-fält för kategori <br> Ändra UsedBy-fält för tagg |
| **Nätverk** | Tilldela nätverk <br> Konfigurera <br> Flytta nätverk <br> Ta bort |
| **Prestanda** | Ändra intervall |
| **Värdprofil** | Visa |
| **Resurs** | Tillämpa rekommendation <br> Tilldela vApp till resurspool <br> Tilldela virtuell dator till resurspoolen <br> Skapa resurspool <br> Migrera avstängd virtuell dator <br> Migrera påslagen på virtuell dator <br> Ändra resurspool <br> Flytta resurspool <br> Fråga vMotion <br> Ta bort resurspool <br> Byt namn på resurspool |
| **Schemalagd aktivitet** | Skapa uppgifter <br> Ändra uppgift <br> Ta bort uppgift <br> Kör aktivitet |
| **Sessioner** | Personifiera användare <br> Meddelande <br> Validera session <br> Visa och stoppa sessioner |
| **Datalagerkluster** | Konfigurera ett datalagerkluster |
| **Profildriven lagring** | Profildriven lagringsuppdatering <br> Profildriven lagringsvy |
| **Lagringsvyer** | Konfigurera tjänsten <br> Visa |
| **Aktiviteter** | Skapa uppgift <br> Uppdatera uppgift |
| **Överför tjänst**| Hantera <br> Övervaka |
| **vApp (på nytt)** | Lägg till virtuell dator <br> Tilldela resurspool <br> Tilldela vApp <br> Klona <br> Skapa <br> Ta bort <br> Exportera <br> Importera <br> Flytta <br> Avstängning <br> Slå på <br> Byt namn <br> Suspend <br> Avregistrera <br> Visa OVF-miljö <br> konfiguration av vApp-program <br> konfiguration av vApp-instans <br> vApp hanteradAv konfiguration <br> vApp-resurskonfiguration |
| **VRMPolicy** | Fråga VRMPolicy <br> Uppdatera VRMPolicy |
| **Konfiguration för > virtuell dator** | Lägga till befintlig disk <br> Lägg till ny disk <br> Lägga till eller ta bort enhet <br> Avancerat <br> Ändra cpu-antal <br> Ändra resurs <br> Konfigurera managedBy <br> Spårning av diskändringar <br> Tomt på disk <br> Visa anslutningsinställningar <br> Utöka den virtuella disken <br> Värd-USB-enhet <br> Minne <br> Ändra enhetsinställningar <br> Kompatibilitet med frågefeltolerans <br> Fråga oägda filer <br> Rå enhet <br> Ladda om från sökvägen <br> Ta bort disk <br> Byt namn <br> Återställa gästinformation <br> Ange anteckning <br> Inställningar <br> Placering av växlingsfil <br> Växla fördel överordnad <br> Lås upp virtuell dator <br> Uppgradera kompatibiliteten för virtuella datorer |
| **Virtuella > gästverksamhet** | Ändring av gäståtgärdsalias <br> Aliasfråga för gäståtgärd <br> Ändringar av gästdrift <br> Körning av gäståtgärdsprogram <br> Frågor om gäståtgärd |
| **Interaktion med > virtuell dator** | Svara på fråga <br> Säkerhetskopiering på virtuell dator <br> Konfigurera CD-media <br> Konfigurera diskettmedia <br> Interaktion mellan konsoler <br> Skapa skärmdump <br> Defragmentera alla diskar <br> Enhetsanslutning <br> Dra och släpp <br> Hantering av gästoperativsystem av VIX API <br> Injicera USB HID-skanningskoder <br> Pausa eller pausa <br> Utföra rensnings- eller krympningsåtgärder <br> Avstängning <br> Slå på <br> Spela in session på virtuell dator <br> Spela upp session på virtuell dator <br> Återställ <br> Återuppta feltolerans <br> Suspend <br> Pausa feltolerans <br> Redundanstest <br> Testa omstart sekundär virtuell dator <br> Inaktivera feltolerans <br> Aktivera feltolerans <br> Installera VMware Tools |
| **Virtuell dator > lager** | Skapa från befintliga <br> Skapa ny <br> Flytta <br> Registrera dig <br> Ta bort <br> Avregistrera |
| **Etablering av virtuella datorer >** | Tillåt diskåtkomst <br> Tillåt filåtkomst <br> Tillåt skrivskyddad diskåtkomst <br> Tillåt hämtning av virtuella datorer <br> Tillåt överföring av filer för virtuella datorer <br> Mall för klon <br> Klona virtuell dator <br> Skapa mall från virtuell dator <br> Anpassa <br> Distribuera mallen <br> Markera som mall <br> Markera som virtuell dator <br> Ändra anpassningsspecifikation <br> Befordra diskar <br> Läs anpassningsspecifikationer |
| **Konfiguration av > tjänst för virtuell dator** | Tillåt meddelanden <br> Tillåt avsökning av globala händelsemeddelanden <br> Hantera tjänstkonfigurationer <br> Ändra tjänstkonfiguration <br> Konfigurationer av frågetjänst <br> Läs tjänstkonfiguration |
| **Hantering av virtuella datorer > ögonblicksbild** | Skapa en ögonblicksbild <br> Ta bort ögonblicksbild <br> Byt namn på ögonblicksbild <br> Återgå till ögonblicksbild |
| **Virtuell dator > vSphere Replication** | Konfigurera replikering <br> Hantera replikering <br> Övervaka replikering |
| **vService (VService)** | Skapa beroende <br> Förstör beroende <br> Konfigurera om beroendekonfiguration <br> Uppdatera beroende |

### <a name="cloud-cluster-admin-role"></a>Moln-kluster-admin-roll

| **Kategori** | **Privilegium** |
|----------|-----------|
| **Datalager** | Allokera utrymme <br> Bläddra i datalager <br> Konfigurera datalager <br> Filåtgärder på låg nivå <br> Ta bort datalager <br> Byt namn på datalager <br> Uppdatera filer för virtuella datorer <br> Uppdatera metadata för virtuella datorer |
| **Mapp** | Skapa mapp <br> Ta bort mapp <br> Flytta mapp <br> Byta namn på mappen |
| **Konfiguration för > värd**  | Konfiguration av lagringspartition |
| **vSphere-taggning** | Tilldela eller ta bort vSphere-tagg <br> Skapa vSphere-tagg <br> Skapa vSphere-taggkategori <br> Ta bort vSphere-tagg <br> Ta bort vSphere-taggkategori <br> Redigera vSphere-tagg <br> Redigera vSphere-taggkategori <br> Ändra usedby-fält för kategori <br> Ändra UsedBy-fält för tagg |
| **Nätverk** | Tilldela nätverk |
| **Resurs** | Tillämpa rekommendation <br> Tilldela vApp till resurspool <br> Tilldela virtuell dator till resurspoolen <br> Skapa resurspool <br> Migrera avstängd virtuell dator <br> Migrera påslagen på virtuell dator <br> Ändra resurspool <br> Flytta resurspool <br> Fråga vMotion <br> Ta bort resurspool <br> Byt namn på resurspool |
| **vApp (på nytt)** | Lägg till virtuell dator <br> Tilldela resurspool <br> Tilldela vApp <br> Klona <br> Skapa <br> Ta bort <br> Exportera <br> Importera <br> Flytta <br> Avstängning <br> Slå på <br> Byt namn <br> Suspend <br> Avregistrera <br> Visa OVF-miljö <br> konfiguration av vApp-program <br> konfiguration av vApp-instans <br> vApp hanteradAv konfiguration <br> vApp-resurskonfiguration |
| **VRMPolicy** | Fråga VRMPolicy <br> Uppdatera VRMPolicy |
| **Konfiguration för > virtuell dator** | Lägga till befintlig disk <br> Lägg till ny disk <br> Lägga till eller ta bort enhet <br> Avancerat <br> Ändra cpu-antal <br> Ändra resurs <br> Konfigurera managedBy <br> Spårning av diskändringar <br> Tomt på disk <br> Visa anslutningsinställningar <br> Utöka den virtuella disken <br> Värd-USB-enhet <br> Minne <br> Ändra enhetsinställningar <br> Kompatibilitet med frågefeltolerans <br> Fråga oägda filer <br> Rå enhet <br> Ladda om från sökvägen <br> Ta bort disk <br> Byt namn <br> Återställa gästinformation <br> Ange anteckning <br> Inställningar <br> Placering av växlingsfil <br> Växla fördel överordnad <br> Lås upp virtuell dator <br> Uppgradera kompatibiliteten för virtuella datorer |
| **Virtuella > gästverksamhet** | Ändring av gäståtgärdsalias <br> Aliasfråga för gäståtgärd <br> Ändringar av gästdrift <br> Körning av gäståtgärdsprogram <br> Frågor om gäståtgärd |
| **Interaktion med > virtuell dator** | Svara på fråga <br> Säkerhetskopiering på virtuell dator <br> Konfigurera CD-media <br> Konfigurera diskettmedia <br> Interaktion mellan konsoler <br> Skapa skärmdump <br> Defragmentera alla diskar <br> Enhetsanslutning <br> Dra och släpp <br> Hantering av gästoperativsystem av VIX API <br> Injicera USB HID-skanningskoder <br> Pausa eller pausa <br> Utföra rensnings- eller krympningsåtgärder <br> Avstängning <br> Slå på <br> Spela in session på virtuell dator <br> Spela upp session på virtuell dator <br> Återställ <br> Återuppta feltolerans <br> Suspend <br> Pausa feltolerans <br> Redundanstest <br> Testa omstart sekundär virtuell dator <br> Inaktivera feltolerans <br> Aktivera feltolerans <br> Installera VMware Tools
| **Virtuell dator > lager** | Skapa från befintliga <br> Skapa ny <br> Flytta <br> Registrera dig <br> Ta bort <br> Avregistrera |
| **Etablering av virtuella datorer >** | Tillåt diskåtkomst <br> Tillåt filåtkomst <br> Tillåt skrivskyddad diskåtkomst <br> Tillåt hämtning av virtuella datorer <br> Tillåt överföring av filer för virtuella datorer <br> Mall för klon <br> Klona virtuell dator <br> Skapa mall från virtuell dator <br> Anpassa <br> Distribuera mallen <br> Markera som mall <br> Markera som virtuell dator <br> Ändra anpassningsspecifikation <br> Befordra diskar  <br> Läs anpassningsspecifikationer |
| **Konfiguration av > tjänst för virtuell dator** | Tillåt meddelanden <br> Tillåt avsökning av globala händelsemeddelanden <br> Hantera tjänstkonfigurationer <br> Ändra tjänstkonfiguration <br> Konfigurationer av frågetjänst <br> Läs tjänstkonfiguration
| **Hantering av virtuella datorer > ögonblicksbild** | Skapa en ögonblicksbild <br> Ta bort ögonblicksbild <br> Byt namn på ögonblicksbild <br> Återgå till ögonblicksbild |
| **Virtuell dator > vSphere Replication** | Konfigurera replikering <br> Hantera replikering <br> Övervaka replikering |
| **vService (VService)** | Skapa beroende <br> Förstör beroende <br> Konfigurera om beroendekonfiguration <br> Uppdatera beroende |

### <a name="cloud-storage-admin-role"></a>Moln-lagring-admin-roll

| **Kategori** | **Privilegium** |
|----------|-----------|
| **Datalager** | Allokera utrymme <br> Bläddra i datalager <br> Konfigurera datalager <br> Filåtgärder på låg nivå <br> Ta bort datalager <br> Byt namn på datalager <br> Uppdatera filer för virtuella datorer <br> Uppdatera metadata för virtuella datorer |
| **Konfiguration för > värd** | Konfiguration av lagringspartition |
| **Datalagerkluster** | Konfigurera ett datalagerkluster |
| **Profildriven lagring** | Profildriven lagringsuppdatering <br> Profildriven lagringsvy |
| **Lagringsvyer** | Konfigurera tjänsten <br> Visa |

### <a name="cloud-network-admin-role"></a>Moln-nätverk-admin-roll

| **Kategori** | **Privilegium** |
|----------|-----------|
| **dvPort-gruppen** | Skapa <br> Ta bort <br> Ändra <br> Åtgärd för princip <br> Åtgärden Omfattning |
| **Nätverk** | Tilldela nätverk <br> Konfigurera <br> Flytta nätverk <br> Ta bort |
| **Konfiguration för > virtuell dator** | Ändra enhetsinställningar |

### <a name="cloud-vm-admin-role"></a>Moln-VM-administratör-roll

| **Kategori** | **Privilegium** |
|----------|-----------|
| **Datalager** | Allokera utrymme <br> Bläddra i datalager |
| **Nätverk** | Tilldela nätverk |
| **Resurs** | Tilldela virtuell dator till resurspoolen <br> Migrera avstängd virtuell dator <br> Migrera påslagen på virtuell dator
| **vApp (på nytt)** | Exportera <br> Importera |
| **Konfiguration för > virtuell dator** | Lägga till befintlig disk <br> Lägg till ny disk <br> Lägga till eller ta bort enhet <br> Avancerat <br> Ändra cpu-antal <br> Ändra resurs <br> Konfigurera managedBy <br> Spårning av diskändringar <br> Tomt på disk <br> Visa anslutningsinställningar <br> Utöka den virtuella disken <br> Värd-USB-enhet <br> Minne <br> Ändra enhetsinställningar <br> Kompatibilitet med frågefeltolerans <br> Fråga oägda filer <br> Rå enhet <br> Ladda om från sökvägen <br> Ta bort disk <br> Byt namn <br> Återställa gästinformation <br> Ange anteckning <br> Inställningar <br> Placering av växlingsfil <br> Växla fördel överordnad <br> Lås upp virtuell dator <br> Uppgradera kompatibiliteten för virtuella datorer |
| **Virtuella >gästverksamhet** | Ändring av gäståtgärdsalias <br> Aliasfråga för gäståtgärd <br> Ändringar av gästdrift <br> Körning av gäståtgärdsprogram <br> Frågor om gäståtgärd    |
| **Interaktion med >virtuell dator** | Svara på fråga <br> Säkerhetskopiering på virtuell dator <br> Konfigurera CD-media <br> Konfigurera diskettmedia <br> Interaktion mellan konsoler <br> Skapa skärmdump <br> Defragmentera alla diskar <br> Enhetsanslutning <br> Dra och släpp <br> Hantering av gästoperativsystem av VIX API <br> Injicera USB HID-skanningskoder <br> Pausa eller pausa <br> Utföra rensnings- eller krympningsåtgärder <br> Avstängning <br> Slå på <br> Spela in session på virtuell dator <br> Spela upp session på virtuell dator <br> Återställ <br> Återuppta feltolerans <br> Suspend <br> Pausa feltolerans <br> Redundanstest <br> Testa omstart sekundär virtuell dator <br> Inaktivera feltolerans <br> Aktivera feltolerans <br> Installera VMware Tools |
| **Virtuell dator >lager** | Skapa från befintliga <br> Skapa ny <br> Flytta <br> Registrera dig <br> Ta bort <br> Avregistrera |
| **Etablering av virtuella datorer >** | Tillåt diskåtkomst <br> Tillåt filåtkomst <br> Tillåt skrivskyddad diskåtkomst <br> Tillåt hämtning av virtuella datorer <br> Tillåt överföring av filer för virtuella datorer <br> Mall för klon <br> Klona virtuell dator <br> Skapa mall från virtuell dator <br> Anpassa <br> Distribuera mallen <br> Markera som mall <br> Markera som virtuell dator <br> Ändra anpassningsspecifikation <br> Befordra diskar <br> Läs anpassningsspecifikationer |
| **Konfiguration av >tjänst för virtuell dator** | Tillåt meddelanden <br> Tillåt avsökning av globala händelsemeddelanden <br> Hantera tjänstkonfigurationer <br> Ändra tjänstkonfiguration <br> Konfigurationer av frågetjänst <br> Läs tjänstkonfiguration
| **Hantering av virtuella datorer >ögonblicksbild** | Skapa en ögonblicksbild <br> Ta bort ögonblicksbild <br> Byt namn på ögonblicksbild <br> Återgå till ögonblicksbild |
| **Virtuell dator >vSphere Replication** | Konfigurera replikering <br> Hantera replikering <br> Övervaka replikering |
| **vService (VService)** | Skapa beroende <br> Förstör beroende <br> Konfigurera om beroendekonfiguration <br> Uppdatera beroende |
