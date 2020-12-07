---
title: Programarkitektur för Azure Migrate
description: Innehåller en översikt över Azure Migrate-installationen som används i Server utvärdering och migrering.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 42d4a722be25eec4b3e27012350346018fdba0f3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754121"
---
# <a name="azure-migrate-appliance-architecture"></a>Programarkitektur för Azure Migrate

I den här artikeln beskrivs arkitekturen och processerna för Azure Migrate utrustning. Azure Migrate-installationen är en förenklad enhet som distribueras lokalt, för att identifiera virtuella datorer och fysiska servrar för migrering till Azure. 

## <a name="deployment-scenarios"></a>Distributionsscenarier

Azure Migrates apparaten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för** 
--- | --- | ---
**Virtuell VMware VM-utvärdering** | Azure Migrate: Server utvärdering | Identifiera virtuella VMware-datorer.<br/><br/> Identifiera maskin program och beroenden.<br/><br/> Samla in metadata för datorns metadata och prestanda och skicka till Azure.
**VMware VM-migrering (utan agent)** | Azure Migrate: Server-migrering | Identifiera virtuella VMware-datorer<br/><br/>  Replikera virtuella VMware-datorer med [migrering utan agent](server-migrate-overview.md).
**Utvärdering av Hyper-V VM** | Azure Migrate: Server utvärdering | Identifiera virtuella Hyper-V-datorer.<br/><br/> Samla in metadata för datorns metadata och prestanda och skicka till Azure.
**Fysisk dator** |  Azure Migrate: Server utvärdering |  Identifiera fysiska servrar.<br/><br/> Samla in metadata för datorns metadata och prestanda och skicka till Azure.

## <a name="appliance-components"></a>Utrustnings komponenter

Enheten har ett antal komponenter.

- **Hanterings app**: det här är en webbapp för användarindata under installationen av produkten. Används vid utvärdering av datorer för migrering till Azure.
- **Identifierings agent**: agenten samlar in dator konfigurations data. Används vid utvärdering av datorer för migrering till Azure. 
- **Insamlings agent**: agenten samlar in prestanda data. Används vid utvärdering av datorer för migrering till Azure.
- **Dra-agent**: dirigerar VM-replikering och samordnar kommunikationen mellan replikerade datorer och Azure. Används endast när du replikerar virtuella VMware-datorer till Azure med hjälp av en agent lös migrering.
- **Gateway**: skickar replikerade data till Azure. Används endast när du replikerar virtuella VMware-datorer till Azure med hjälp av en agent lös migrering.
- **Tjänsten automatisk uppdatering**: uppdaterar komponenter för komponenter (körs var 24: e timme).



## <a name="appliance-deployment"></a>Distribution av utrustning

- Azure Migrate-installationen kan konfigureras med hjälp av en mall för [Hyper-V](how-to-set-up-appliance-hyper-v.md) eller [VMware](how-to-set-up-appliance-vmware.md) eller med ett PowerShell-skript installations program för [VMware/Hyper-V](deploy-appliance-script.md)och för [fysiska servrar](how-to-set-up-appliance-physical.md). 
- Krav för installation av support och distributions krav sammanfattas i [support matrisen för enheten](migrate-appliance.md).


## <a name="appliance-registration"></a>Apparat registrering

Under installationen av installationen registrerar du enheten med Azure Migrate och de åtgärder som sammanfattas i tabellen sker.

