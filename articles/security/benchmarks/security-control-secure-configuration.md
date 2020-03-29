---
title: Azure Security Control – säker konfiguration
description: Säker konfiguration för säkerhetskontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934298"
---
# <a name="security-control-secure-configuration"></a>Säkerhetskontroll: Säker konfiguration

Upprätta, implementera och aktivt hantera (spåra, rapportera om, korrigera) säkerhetskonfigurationen av Azure-resurser för att förhindra att angripare utnyttjar sårbara tjänster och inställningar.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.1 | 5.1 | Kund |

Använd Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.2 | 5.1 | Kund |

Använd Azure Security &quot;Center-rekommendationen Åtgärda säkerhetsproblem i&quot; säkerhetskonfigurationer på dina virtuella datorer för att underhålla säkerhetskonfigurationer på alla beräkningsresurser.

Så här övervakar du Rekommendationerna för Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Åtgärda Azure Security Center-rekommendationer:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.3 | 5.2 | Kund |

Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar över dina Azure-resurser.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure-principeffekter:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.4 | 5.2 | Delad |

Grundläggande operativsystemavbildningar hanteras och underhålls av Microsoft.

Du kan dock använda säkerhetsinställningar som krävs av din organisation med Hjälp av Azure Resource Manager-mallar och/eller konfiguration av önskat tillstånd.

Så här skapar du en virtuell Azure-dator från en Azure Resource Manager-mall:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Förstå önskad tillståndskonfiguration för virtuella Azure-datorer:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.5 | 5.3 | Kund |

Om du använder anpassade Azure-principdefinitioner kan du använda Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation för Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.6 | 5.3 | Kund |

Om du använder anpassade avbildningar använder du RBAC för att säkerställa att endast behöriga användare kan komma åt avbildningarna. För behållaravbildningar lagrar du dem i Azure Container Registry och utnyttjar RBAC för att säkerställa att endast behöriga användare kan komma åt avbildningarna.

Förstå RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Förstå RBAC för containerregistret:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Konfigurera RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.7 | 5.4 | Kund |

Använd Azure Policy för att avisera, granska och tillämpa systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.8 | 5.4 | Kund |

Använd Azure-beräkningstillägg som PowerShell Desired State Configuration för Windows-beräkning eller Linux Chef-tillägg för Linux.

Så här installerar du tillägg för virtuella datorer i Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.9 | 5.5 | Kund |

Använd Azure Security Center för att utföra baslinjegenomsökningar för dina Azure-resurser

Åtgärda rekommendationer i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.1 | 5.5 | Kund |

Använd Azure Security Center för att utföra baslinjegenomsökningar för OS- och Docker-inställningar för behållare.

Förstå azure security center-behållarrekommendationer:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt 

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.11 | 13.1 | Kund |

Använd Managed Service Identity tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina molnprogram.

Så här integrerar du med Azure-hanterade identiteter:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.12 | 4.1 | Kund |

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

Konfigurerar hanterade identiteter:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.13 | 13.3 | Kund |

Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

Så här konfigurerar du autentiseringsskanner:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhetskontroll: [Malware Defense](security-control-malware-defense.md)
