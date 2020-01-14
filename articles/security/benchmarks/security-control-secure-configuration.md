---
title: Azure säkerhets kontroll – säker konfiguration
description: Säker konfiguration av säkerhets kontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934298"
---
# <a name="security-control-secure-configuration"></a>Säkerhets kontroll: säker konfiguration

Upprätta, implementera och aktivt hantera (spåra, rapportera om, korrigera) säkerhets konfigurationen för Azure-resurser för att förhindra att angripare utnyttjar sårbara tjänster och inställningar.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.1 | 5.1 | Kund |

Använd Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.2 | 5.1 | Kund |

Använd Azure Security Center rekommendation &quot;reparera sårbarheter i säkerhetskonfigurationer på din Virtual Machines&quot; för att underhålla säkerhetskonfigurationer för alla beräknings resurser.

Så här övervakar du Azure Security Center rekommendationer:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Så här åtgärdar du Azure Security Center rekommendationer:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.3 | 5.2 | Kund |

Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.4 | 5.2 | Delad |

Grundläggande operativ Systems avbildningar hanteras och underhålls av Microsoft.

Du kan dock tillämpa säkerhets inställningar som krävs av din organisation med hjälp av Azure Resource Manager mallar och/eller önskad tillstånds konfiguration.

Så här skapar du en virtuell Azure-dator från en Azure Resource Manager-mall:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Förstå önskad tillstånds konfiguration för Azure Virtual Machines:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.5 | 5.3 | Kund |

Om du använder anpassade definitioner för Azure-principer använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation om Azure databaser:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.6 | 5.3 | Kund |

Om du använder anpassade avbildningar använder du RBAC för att se till att endast behöriga användare får åtkomst till avbildningarna. För behållar avbildningar lagrar du dem i Azure Container Registry och använder RBAC för att se till att endast behöriga användare får åtkomst till avbildningarna.

Förstå RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Förstå RBAC för Container Registry:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Konfigurera RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.7 | 5.4 | Kund |

Använd Azure Policy för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.8 | 5.4 | Kund |

Använd Azure Compute-tillägg som PowerShell Desired State Configuration för Windows Compute eller Linux kock-tillägget för Linux.

Så här installerar du tillägg för virtuella datorer i Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.9 | 5.5 | Kund |

Använd Azure Security Center för att utföra bas linje genomsökningar för dina Azure-resurser

Så här åtgärdar du rekommendationer i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.1 | 5.5 | Kund |

Använd Azure Security Center för att utföra bas linje genomsökningar för OS-och Docker-inställningar för behållare.

Förstå rekommendationer för Azure Security Center container:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt 

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7.11 | 13,1 | Kund |

Använd Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

Så här integrerar du med Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här ger Key Vault autentisering med en hanterad identitet:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7,12 | 4.1 | Kund |

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

Så här konfigurerar du hanterade identiteter:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 7,13 | 13,3 | Kund |

Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

Konfigurera inloggnings skannern:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Nästa steg

Se nästa säkerhets kontroll: skydd [mot skadlig kod](security-control-malware-defense.md)