**Åtgärd** | **Information** | **Behörigheter**
--- | --- | ---
**Registrera käll leverantörer** | Dessa resurs leverantörer registreras i den prenumeration du väljer under installationen av installationen: Microsoft. OffAzure, Microsoft. Migrate och Microsoft. nyckel valv.<br/><br/> När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. | Om du vill registrera resurs leverantörer behöver du en deltagar-eller ägar roll för prenumerationen.
**Skapa Azure AD-App – kommunikation** | Azure Migrate skapar en Azure Active Directory (Azure AD)-app för kommunikation (autentisering och auktorisering) mellan de agenter som körs på produkten och deras respektive tjänster som körs på Azure.<br/><br/> Den här appen har inte behörighet att göra Azure Resource Manager-anrop eller Azure RBAC-åtkomst på vilken resurs som helst. | Du behöver [dessa behörigheter](./tutorial-discover-vmware.md#prepare-an-azure-user-account) för att Azure Migrate ska kunna skapa appen.
**Skapa Azure AD-appar – nyckel valv** | Den här appen skapas endast för migrering utan agent för virtuella VMware-datorer till Azure.<br/><br/> Den används enbart för att komma åt nyckel valvet som skapats i användarens prenumeration för migrering utan agent.<br/><br/> Den har Azure RBAC-åtkomst på Azure Key Vault (skapas i kundens klient) när identifieringen initieras från enheten. | Du behöver [dessa behörigheter](./tutorial-discover-vmware.md#prepare-an-azure-user-account) för att Azure Migrate ska kunna skapa appen.



## <a name="collected-data"></a>Insamlade data

Data som samlas in av klienten för alla distributions scenarier sammanfattas i [support matrisen för enheten](migrate-appliance.md).

## <a name="discovery-and-collection-process"></a>Process för identifiering och insamling

![Arkitektur](./media/migrate-appliance-architecture/architecture1.png)

Enheten kommunicerar med vCenter-servrar och Hyper-V-värdar/-kluster med följande process.

1. **Starta identifiering**:
    - När du startar identifieringen på Hyper-V-enheten kommunicerar den med Hyper-V-värdarna på WinRM-port 5985 (HTTP).
    - När du startar identifiering på VMware-enheten kommunicerar den med vCenter-servern på TCP-port 443 som standard. OM vCenter-servern lyssnar på en annan port kan du konfigurera den i installations programmets webbapp.
2. **Samla in metadata och prestanda data**:
    - Enheten använder en Common Information Model-session (CIM) för att samla in virtuella Hyper-V-Datadata från Hyper-V-värden på port 5985.
    - Enheten kommunicerar med port 443 som standard för att samla in virtuella VMware-Datadata från vCenter Server.
3. **Skicka data**: enheten skickar insamlade data till Azure Migrate Server utvärdering och migrering av Azure Migrate Server över SSL-port 443. Enheten kan ansluta till Azure via Internet eller via ExpressRoute (kräver Microsoft-peering).
    - För prestanda data samlar enheten in real tids användnings data.
        - Prestanda data samlas in var 20: e sekund för VMware och var 30: e sekund för Hyper-V för varje prestanda mått.
        - Insamlade data samlas in för att skapa en enda data punkt i 10 minuter.
        - Värdet för högsta användning väljs från alla data punkter med 20/30 sekunder och skickas till beräkningen av Azure för bedömning.
        - Baserat på percentilvärdet som anges i bedömnings egenskaperna (50/nittionde/95/99), sorteras 10-minuters punkterna i stigande ordning och lämpligt percentilvärdet används för att beräkna utvärderingen
    - Vid Server migrering börjar installations programmet samla in VM-data och replikerar dem till Azure.
4. **Utvärdera och migrera**: nu kan du skapa utvärderingar från de metadata som samlas in av installations programmet med hjälp av Azure Migrate Server bedömning. Dessutom kan du börja migrera virtuella VMware-datorer med hjälp av Azure Migrate Server-migrering för att dirigera agent lös VM-replikering.

## <a name="appliance-upgrades"></a>Installations program

Installationen uppgraderas eftersom Azure Migrate agenter som körs på enheten uppdateras. Detta sker automatiskt eftersom automatisk uppdatering är aktiverat på enheten som standard. Du kan ändra den här standardinställningen för att uppdatera agenterna manuellt.

Du inaktiverar automatisk uppdatering i registret genom att ange HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate"-nyckeln till 0 (DWORD).


## <a name="next-steps"></a>Nästa steg

[Granska](migrate-appliance.md) support matrisen för enheten.