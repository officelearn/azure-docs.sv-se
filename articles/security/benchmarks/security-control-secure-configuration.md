---
title: Azure Security Control – säker konfiguration
description: Säker konfiguration av Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 981e13e772ee21e0e4a680e6d07b4ad4892a7cee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417056"
---
# <a name="security-control-secure-configuration"></a>Säkerhetskontroll: Säker konfiguration

Upprätta, implementera och aktivt hantera (spåra, rapportera om, korrigera) säkerhetskonfigurationen av Azure-resurser för att förhindra att angripare utnyttjar sårbara tjänster och inställningar.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.1 | 5.1 | Kund |

Använd Azure Policy-alias för att skapa anpassade principer för granskning eller framtvingning av konfigurationen av dina Azure-resurser. Du kan också använda inbyggda Azure-principdefinitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller/ överskrider säkerhetskraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

- [Så här visar du tillgängliga Azure-principalias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Export med en och flera resurser till en mall i Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Säkerhetsrekommendationer - en referensguide](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.2 | 5.1 | Kund |

Använd Azure Security Center-rekommendationer för att underhålla säkerhetskonfigurationer på alla beräkningsresurser.  Dessutom kan du använda anpassade operativsystemavbildningar eller Azure Automation State-konfiguration för att upprätta säkerhetskonfigurationen för det operativsystem som krävs av din organisation.

- [Övervaka Azure Security Center-rekommendationer](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Säkerhetsrekommendationer - en referensguide](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Konfigurationsöversikt över Azure Automation State](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Ladda upp en virtuell hårddisk och använda den för att skapa nya virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Skapa en Virtuell Linux-dator från en anpassad disk med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.3 | 5.2 | Kund |

Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar över dina Azure-resurser.  Dessutom kan du använda Azure Resource Manager-mallar för att upprätthålla säkerhetskonfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure-principeffekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Skapa och hantera principer för att använda kompatibilitet](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Översikt över Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.4 | 5.2 | Delad |

Följ rekommendationer från Azure Security Center om hur du utför sårbarhetsbedömningar på dina Azure-beräkningsresurser.  Dessutom kan du använda Azure Resource Manager-mallar, anpassade operativsystemavbildningar eller Azure Automation State-konfiguration för att upprätthålla säkerhetskonfigurationen för det operativsystem som krävs av din organisation.   Microsofts mallar för virtuella datorer i kombination med Azure Automations önskade tillståndskonfiguration kan hjälpa till att uppfylla och underhålla säkerhetskraven. 

Observera också att Azure Marketplace Virtual Machine Images som publiceras av Microsoft hanteras och underhålls av Microsoft. 

- [Implementera azure security center sårbarhetsbedömningsrekommendationer](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Skapa en virtuell Azure-dator från en Azure Resource Manager-mall](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Konfigurationsöversikt över Azure Automation State](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Skapa en virtuell Windows-dator i Azure-portalen](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Information om hur du hämtar den virtuella datorn-mallen](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.5 | 5.3 | Kund |

Använd Azure DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager-mallar och skript för önskad tillståndskonfiguration. Om du vill komma åt de resurser du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.6 | 5.3 | Kund |

Om du använder anpassade avbildningar använder du rollbaserad åtkomstkontroll (RBAC) för att säkerställa att endast behöriga användare kan komma åt avbildningarna. Med hjälp av ett delat bildgalleri kan du dela dina bilder med olika användare, tjänsthuvudnamn eller AD-grupper inom organisationen.  För behållaravbildningar lagrar du dem i Azure Container Registry och utnyttjar RBAC för att säkerställa att endast behöriga användare kan komma åt avbildningarna.  

- [Förstå RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Förstå RBAC för containerregistret](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Konfigurera RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Översikt över det delade bildgalleriet](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.7 | 5.4 | Kund |

Definiera och implementera standardsäkerhetskonfigurationer för Azure-resurser med Azure-princip. Använd Azure Policy-alias för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för dina Azure-resurser. Du kan också använda inbyggda principdefinitioner som är relaterade till dina specifika resurser.  Dessutom kan du använda Azure Automation för att distribuera konfigurationsändringar.

- [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Så här använder du alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera konfigurationshanteringsverktyg för operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.8 | 5.4 | Kund |

Azure Automation State Configuration är en konfigurationshanteringstjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala datacenter. Du kan enkelt gå ombord på datorer, tilldela dem deklarativa konfigurationer och visa rapporter som visar varje dators efterlevnad till önskat tillstånd som du angav. 

- [Introduktionsdatorer för hantering av Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.9 | 5.5 | Kund |

Använd Azure Security Center för att utföra baslinjegenomsökningar för dina Azure-resurser.  Dessutom kan du använda Azure Policy för att avisera och granska Azure-resurskonfigurationer.

- [Åtgärda rekommendationer i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.10 | 5.5 | Kund |

Använd Azure Security Center för att utföra baslinjegenomsökningar för OS- och Docker-inställningar för behållare.

- [Förstå rekommendationer för Azure Security Center-container](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.11 | 13.1 | Kund |

Använd Managed Service Identity tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina molnprogram.

- [Så här integrerar du med Azure-hanterade identiteter](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Så här skapar du ett nyckelvalv](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.12 | 4.1 | Kund |

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

- [Konfigurerar hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 7.13 | 18.1, 18.7 | Kund |

Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

- [Konfigurera autentiseringsskanner](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Malware Defense](security-control-malware-defense.md)