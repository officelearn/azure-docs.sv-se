---
title: Azure Migrerarinstallationsarkitektur
description: Ger en översikt över Azure Migrate-installationen som används vid serverutvärdering och migrering.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389026"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrerarinstallationsarkitektur

I den här artikeln beskrivs Azure Migrate-installationarkitektur och processer. Azure Migrate-installationen är en lättinstallation som distribueras lokalt för att identifiera virtuella datorer och fysiska servrar för migrering till Azure. 

## <a name="deployment-scenarios"></a>Distributionsscenarier

Azure Migrate-enheten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för** 
--- | --- | ---
**VMware VM-utvärdering** | Azure Migrera:Server-utvärdering | Upptäck virtuella datorer med VMware.<br/><br/> Upptäck datorappar och beroenden.<br/><br/> Samla in datorns metadata och prestandametadata och skicka till Azure.
**VMware VM-migrering (agentlös)** | Azure Migrera:Servermigrering | Upptäck virtuella datorer med VMware<br/><br/>  Replikera virtuella datorer med VMware med [agentlös migrering](server-migrate-overview.md).
**Bedömning av virtuella datorer med hyper-V** | Azure Migrera:Server-utvärdering | Upptäck virtuella virtuella datorer med hyper-v.<br/><br/> Samla in datorns metadata och prestandametadata och skicka till Azure.
**Fysisk maskin** |  Azure Migrera:Server-utvärdering |  Upptäck fysiska servrar.<br/><br/> Samla in datorns metadata och prestandametadata och skicka till Azure.

## <a name="appliance-components"></a>Komponenter till apparaten

Apparaten har ett antal komponenter.

- **Hanteringsapp:** Det här är en webbapp för användarinmatning under distribution av installationen. Används vid bedömning av datorer för migrering till Azure.
- **Identifieringsagent:** Agenten samlar in maskinkonfigurationsdata. Används vid bedömning av datorer för migrering till Azure. 
- **Bedömningsagent:** Agenten samlar in prestandadata. Används vid bedömning av datorer för migrering till Azure.
- **DRA-agent**: Dirigerar VM-replikering och samordnar kommunikationen mellan replikerade datorer och Azure. Används endast vid replikerande virtuella virtuella datorer med VMware till Azure med hjälp av agentless migrering.
- **Gateway**: Skickar replikerade data till Azure. Används endast vid replikerande virtuella virtuella datorer med VMware till Azure med hjälp av agentless migrering.
- **Automatisk uppdatering:** Uppdaterar apparatens komponenter (körs var 24:e timme).



## <a name="appliance-deployment"></a>Distribution av apparater

- Azure Migrate-installationen kan konfigureras med hjälp av en mall för [Hyper-V](how-to-set-up-appliance-hyper-v.md) eller [VMware](how-to-set-up-appliance-vmware.md) eller med hjälp av ett PowerShell-skriptinstallationsprogram för [VMware/Hyper-V](deploy-appliance-script.md)och för [fysiska servrar](how-to-set-up-appliance-physical.md). 
- Installationskrav och driftsättningsförutsättningar sammanfattas i [stödmatrisen för apparater](migrate-appliance.md).


## <a name="appliance-registration"></a>Registrering av apparater

Under installationen av installationen av installationen registrerar du installationen med Azure Migrate och de åtgärder som sammanfattas i tabellen inträffar.

