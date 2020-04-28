---
title: Azure säkerhets kontroll – säker konfiguration
description: Säker konfiguration av Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e3d43239dabe75bc2b25319945c2c6b08d726d2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193113"
---
# <a name="security-control-secure-configuration"></a>Säkerhets kontroll: säker konfiguration

Upprätta, implementera och aktivt hantera (spåra, rapportera om, korrigera) säkerhets konfigurationen för Azure-resurser för att förhindra att angripare utnyttjar sårbara tjänster och inställningar.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.1 | 5.1 | Kund |

Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen för dina Azure-resurser. Du kan också använda inbyggda Azure Policy definitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Exportera en och flera resurser till en mall i Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Säkerhets rekommendationer – en referens guide](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.2 | 5.1 | Kund |

Använd Azure Security Center rekommendationer för att underhålla säkerhetskonfigurationer för alla beräknings resurser.  Dessutom kan du använda anpassade operativ system avbildningar eller Azure Automation tillstånds konfiguration för att upprätta säkerhets konfigurationen för det operativ system som krävs av din organisation.

- [Så här övervakar du Azure Security Center rekommendationer](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Säkerhets rekommendationer – en referens guide](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Översikt över Azure Automation tillstånds konfiguration](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Ladda upp en virtuell hård disk och Använd den för att skapa nya virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.3 | 5.2 | Kund |

Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.  Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Skapa och hantera principer för att använda kompatibilitet](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Översikt över Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.4 | 5.2 | Delad |

Följ rekommendationerna från Azure Security Center om att utföra sårbarhets bedömningar på dina Azure Compute-resurser.  Dessutom kan du använda Azure Resource Manager mallar, anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration för att underhålla säkerhets konfigurationen för det operativ system som krävs av din organisation.   Mallarna för Microsoft Virtual Machines tillsammans med Azure Automation önskad tillstånds konfiguration kan under lätta möten och upprätthålla säkerhets kraven. 

Observera också att avbildningar av virtuella Azure Marketplace-datorer som publiceras av Microsoft hanteras och underhålls av Microsoft. 

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Så här skapar du en virtuell Azure-dator från en Azure Resource Manager-mall](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Översikt över Azure Automation tillstånds konfiguration](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Skapa en virtuell Windows-dator i Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Information om hur du hämtar mallen för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Exempelskript för att överföra en virtuell hårddisk till Azure och skapa en ny virtuell dator](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7.5 | 5.3 | Kund |

Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,6 | 5.3 | Kund |

Om du använder anpassade avbildningar använder du rollbaserad åtkomst kontroll (RBAC) för att se till att endast behöriga användare får åtkomst till avbildningarna. Med hjälp av ett delat bild galleri kan du dela dina avbildningar till olika användare, tjänst huvud namn eller AD-grupper i din organisation.  För behållar avbildningar lagrar du dem i Azure Container Registry och använder RBAC för att se till att endast behöriga användare får åtkomst till avbildningarna.  

- [Förstå RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Förstå RBAC för Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Konfigurera RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Översikt över delade avbildnings Galleri](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,7 | 5.4 | Kund |

Definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure Policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser.  Dessutom kan du använda Azure Automation för att distribuera konfigurations ändringar.

- [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Använda alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,8 | 5.4 | Kund |

Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett. 

- [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,9 | 5,5 | Kund |

Använd Azure Security Center för att utföra bas linje genomsökningar för dina Azure-resurser.  Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,10 | 5,5 | Kund |

Använd Azure Security Center för att utföra bas linje genomsökningar för OS-och Docker-inställningar för behållare.

- [Förstå rekommendationer för Azure Security Center-container](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,11 | 13,1 | Kund |

Använd Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

- [Integrera med Azure Managed Identities](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Så här skapar du en Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Så här ger Key Vault autentisering med en hanterad identitet](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,12 | 4.1 | Kund |

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Konfigurera hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 7,13 | 18,1, 18,7 | Kund |

Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: skydd [mot skadlig kod](security-control-malware-defense.md)