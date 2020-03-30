---
title: Översikt över Azure Kubernetes-tjänsten (AKS)
description: Lär dig mer om självdiagnostiserande kluster i Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126606"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Översikt över Azure Kubernetes Service Diagnostics (förhandsversion)

Felsökning av Ak Kubernetes Service (AKS) klusterproblem är en viktig del av att underhålla klustret, särskilt om klustret kör verksamhetskritiska arbetsbelastningar. AKS Diagnostics är en intelligent, självdiagnostisk upplevelse som hjälper dig att identifiera och lösa problem i klustret. AKS Diagnostics är molnbaserad och du kan använda den utan extra konfigurations- eller faktureringskostnad.

Den här funktionen är nu i offentlig förhandsversion.

## <a name="open-aks-diagnostics"></a>Öppna AKS-diagnostik

Så här kommer du åt AKS-diagnostik:

- Navigera till kubernetes-klustret i [Azure-portalen](https://portal.azure.com).
- Klicka på **Diagnostisera och lösa problem** i vänster navigering, som öppnar AKS Diagnostics.
- Välj en kategori som bäst beskriver problemet med klustret med hjälp av nyckelorden på startpanelen, eller skriv ett nyckelord som bäst beskriver problemet i sökfältet, till exempel Problem med _klusternod_.

![Startsida](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Visa en diagnostikrapport

När du har klickat på en kategori kan du visa en diagnostikrapport som är specifik för klustret. Diagnostikrapporten ropar på ett intelligent sätt om det finns några problem i klustret med statusikoner. Du kan öka detaljnivån för varje ämne genom att klicka på **Mer information** för att se detaljerad beskrivning av problemet, rekommenderade åtgärder, länkar till användbara dokument, relaterade mått och loggningsdata. Diagnostikrapporter genereras intelligent baserat på det aktuella tillståndet för klustret efter att ha kört en mängd kontroller. Diagnostikrapporter kan vara ett användbart verktyg för att identifiera problemet med klustret och hitta nästa steg för att lösa problemet.

![Diagnostikrapport](./media/concepts-diagnostics/diagnostic-report.png)

![Utökad diagnostikrapport](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Klusterinsikter

Följande diagnostiska kontroller är tillgängliga i **Cluster Insights**.

### <a name="cluster-node-issues"></a>Problem med klusternod

Klusternodproblem söker efter nodrelaterade problem som kan orsaka att klustret beter sig oväntat.

- Problem med nodberedskap
- Nodfel
- Otillräckliga resurser
- Nod saknas IP-konfiguration
- CNI-nodfel
- Noden hittades inte
- Nod avstängd
- Nodautentiseringsfel
- Nod kube-proxy inaktuella

### <a name="create-read-update--delete-operations"></a>Skapa, läsa, uppdatera & borttagningsåtgärder

CRUD-åtgärder söker efter crud-åtgärder som kan orsaka problem i klustret.

- Åtgärdsfel för borttagning av undernät
- Åtgärdsfel för borttagning av nätverkssäkerhetsgrupp
- Åtgärdsfel för borttagning av vägtabell vid användning
- Fel vid refererad resursetablering
- Åtgärdsfel för borttagning av offentlig IP-adress
- Distributionsfel på grund av distributionskvot
- Åtgärdsfel på grund av organisationsprincip
- Registrering av prenumeration saknas
- Etableringsfel för VM-tillägg
- Kapacitet för undernät
- Kvoten överskred felet

### <a name="identity-and-security-management"></a>Identitets- och säkerhetshantering

Identitets- och säkerhetshantering identifierar autentiserings- och auktoriseringsfel som kan förhindra kommunikation till klustret.

- Nodauktoriseringsfel
- 401 fel
- 403-fel

## <a name="next-steps"></a>Nästa steg

Samla in loggar som hjälper dig att felsöka klusterproblemen ytterligare med hjälp av [AKS Periscope](https://aka.ms/aksperiscope).

Skicka dina frågor eller feedback på [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) genom att lägga till "[Diag]" i titeln.
