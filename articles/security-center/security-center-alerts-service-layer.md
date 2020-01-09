---
title: Hot identifiering i Azure Service Layer – Azure Security Center
description: I det här avsnittet presenteras Azure Service Layer-aviseringar som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665698"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Hot identifiering för Azure Service Layer i Azure Security Center

I det här avsnittet presenteras Azure Security Centers aviseringar som är tillgängliga när du övervakar följande Azure Service-lager:

* [Azure nätverks lager](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (för hands version)](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Azure nätverks lager<a name="network-layer"></a>

Security Center Network Layer Analytics baseras på [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paket rubriker som samlas in av Azure Core-routrar. Baserat på den här datafeeden använder Security Center maskin inlärnings modeller för att identifiera och flagga skadliga trafik aktiviteter. Security Center använder också Microsoft Threat Intelligence-databasen för att utöka IP-adresser.

Vissa nätverkskonfigurationer kan begränsa Security Center från att generera aviseringar om misstänkt nätverks aktivitet. För att Security Center generera nätverks aviseringar, se till att:

- Den virtuella datorn har en offentlig IP-adress (eller finns på en belastningsutjämnare med en offentlig IP-adress).

- Den virtuella datorns utgående trafik blockeras inte av en extern ID-lösning.

- Den virtuella datorn har tilldelats samma IP-adress för hela timmen då den misstänkta kommunikationen uppstod. Detta gäller även för virtuella datorer som skapats som en del av en hanterad tjänst (t. ex. AKS, Databricks).

En lista över aviseringar för Azure nätverks lager finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurenetlayer).

Mer information om hur Security Center kan använda nätverksrelaterade signaler för att använda hot skydd finns [i heuristisk DNS-identifieringar i Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Azure Management Layer (Azure Resource Manager) (för hands version)<a name ="management-layer"></a>

Security Center skydds skikt baserat på Azure Resource Manager är för närvarande en för hands version.

Security Center erbjuder ett extra skydds lager genom att använda Azure Resource Manager händelser, som anses vara kontroll planet för Azure. Genom att analysera Azure Resource Manager poster Security Center identifiera ovanliga eller potentiellt skadliga åtgärder i Azure-prenumerationens miljö.

En lista över aviseringar om Azure Resource Manager (förhands granskning) finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Flera av föregående analyser drivs av Microsoft Cloud App Security. Om du vill dra nytta av dessa analyser måste du aktivera en Cloud App Security-licens. Om du har en Cloud App Security licens är de här aviseringarna aktiverade som standard. Så här inaktiverar du dem:
>
> 1. På bladet **Security Center** väljer du **säkerhets princip**. Välj **Redigera inställningar**för den prenumeration som du vill ändra.
> 2. Välj **hot identifiering**.
> 3. Under **Aktivera integreringar**, rensa **Tillåt Microsoft Cloud App Security åtkomst till mina data**och välj **Spara**.

>[!NOTE]
>Security Center lagrar säkerhetsrelaterad kund information i samma geo som dess resurs. Om Microsoft ännu inte har distribuerat Security Center i resursens geografiska område, lagrar data i USA. När Cloud App Security är aktive rad lagras informationen i enlighet med reglerna för geo-platsen för Cloud App Security. Mer information finns i [data lagring för icke-regionala tjänster](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault (för hands version)<a name="azure-keyvault"></a>

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

Azure Security Center innehåller Azure-inbyggt Avancerat skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. Security Center identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Det här skydds lagret gör att du kan åtgärda hot utan att vara säkerhets expert och utan att behöva hantera säkerhets övervaknings system från tredje part.  

När avvikande aktiviteter inträffar kan Security Center Visa aviseringar och skicka dem via e-post till prenumerations administratörer. Dessa aviseringar innehåller information om den misstänkta aktiviteten och rekommendationer om hur du undersöker och åtgärdar hot. 

> [!NOTE]
> Den här tjänsten är för närvarande inte tillgänglig i moln regioner i Azure myndigheter och suveräna.

En lista över Azure Key Vault aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurekv).
