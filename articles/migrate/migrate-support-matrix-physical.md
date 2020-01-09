---
title: Stöd för fysisk server bedömning/migrering med Azure Migrate
description: Sammanfattar stöd för fysisk server bedömning/migrering med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 9e749297d831aeae7d785a9a9a29bea1f8c6d5e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454620"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>Stödmatris för utvärdering och migrering av fysisk server

Du kan använda [tjänsten Azure Migrate](migrate-overview.md) för att utvärdera och migrera datorer till Microsoft Azure molnet. Den här artikeln sammanfattar support inställningar och begränsningar för att utvärdera och migrera lokala fysiska servrar.



## <a name="physical-server-scenarios"></a>Scenarier för fysiska servrar

Tabellen sammanfattar scenarier som stöds för fysiska servrar.

**Distribution** | **Information***
--- | ---
**Utvärdera lokala fysiska servrar** | [Konfigurera](tutorial-prepare-physical.md) din första utvärdering.<br/><br/> [Kör](tutorial-assess-physical.md) en utvärdering.
**Migrera fysiska servrar till Azure** | [Prova](tutorial-migrate-physical-virtual-machines.md) migrering till Azure.


## <a name="azure-migrate-projects"></a>Azure Migrate projekt

**Support** | **Detaljer**
--- | ---
**Azure-behörigheter** | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
**Fysiska servrar** | Utvärdera upp till 35 000 fysiska servrar i ett enda projekt. Du kan ha flera projekt i en Azure-prenumeration. Ett projekt kan omfatta fysiska servrar, virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.
**Geografi** | Du kan skapa Azure Migrate projekt i ett antal geografiska områden. Även om du kan skapa projekt i specifika geografiska områden kan du utvärdera eller migrera datorer för andra mål platser. Projektets geografi används bara för att lagra identifierade metadata.

  **Geografi** | **Lagrings plats för metadata**
  --- | ---
  Azure Government | USA Gov Virginia
  Asien och stillahavsområdet | Asien, östra eller Sydostasien
  Australien | Östra Australien eller Australien, sydöstra
  Brasilien | Brasilien, södra
  Kanada | Kanada, centrala eller Kanada, öst
  Europa | Europa, norra eller Europa, västra
  Frankrike | Frankrike, centrala
  Indien | Centrala Indien eller södra Indien
  Japan |  Japan, östra eller Japan, väst
  Korea | Korea, centrala eller Korea, södra
  Storbritannien | Storbritannien, södra eller Storbritannien, västra
  USA | USA, centrala eller västra USA 2


 > [!NOTE]
 > Stöd för Azure Government är för närvarande bara tillgängligt för den [äldre versionen](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) av Azure Migrate.


## <a name="assessment-physical-server-requirements"></a>Bedömning-krav för fysisk server

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distribution av fysisk server**       | Den fysiska servern kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | **Windows:** Konfigurera ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Användar kontot måste läggas till i dessa grupper – användare av fjärr skrivbord, prestanda övervaknings användare och användare av prestanda loggar. <br/> **Linux:** Du behöver ett rot konto på de Linux-servrar som du vill identifiera. |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem stöds förutom följande:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>Bedömnings krav

Vid utvärdering kör Azure Migrate en förenklad installation för att identifiera fysiska servrar och skicka Server-metadata och prestanda data till Azure Migrate. Installationen kan köras antingen på en fysisk server eller en virtuell dator och du ställer in den med ett PowerShell-skript som du hämtar från Azure Portal. I följande tabell sammanfattas kraven på installationen.

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distribution av utrustning**   |  Installations skriptet för installationen kan laddas ned från portalen (i en zippad mapp). <br/> Du kan zippa upp mappen och köra PowerShell-skriptet (AzureMigrateInstaller. ps1) antingen på en dedikerad fysisk server eller en virtuell dator för att konfigurera installationen.<br/>  Datorn som valts för att installera installationen måste köra Windows Server 2016.<br/> Datorn behöver tillräckligt med utrymme för att allokera 16 GB RAM-minne, 8 virtuella processorer, runt 80 GB lagrings utrymme och en extern växel för den virtuella datorns dator.<br/> Enheten behöver en statisk eller dynamisk IP-adress och Internet åtkomst.
| **Azure Migrate projekt**  |  En apparat kan associeras med ett enda projekt.<br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> Du kan utvärdera upp till 35 000 datorer i ett projekt.
| **Identifiering**              | En enda apparat kan identifiera upp till 250 servrar.
| **Utvärderings grupp**       | Du kan lägga till upp till 35 000 datorer i en enda grupp.
| **Utvärdering**             | Du kan utvärdera upp till 35 000 datorer i en enda utvärdering.


## <a name="assessment-appliance-url-access"></a>URL-åtkomst för utvärderings utrustning

För att utvärdera de virtuella datorerna behöver Azure Migrate-enheten Internet anslutning.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy ger du åtkomst till URL: erna i tabellen, vilket säkerställer att proxyn matchar eventuella CNAME-poster som tas emot vid sökning av URL: erna.
- Om du har en avlyssnings-proxy kan du behöva importera Server certifikatet från proxyservern till enheten.


**URL** | **Detaljer**  
--- | ---
*.portal.azure.com | Navigering till Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com  | Logga in till din Azure-prenumeration
*.microsoftonline.com <br/> *.microsoftonline-p.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
management.azure.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
dc.services.visualstudio.com | Loggning och övervakning
*.vault.azure.net | Hantera hemligheter i Azure Key Vault när du kommunicerar mellan installations programmet och tjänsten.
aka.ms/* | Tillåt åtkomst till aka-länkar.
https://download.microsoft.com/download/* | Tillåter hämtning från Microsoft Download-webbplatsen.



## <a name="assessment-port-requirements"></a>Utvärdering-port krav

I följande tabell sammanfattas port kraven för utvärdering.

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/> Utgående anslutningar på portarna 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Fysiska servrar** | **Windows:** Inkommande anslutningar på port 443, WinRM-portar 5985 (HTTP) och 5986 (HTTPS) för att hämta konfigurations-och prestanda metadata från Windows-servrar. <br/> **Linux:**  Inkommande anslutningar på port 22 (UDP) för hämtning av konfigurations-och prestanda-metadata från Linux-servrar. |


## <a name="next-steps"></a>Nästa steg

[Förbered för fysisk server-utvärdering](tutorial-prepare-physical.md) för fysisk server-utvärdering och migrering.