**Åtgärd** | **Detaljer** | **Behörigheter**
--- | --- | ---
**Registrera källleverantörer** | Dessa resursleverantörer registreras i den prenumeration du väljer under installationen av installationen av installationen av installationen av installationen av installationen: Microsoft.OffAzure, Microsoft.Migrate och Microsoft.KeyVault.<br/><br/> Om du registrerar en resursleverantör konfigureras prenumerationen så att den fungerar med resursleverantören. | Om du vill registrera resursleverantörerna behöver du en deltagar- eller ägarroll i prenumerationen.
**Skapa Azure AD-app-kommunikation** | Azure Migrate skapar en Azure Active Directory-app (Azure AD) för kommunikation (autentisering och auktorisering) mellan agenter som körs på installationen och deras respektive tjänster som körs på Azure.<br/><br/> Den här appen har inte behörighet att ringa Azure Resource Manager-anrop eller RBAC-åtkomst på någon resurs. | Du behöver [dessa behörigheter](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) för Azure Migrate för att skapa appen.
**Skapa Azure AD-appar-Nyckelvalv** | Den här appen skapas endast för agentlös migrering av virtuella datorer med VMware till Azure.<br/><br/> Den används uteslutande för att komma åt nyckelvalvet som skapats i användarens prenumeration för agentlös migrering.<br/><br/> Den har RBAC-åtkomst på Azure-nyckelvalvet (skapad i kundens klientorganisation), när identifiering initieras från installationen. | Du behöver [dessa behörigheter](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) för Azure Migrate för att skapa appen.



## <a name="collected-data"></a>Insamlade data

Data som samlas in av klienten för alla distributionsscenarier sammanfattas i [matrisen för stöd för apparat .](migrate-appliance.md)

## <a name="discovery-and-collection-process"></a>Identifierings- och insamlingsprocess

![Arkitektur](./media/migrate-appliance-architecture/architecture.png)

Installationen kommunicerar med vCenter-servrar och Hyper-V-värdar/-kluster med hjälp av följande process.

1. **Starta identifiering:**
    - När du startar identifieringen på Hyper-V-enheten kommunicerar den med Hyper-V-värdarna på WinRM-portarna 5985 (HTTP) och 5986 (HTTPS).
    - När du startar identifieringen på VMware-enheten kommunicerar den som standard med vCenter-servern på TCP-port 443. Om vCenter-servern lyssnar på en annan port kan du konfigurera den i appen för installationens webb.
2. **Samla in metadata och prestandadata:**
    - Installationen använder en CIM-session (Common Information Model) för att samla in Hyper-V-VM-data från Hyper-V-värden på portarna 5985 och 5986.
    - Installationen kommunicerar med port 443 som standard för att samla in VMware VM-data från vCenter Server.
3. **Skicka data:** Installationen skickar insamlade data till Azure Migrate Server Assessment och Azure Migrate Server Migration över SSL-port 443. Installationen kan ansluta till Azure via internet eller använda ExpressRoute med offentlig/Microsoft-peering.
    - För prestandadata samlar apparaten in realtidsdata.
        - Prestandadata samlas in var 20:e sekund för VMware och var 30:e sekund för Hyper-V för varje prestandamått.
        - De insamlade datan samlas in för att skapa en enda datapunkt i 10 minuter.
        - Det högsta utnyttjandevärdet väljs från alla 20/30-sekundersdatapunkter och skickas till Azure för bedömningsberäkning.
        - Baserat på det percentilvärde som anges i bedömningsegenskaperna (50th/90th/95th/99th) sorteras tiominuterpunkterna i stigande ordning och lämpligt percentilvärde används för att beräkna bedömningen
    - För servermigrering börjar installationen av VM-data och replikerar den till Azure.
4. **Utvärdera och migrera:** Du kan nu skapa utvärderingar från metadata som samlas in av enheten med Hjälp av Azure Migrate Server Assessment. Dessutom kan du också börja migrera virtuella virtuella datorer med VMware med Azure Migrate Server-migrering till orkestrera agentlös VM-replikering.





## <a name="appliance-upgrades"></a>Uppgraderingar av apparater

Installationen uppgraderas när Azure Migrate-agenter som körs på installationen uppdateras. Detta sker automatiskt eftersom automatisk uppdatering är aktiverad på apparaten som standard. Du kan ändra den här standardinställningen för att uppdatera agenterna manuellt.

Du inaktiverar automatisk uppdatering i registret genom att ange HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate"-tangenten till 0 (DWORD).

 

## <a name="next-steps"></a>Nästa steg

[Granska](migrate-appliance.md) stödmatrisen för apparaten.

