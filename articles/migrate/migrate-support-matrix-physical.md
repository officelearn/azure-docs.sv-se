---
title: Stöd för fysisk server bedömning/migrering med Azure Migrate
description: Sammanfattar stöd för fysisk server bedömning/migrering med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b5b5da6282b1df6c70fd58dcf8c417250de81b73
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196337"
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

**Support** | **Information**
--- | ---
**Azure-behörigheter** | Du måste ha deltagar-eller ägar behörigheter i prenumerationen för att kunna skapa ett Azure Migrate-projekt.
**Fysiska servrar** | Utvärdera upp till 35 000 fysiska servrar i ett enda projekt. Du kan ha flera projekt i en Azure-prenumeration. Ett projekt kan omfatta fysiska servrar, virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.
**Geografi** | Du kan skapa Azure Migrate projekt i ett antal geografiska områden. Även om du kan skapa projekt i specifika geografiska områden kan du utvärdera eller migrera datorer för andra mål platser. Projektets geografi används bara för att lagra identifierade metadata.

  **Geografi** | **Lagrings plats för metadata**
  --- | ---
  Azure Government | Virginia (USA-förvaltad region)
  Asien och stillahavsområdet | Asien, östra eller Sydostasien
  Australien | Östra Australien eller Australien, sydöstra
  Brasilien | Södra Brasilien
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

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Distribution av fysisk server**       | Den fysiska servern kan vara fristående eller distribuerad i ett kluster. |
| **Behörigheter**           | **Windows:** Konfigurera ett lokalt användar konto på alla Windows-servrar som du vill ska ingå i identifieringen. Användar kontot måste läggas till i dessa grupper – användare av fjärr skrivbord, prestanda övervaknings användare och användare av prestanda loggar. <br/> **Linux:** Du behöver ett rot konto på de Linux-servrar som du vill identifiera. |
| **Operativsystem** | Alla [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -operativsystem stöds förutom följande:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>Bedömnings krav

Vid utvärdering kör Azure Migrate en förenklad installation för att identifiera fysiska servrar och skicka Server-metadata och prestanda data till Azure Migrate. Installationen kan köras antingen på en fysisk server eller en virtuell dator och du ställer in den med ett PowerShell-skript som du hämtar från Azure Portal. I följande tabell sammanfattas kraven på installationen.

| **Support**                | **Information**               
| :-------------------       | :------------------- |
| **Distribution av utrustning**   |  Du distribuerar enheten antingen på en fysisk server eller en virtuell dator.<br/>  Värd datorn måste köra Windows Server 2012 R2 eller senare.<br/> Värden behöver tillräckligt med utrymme för att allokera 16 GB RAM-minne, 8 virtuella processorer, runt 80 GB lagrings utrymme och en extern växel för den virtuella datorns dator.<br/> Enheten behöver en statisk eller dynamisk IP-adress och Internet åtkomst.
| **Azure Migrate projekt**  |  En apparat kan associeras med ett enda projekt.<br/> Valfritt antal enheter kan associeras med ett enda projekt.<br/> Du kan utvärdera upp till 35 000 datorer i ett projekt.
| **Identifikation**              | En enda apparat kan identifiera upp till 250 servrar.
| **Utvärderings grupp**       | Du kan lägga till upp till 35 000 datorer i en enda grupp.
| **Utvärdering**             | Du kan utvärdera upp till 35 000 datorer i en enda utvärdering.


## <a name="assessment-appliance-url-access"></a>URL-åtkomst för utvärderings utrustning

För att utvärdera de virtuella datorerna behöver Azure Migrate-enheten Internet anslutning.

- När du distribuerar installationen kontrollerar Azure Migrate en anslutning till de URL: er som sammanfattas i tabellen nedan.
- Om du använder en URL-baserad proxy ger du åtkomst till URL: erna i tabellen, vilket säkerställer att proxyn matchar eventuella CNAME-poster som tas emot vid sökning av URL: erna.
- Om du har en avlyssnings-proxy kan du behöva importera Server certifikatet från proxyservern till enheten.


**URL** | **Information**  
--- | ---
*.portal.azure.com | Navigering till Azure Portal
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Logga in till din Azure-prenumeration
*.microsoftonline.com <br/> *.microsoftonline-p.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
management.azure.com | Att skapa Azure Active Directory program för att kunna underhålla kommunikationen.
dc.services.visualstudio.com | Loggning och övervakning
*.vault.azure.net | Hantera hemligheter i Azure Key Vault när du kommunicerar mellan installations programmet och tjänsten.
aka.ms/* | Tillåt åtkomst till aka-länkar.
https://download.microsoft.com/download/* | Tillåter hämtning från Microsoft Download-webbplatsen.



## <a name="assessment-port-requirements"></a>Utvärdering-port krav

I följande tabell sammanfattas port kraven för utvärdering.

**Anordningar** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/> Utgående anslutningar på portarna 443, 5671 och 5672 för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Fysiska servrar** | **Windows:** Inkommande anslutningar på portarna 443, 5989 för att hämta konfigurations-och prestanda-metadata från Windows-servrar. <br/> **Linux:**  Inkommande anslutningar på port 22 (UDP) för hämtning av konfigurations-och prestanda-metadata från Linux-servrar. |


## <a name="next-steps"></a>Nästa steg

[Förbered för fysisk server-utvärdering](tutorial-prepare-physical.md) för fysisk server-utvärdering och migrering.
