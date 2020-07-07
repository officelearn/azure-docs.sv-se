---
title: Koncept – identitet och åtkomst
description: Lär dig mer om identitets-och åtkomst koncepten i Azure VMware-lösningen (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ce85b60b38db86af14b9e9aa06e568436dc76658
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82740959"
---
# <a name="azure-vmware-solution-avs-identity-concepts"></a>Identitets koncept för Azure VMware-lösning (AVS)

En vCenter-Server och NSX-T-hanterare tillhandahålls när ett privat moln distribueras. Du använder vCenter för att hantera arbets belastningar för virtuella datorer och NSX-T-hanterare för att utöka det privata molnets program varu definitions nätverk.

Åtkomst-och identitets hantering använder CloudAdmin grupp behörigheter för vCenter-och begränsade administratörs rättigheter för NSX-T Manager. Den här principen säkerställer att din privata moln plattform kan uppgraderas automatiskt. Detta levererar de senaste funktionerna och korrigeringarna regelbundet. Mer information om uppgraderingar av privata moln finns i [artikeln om uppgraderingar av privata moln][concepts-upgrades] .

## <a name="vcenter-access-and-identity"></a>vCenter-åtkomst och identitet

Behörigheter i vCenter tillhandahålls genom CloudAdmin-gruppen. Gruppen kan hanteras lokalt i vCenter eller via integrering av enkel inloggning med vCenter LDAP med Azure Active Directory. Du får möjlighet att aktivera integreringen när du har distribuerat ett privat moln.

Behörigheterna CloudAdmin och CloudGlobalAdmin visas i tabellen nedan.

|  Privilegie rad uppsättning           | CloudAdmin | CloudGlobalAdmin | Kommentar |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Larm                  | En CloudAdmin-användare har alla larm privilegier för larm i beräknings-ResourcePool och virtuella datorer.     |          --        |  -- |
|  Automatisk distribution             |  --  |        --        |  Microsoft hanterar värd hantering.  |
|  Certifikat            |  --  |        --       |  Microsoft certifikat hantering.  |
|  Innehållsbibliotek         | En CloudAdmin-användare har behörighet att skapa och använda filer i ett innehålls bibliotek.    |         Aktive rad med SSO.         |  Microsoft distribuerar filer i innehålls biblioteket till ESXi-värdar.  |
|  Datacenter              |  --  |        --          |  Microsoft utför alla data Center åtgärder.  |
|  Datalager               | Data lager. AllocateSpace, data lager. browse, Datastore.Config, data lager. DeleteFile, data lager. FileManagement, data lager. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX Agent Manager       |  --  |         --       |  Microsoft utför alla åtgärder.  |
|  Mapp                  |  En CloudAdmin-användare har alla mappbehörigheter.     |  --  |  --  |
|  Global                  |  Global. CancelTask, global. GlobalTag, global. Health, global. LogEvent, global. ManageCustomFields, global. ServiceManagers, global. SetCustomField, Global.SystemTag         |                  |    |
|  Värd                    |  Host. HBR. HbrManagement      |        --          |  Microsoft utför alla andra värd åtgärder.  |
|  InventoryService        |  InventoryService. taggning      |        --          |  --  |
|  Nätverk                 |  Nätverk. tilldela    |                  |  Microsoft utför alla andra nätverks åtgärder.  |
|  Behörigheter             |  --  |        --       |  Microsoft gör alla behörigheter-åtgärder.  |
|  Profil driven lagring  |  --  |        --       |  Microsoft utför alla profil åtgärder.  |
|  Resurs                |  En CloudAdmin-användare har alla resurs behörigheter.        |      --       | --   |
|  Schemalagd aktivitet          |  En CloudAdmin-användare har alla ScheduleTask-behörigheter.   |   --   | -- |
|  Sessioner                |  Sessions. GlobalMessage, sessions. ValidateSession      |   --   |  Microsoft gör alla andra åtgärder i sessioner.  |
|  Lagringspooler           |  StorageViews. View   |        --          |  Microsoft gör alla andra lagrings visnings åtgärder (konfigurera tjänsten).  |
|  Aktiviteter                   |  --  |  --   |  Microsoft hanterar tillägg som hanterar aktiviteter.  |
|  vApp                    |  En CloudAdmin-användare har alla vApp-behörigheter.  |  --  |  --  |
|  Virtuell dator         |  En CloudAdmin-användare har alla VirtualMachine-behörigheter.  |  --  |  --  |
|  vService                |  En CloudAdmin-användare har alla vService-behörigheter.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T-hanterarens åtkomst och identitet

Du kommer åt NSX-T-hanteraren med hjälp av kontot "administratör". Det kontot har fullständig behörighet och gör att du kan skapa och hantera T1-routrar, logiska växlar och alla tjänster. De fullständiga privilegierna i NSX-T ger också till gång till NSX-T t0-routern. En ändring i t0-routern kan resultera i försämrade nätverks prestanda eller förlust av åtkomst till ett privat moln. För att uppfylla support kraven måste du öppna en supportbegäran i Azure Portal för att begära ändringar i din NSX-T t0-router.
  
## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig om [metoder för uppgradering av privata moln][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md